# Methodology - ADL Event Analysis

**Analysis**: Hyperliquid ADL Event  
**Date**: October 10, 2025, 21:15:00 - 21:27:00 UTC  
**Version**: V2 (Corrected)

---

## Data Sources

### 1. Trade Fills
**Source**: `s3://hl-mainnet-node-data/node_fills_by_block/hourly/20251010/21.lz4`

**Format**: LZ4-compressed JSON Lines
- Each line is a block containing fills
- Includes: user, coin, price, size, side, direction, closed_pnl
- **Critical field**: `direction` tells us what happened (e.g., "Liquidated Cross Long")

**Extracted**:
- BTC: 160,956 fills
- SOL: 44,020 fills
- Total: 204,976 fills

### 2. Mark Prices
**Source**: `s3://hyperliquid-archive/asset_ctxs/20251010.csv.lz4`

**Format**: LZ4-compressed CSV
- Contains official Hyperliquid mark prices
- Used for unrealized PNL calculation
- 1-minute snapshots

**Final Prices** (21:27 UTC):
- BTC: $108,340.00
- SOL: $169.36

### 3. What We DON'T Have
- Clearinghouse state snapshots
- Account-level data (total collateral, account value)
- Historical leverage ratios
- Cross-margin account balances

---

## Position Reconstruction

### Overview
We reconstruct positions by replaying all fills for each user-coin pair.

### Key Logic: Determining Position Side

**For Liquidations** (CRITICAL FIX):
```
If direction contains "Liquidated Cross Long":
  → Position WAS: LONG
  → Action: Sell (side='A') to close the long
  → Record: LONG position with loss

If direction contains "Liquidated Cross Short":
  → Position WAS: SHORT  
  → Action: Buy (side='B') to close the short
  → Record: SHORT position with loss
```

**Previous Bug**: Used `side` field, which is the closing ACTION, not the position.

**Fix**: Parse `direction` field to determine original position side.

### For Regular Trades
```
side='B' (Buy):
  - Opening long or closing short
  - Increases position size (positive direction)

side='A' (Ask/Sell):
  - Opening short or closing long
  - Decreases position size (negative direction)
```

### Position Size Tracking
- Start: position_size = 0
- For each fill: position_size += size_change
- For liquidations: position_size = 0 (fully closed)
- Final: If position_size > 0 → LONG, if < 0 → SHORT

---

## PNL Calculation

### Realized PNL
```
Sum of `closed_pnl` from all fills
```
- Directly from blockchain data
- Represents actual profit/loss from closed trades
- **Most reliable metric**

### Unrealized PNL

**For Open Positions**:
```
If LONG: (mark_price - entry_price) * position_size
If SHORT: (entry_price - mark_price) * position_size
```

**For Liquidated Positions**:
```
Unrealized PNL = 0
```
- Liquidated positions are fully closed
- Only realized PNL applies

### Absolute PNL
```
Absolute PNL = Realized PNL + Unrealized PNL
```

### % PNL
```
% PNL = (Absolute PNL / Position Value) × 100
Position Value = position_size × mark_price
```

---

## Entry Price Calculation

### For Open Positions
**Weighted Average**:
```
entry_price = sum(fill_price × fill_size) / total_position_size
```

### For Liquidated Positions
**Approximation**:
- Use `start_position` from liquidation fill
- Entry price from liquidation fill price (approximate)

---

## Liquidation Handling

### Detection
Positions are marked as liquidated if ANY fill has:
```
direction contains "Liquidated"
```

Types detected:
- Liquidated Cross Long
- Liquidated Cross Short
- Liquidated Isolated Long
- Liquidated Isolated Short

### Special Handling
1. **Original Side Preservation**: Record side BEFORE liquidation
2. **Zero Unrealized PNL**: Liquidated = fully closed
3. **Position Size**: Use `start_position` from liquidation fill

---

## Limitations

### What We CAN Calculate (Accurate)
✅ Position side (LONG/SHORT)  
✅ Position size  
✅ Entry price (weighted average)  
✅ Realized PNL  
✅ Unrealized PNL (for open positions)  
✅ Liquidation status  

### What We CANNOT Calculate (Missing Data)
❌ **Leverage Ratio**: Requires `accountValue` and `totalNtlPos`  
❌ **Negative Equity**: Requires `totalRawUsd` (account cash)  
❌ **Account-Level Metrics**: Requires clearinghouse state  
❌ **Cross-Margin Impact**: Requires account-level data  

### Why These Are Missing
- S3 fills data is **trade-level**, not **account-level**
- Clearinghouse state snapshots not available in public S3
- Historical account data not accessible via API
- Would require direct database access or historical state snapshots

---

## Verification

### Method
Compare our results against Hyperliquid explorers:
- https://app.hyperliquid.xyz/explorer
- https://hyperdash.info

### Example Verification
**Address**: `0x1a67ea21ba0f895560590147203d08a832054055`

| Field | Our Data | Explorer | Match |
|-------|----------|----------|-------|
| Asset | SOL | SOL | ✅ |
| Side | LONG | LONG | ✅ |
| PNL | -$6,846,713.44 | -$6,846,713.44 | ✅ |
| Liquidated | True | True | ✅ |

### Discrepancy Investigation
If explorer data doesn't match:
1. Check if address is correct (no typos)
2. Verify date/time window
3. Check if explorer uses different PNL calculation
4. Confirm explorer shows same event window

---

## Data Quality Assessment

### High Confidence (Use These)
- Position sides: ✅ Verified against explorer
- Realized PNL: ✅ Matches explorer
- Liquidation status: ✅ From blockchain data
- Trade prices: ✅ From S3 fills
- Timestamps: ✅ Nanosecond precision

### Medium Confidence (Calculated)
- Entry prices: ⚠️ Weighted average approximation
- Unrealized PNL: ⚠️ Based on mark prices at end of window
- Position sizes: ⚠️ Reconstructed from fills

### Not Available (Missing Data Source)
- Leverage: ❌ Requires clearinghouse state
- Negative equity: ❌ Requires account data
- Account balances: ❌ Not in S3 data

---

## Code Implementation

### Position Reconstruction Algorithm
```python
for (user, coin), fills in group_by([user, coin]):
    position_size = 0
    was_liquidated = False
    original_side = None
    
    for fill in fills:
        if "Liquidated" in fill.direction:
            was_liquidated = True
            if "Long" in fill.direction:
                original_side = "LONG"
            elif "Short" in fill.direction:
                original_side = "SHORT"
            position_size = 0  # Fully closed
        else:
            # Regular trade
            if fill.side == 'B':
                position_size += fill.size
            else:
                position_size -= fill.size
    
    # Determine final side
    if was_liquidated:
        final_side = original_side
    elif position_size > 0:
        final_side = "LONG"
    else:
        final_side = "SHORT"
```

### PNL Calculation
```python
realized_pnl = sum(fill.closed_pnl for fill in fills)

if was_liquidated:
    unrealized_pnl = 0
elif final_side == "LONG":
    unrealized_pnl = (mark_price - entry_price) * position_size
else:
    unrealized_pnl = (entry_price - mark_price) * position_size

absolute_pnl = realized_pnl + unrealized_pnl
```

---

## Changes from Previous Version

### Bug Fixed
**Issue**: Position sides incorrect for liquidations

**Root Cause**: Used `side` field (closing action) instead of `direction` field (position type)

**Impact**: 
- ALL liquidated positions had wrong side
- 10,982 liquidation fills affected
- Top winners/losers list was incorrect

**Fix**: Parse `direction` field to determine original position side

**Verification**: Multiple addresses checked against explorer ✅

---

## Academic Research Notes

### Suitable For
✅ Position-level PNL analysis  
✅ Liquidation event studies  
✅ Price impact analysis  
✅ Trading behavior analysis  
✅ Market microstructure  

### NOT Suitable For
❌ Leverage analysis (data unavailable)  
❌ Account-level risk metrics (data unavailable)  
❌ Cross-margin studies (data unavailable)  
❌ Capital efficiency analysis (data unavailable)  

### Citation Recommendation
```
Data Source: Hyperliquid S3 buckets (official blockchain data)
Method: Position reconstruction from trade fills
Limitation: Account-level metrics unavailable
Verification: Spot-checked against Hyperliquid explorer
```

---

## Version History

**V1 (INCORRECT)**: 
- Used `side` field for position direction
- Liquidations had wrong sides
- ❌ Do not use

**V2 (CORRECTED)**:
- Uses `direction` field for position direction
- Correct liquidation handling
- Verified against explorer
- ✅ Use this version

---

**Document Version**: 1.0  
**Last Updated**: November 6, 2025  
**Status**: Verified & Approved for Research

