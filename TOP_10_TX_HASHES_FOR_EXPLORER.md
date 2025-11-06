# Top 10 Winners - Transaction Hashes for Entry Price Lookup

**Purpose**: Use these transaction hashes to find entry prices on Hyperliquid Explorer  
**Why**: Most positions were opened >24 hours before the ADL event

---

## 🔍 How to Find Entry Prices

### Method 1: Hyperliquid Explorer
1. Go to: https://app.hyperliquid.xyz/explorer
2. Search for the address
3. Filter transactions to October (or earlier if needed)
4. Look for the first "Open Short" transaction
5. That's the entry price!

### Method 2: HyperDash (Easier!)
1. Go to: https://hyperdash.info/trader/{ADDRESS}
2. View full trading history
3. Find when they opened the short position
4. Entry price is shown directly

---

## 1. **$79.7M Profit** - BTC SHORT

**Address**: `0xb317d2bc2d3d2df5fa441b5bae0ab9d8b07283ae`

**Earliest Transactions in Our Window** (21:15-21:27 UTC):
- All are "Auto-Deleveraging" or "Close Short"
- ⚠️ **Position opened BEFORE 21:00 UTC**

**Sample ADL Transaction**:
```
0x784fd33f92195e9079c9042d3a636a010f00eb252d1c7d621c187e92511d387b
Time: 2025-10-10 21:17:06 UTC
Direction: Auto-Deleveraging
Size: 114.78 BTC @ $108,416
```

**To find entry**: Search address on explorer, look for earliest "Open Short" BTC transaction

---

## 2. **$10.6M Profit** - SOL SHORT

**Address**: `0x880ac484a1743862989a441d6d867238c7aa311c`

**Only Transaction in Our Window**:
```
0xb94a94f415be9943bac4042d3a688502019200d9b0b1b8155d134046d4b2732e
Time: 2025-10-10 21:19:08 UTC
Direction: Auto-Deleveraging
Size: 183,821.57 SOL @ $160.58
```

⚠️ **Massive position - definitely opened well before Oct 10**

---

## 3. **$9.8M Profit** - BTC SHORT

**Address**: `0x8decc13b6e83873a78126e99036f9442019fd0b5`

**Only Transaction in Our Window**:
```
0x56270b21b0e492a857a0042d3a694b015f0023074be7b17af9efb6746fe86c92
Time: 2025-10-10 21:19:25 UTC
Direction: Auto-Deleveraging
Size: 664.70 BTC @ $106,216
```

---

## 4. **$9.4M Profit** - SOL SHORT

**Address**: `0xecb63caa47c7c4e77f60f1ce858cf28dc2b82b00`

**Only Transaction in Our Window**:
```
0xec2dce1b539d7e70eda7042d3a6925000068e600ee909d428ff6796e1291585b
Time: 2025-10-10 21:19:22 UTC
Direction: Auto-Deleveraging
Size: 166,820.26 SOL @ $156.42
```

---

## 5. **$7.5M Profit** - SOL SHORT

**Address**: `0x35d1151ef1aab579cbb3109e69fa82f94ff5acb1`

**Only Transaction in Our Window**:
```
0x784fd33f92195e9079c9042d3a636a010f00eb252d1c7d621c187e92511d387b
Time: 2025-10-10 21:17:06 UTC
Direction: Auto-Deleveraging
Size: 269,597.80 SOL @ $173.05
```

---

## 6. **$6.4M Profit** - BTC SHORT

**Address**: `0x5d2f4460ac3514ada79f5d9838916e508ab39bb7`

**First Transaction in Our Window**:
```
0xa5176caae9e2df57a691042d3a6a93020607009084e5fe2948e017fda8e6b942
Time: 2025-10-10 21:19:52 UTC
Direction: Auto-Deleveraging
Size: 181.47 BTC @ $103,960
```

**Later transactions**: All "Close Short" - position was being managed

---

## 7. **$4.9M Profit** - SOL SHORT ✅ 

**Address**: `0x023a3d058020fb76cca98f01b3c48c8938a22355`

**First Transactions in Our Window** (21:15 UTC):
```
0x8c335d66f9b54b2e8dad042d3a5ed902072e004c94b86a002ffc08b9b8b92519
Time: 2025-10-10 21:15:09 UTC
Direction: Close Short
Size: 1.30 SOL @ $182.07
```

✅ **Found 3 "Open Short" fills in our data!**

**Sample Open Short**:
```
Times: 21:15-21:17 UTC
Direction: Open Short
Prices: ~$175-182
```

**Note**: Entry might still be earlier - check explorer for full history

---

## 8. **$3.1M Profit** - BTC SHORT

**Address**: `0x960bb18454cd67b5a3edb4fa802b7c0b5b10e2ee`

**Only Transaction in Our Window**:
```
0xb94a94f415be9943bac4042d3a688502019200d9b0b1b8155d134046d4b2732e
Time: 2025-10-10 21:19:08 UTC
Direction: Auto-Deleveraging
Size: 280.09 BTC @ $108,202
```

---

## 9. **$2.8M Profit** - SOL SHORT

**Address**: `0xd4c1f7e8d876c4749228d515473d36f919583d1d`

**First Transaction in Our Window**:
```
0x784fd33f92195e9079c9042d3a636a010f00eb252d1c7d621c187e92511d387b
Time: 2025-10-10 21:17:06 UTC
Direction: Auto-Deleveraging
Size: 55,446.72 SOL @ $173.05
```

**Later transactions** (21:25-21:27): "Open Long" - probably flipping to long after ADL

---

## 10. **$2.8M Profit** - SOL SHORT ✅

**Address**: `0xa461db6d21568e97e040c4ab57ff38708a4f0f67`

**First Transaction in Our Window**:
```
0x784fd33f92195e9079c9042d3a636a010f00eb252d1c7d621c187e92511d387b
Time: 2025-10-10 21:17:06 UTC
Direction: Auto-Deleveraging
Size: 67,138.67 SOL @ $173.05
```

✅ **Found 72 "Open Short" fills in our data!** (21:25 UTC)

**Sample Open Short**:
```
0xebb0843531560293ed2a042d3a79830207e6001acc5921658f792f87f059dc7e
Time: 2025-10-10 21:25:05 UTC
Direction: Open Short
Size: 897.57 SOL @ $177.22
```

**Note**: These opens are AFTER the ADL - trader was re-opening shorts

---

## 📊 Summary

| # | Address | Asset | PNL | Opens in Window? | Entry Lookup Method |
|---|---------|-------|-----|------------------|---------------------|
| 1 | `0xb317...` | BTC | $79.7M | ❌ | Explorer history |
| 2 | `0x880a...` | SOL | $10.6M | ❌ | Explorer history |
| 3 | `0x8dec...` | BTC | $9.8M | ❌ | Explorer history |
| 4 | `0xecb6...` | SOL | $9.4M | ❌ | Explorer history |
| 5 | `0x35d1...` | SOL | $7.5M | ❌ | Explorer history |
| 6 | `0x5d2f...` | BTC | $6.4M | ❌ | Explorer history |
| 7 | `0x023a...` | SOL | $4.9M | ⚠️ Partial | Explorer history |
| 8 | `0x960b...` | BTC | $3.1M | ❌ | Explorer history |
| 9 | `0xd4c1...` | SOL | $2.8M | ❌ | Explorer history |
| 10 | `0xa461...` | SOL | $2.8M | ⚠️ After ADL | Explorer history |

**Key Finding**: 8/10 positions were definitely opened before our window  
**Recommendation**: Use explorer to find actual entry prices

---

## 🎯 What You're Looking For

When searching on explorer, find transactions with:
- **Direction**: "Open Short" (for shorts)
- **Asset**: BTC or SOL (matching their position)
- **Date**: Likely days or weeks BEFORE October 10
- **Size**: Large size (these are big positions)

**Entry Price** = Price of the earliest "Open Short" transaction  
(Or weighted average if they scaled in over multiple transactions)

---

## 💡 Expected Findings

Since all these traders made massive profits on shorts:
- **Entry prices likely $115k-$125k for BTC** (much higher than crash low of $97k)
- **Entry prices likely $190-$220 for SOL** (much higher than crash low of $137)
- **Positions opened days/weeks before** (predicting the crash)

This aligns with the ADL mechanism - these were **highly profitable shorts** that had been open for a while, which is why they were selected for ADL.

---

## 📝 For Your Paper

Once you get entry prices from the explorer:
1. Note the entry date (how long position was held)
2. Calculate actual % return (not just PNL)
3. Show that profitable shorts were forced to close via ADL
4. Demonstrate ADL mechanism worked as designed

**This will be much more rigorous than our NULL entry prices!** 🎉

