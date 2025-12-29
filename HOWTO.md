# 📚 How the BANKNIFTY Decision System Works

## 🎯 Live Demo

**See it in action:** https://jsbin.com/rugezihame/edit?html,output

---

## 🔄 System Flow (Step-by-Step)

### Step 1: Data Input
System receives real-time BANKNIFTY data:
- 5-minute candles (OHLC + volume)
- Day High / Day Low
- Previous Day High / Low
- Current time (IST)

### Step 2: VWAP Calculation
```
VWAP = Σ(Typical Price × Volume) / Σ(Volume)
Typical Price = (High + Low + Close) / 3
Resets daily at market open
```

### Step 3: Market Context Classification

#### BULLISH Context:
- ✅ Higher Highs (HH) and Higher Lows (HL) on 5-min chart
- ✅ Price trading above VWAP
- ✅ Candles NOT heavily overlapping

#### BEARISH Context:
- ✅ Lower Highs (LH) and Lower Lows (LL) on 5-min chart
- ✅ Price trading below VWAP
- ✅ Clean downside structure

#### RANGE Context (NO TRADE):
- ⛔ Overlapping candles
- ⛔ Multiple VWAP crosses
- ⛔ Long wicks on both sides

---

## ⚡ Signal Generation

### BUY CE Signal (Call Option)

**ALL conditions must be TRUE:**

1. **Time Filter:** 9:45 AM - 3:10 PM IST
2. **Context:** BULLISH (HH/HL + above VWAP)
3. **Breakout Level:** 5-min candle CLOSES above:
   - Day High, OR
   - Previous Day High, OR
   - Major round number (59000, 59500, etc.)
4. **Candle Quality:** Body > Wicks
5. **Body Ratio:** Body ≥ 60% of total candle range

**Output Example:**
```
SIGNAL: BUY CE
REASON: Bullish context + 5-min close above day high with strong breakout candle
BREAK LEVEL: 59,520
INVALIDATION: 59,460
```

---

### BUY PE Signal (Put Option)

**ALL conditions must be TRUE:**

1. **Time Filter:** 9:45 AM - 3:10 PM IST
2. **Context:** BEARISH (LH/LL + below VWAP)
3. **Breakdown Level:** 5-min candle CLOSES below:
   - Day Low, OR
   - Previous Day Low, OR
   - Major round number
4. **Candle Quality:** Body > Wicks
5. **Body Ratio:** Body ≥ 60% of total candle range

**Output Example:**
```
SIGNAL: BUY PE
REASON: Bearish context + 5-min close below prev day low with strong breakdown candle
BREAK LEVEL: 58,890
INVALIDATION: 58,950
```

---

## 🛡️ Setup Invalidation (Automatic Exit Alert)

System switches to **NO TRADE – SETUP FAILED** if:

1. ⛔ Price closes BACK INSIDE broken level
2. ⛔ Price crosses to OPPOSITE side of VWAP
3. ⛔ Structure FLIPS (HH/HL → LH/LL or vice versa)

**Example:**
- CE signal given at 59,520
- Next candle closes at 59,480 (below breakout)
- **Alert:** "SETUP FAILED – Exit CE position"

---

## ⛔ Hard NO-TRADE Filters

System OVERRIDES all signals if:

### 1. Midday Low-Range
- Time: 11:30 AM - 1:45 PM
- AND Intraday range < 150 points
- **Reason:** Low volatility, whipsaws likely

### 2. Fake Breakouts Detected
- ≥2 failed breakouts in last 90 minutes
- **Reason:** Market testing levels, not genuine moves

### 3. Stuck in Range
- Price between same high/low > 60 minutes
- **Reason:** Consolidation, wait for breakout

### 4. VWAP Chop
- Price crosses VWAP ≥4 times in 10 candles
- **Reason:** No directional bias

---

## 📊 Real Example Walkthrough

### Scenario: Morning Bullish Breakout

**10:15 AM** - System State:
```
BANKNIFTY: 59,480
Day High: 59,500
Prev Day High: 59,450
VWAP: 59,420
Context: BULLISH (HH/HL forming)
Signal: NO TRADE (waiting for breakout)
```

**10:20 AM** - Breakout Candle:
```
Open: 59,490
High: 59,550
Low: 59,485
Close: 59,530 ✅ (above day high)
Body: 40 points
Upper Wick: 20 points
Lower Wick: 5 points
Body > Wicks ✅
```

**10:20 AM** - System Decision:
```
✅ SIGNAL: BUY CE
✅ REASON: Bullish + close above day high + strong body
✅ BREAK LEVEL: 59,500
✅ INVALIDATION: 59,500
```

**User Action:** Manually buy ATM CE (e.g., 59500 CE)

---

**10:25 AM** - Next Candle:
```
Close: 59,560 (holding above 59,500) ✅
Signal: Still BUY CE (setup valid)
```

---

**10:30 AM** - Invalidation Scenario:
```
Close: 59,480 (below 59,500) ⛔
Signal: NO TRADE – SETUP FAILED
```

**User Action:** Exit CE position immediately

---

## 🎨 UI Display

### Frontend shows ONLY:

1. **Signal Box** (large, color-coded)
   - Green: BUY CE
   - Red: BUY PE
   - Gray: NO TRADE

2. **Reason** (single line)
   - "Bullish context + 5-min close above day high"

3. **Levels** (two numbers)
   - Break Level: 59,520
   - Invalidation: 59,460

4. **Context Badge**
   - BULLISH / BEARISH / RANGE

5. **Timestamp**
   - Last updated: 10:20:15 IST

**That's it!** No charts, no indicator clutter.

---

## 🔌 Running the System

### Option 1: With Mock Data (Testing)

```bash
# Terminal 1: Start backend
uvicorn main:app --reload

# Terminal 2: Run mock data feeder
python data_feed_mock.py

# Open index.html in browser
# Watch signals update every 10 seconds
```

### Option 2: With Live Data (Production)

```python
# Replace data_feed_mock.py with broker API
from kiteconnect import KiteTicker

def on_ticks(ws, ticks):
    candle = aggregate_to_5min(ticks)
    decision = decision_service.on_new_candle(candle)
    # decision.signal available

kws = KiteTicker(api_key, access_token)
kws.on_ticks = on_ticks
kws.connect()
```

---

## 🎓 Key Principles

1. **Conservative First**
   - When in doubt → NO TRADE
   - Better to miss than force a bad signal

2. **Rule-Based Only**
   - No predictions
   - No AI/ML guessing
   - Pure logic: IF conditions met THEN signal

3. **Manual Execution**
   - System suggests, YOU decide
   - YOU place orders
   - NO automation

4. **Context Matters**
   - Breakout in RANGE = ignored
   - Breakout in BULLISH context = valid signal

5. **Invalidation is Critical**
   - Setup fails? Exit immediately
   - Don't hope for recovery

---

## ❓ FAQs

**Q: Why does it say NO TRADE so often?**  
A: Conservative by design. Better safe than sorry.

**Q: Can I change the 500-point round levels?**  
A: Yes, edit `round_step` in SignalEngine class (main.py).

**Q: What if I get a signal at 3:15 PM?**  
A: System won't generate signals after 3:10 PM (time filter).

**Q: Does it work for NIFTY?**  
A: Yes, just feed NIFTY data instead of BANKNIFTY.

**Q: Can I backtest this?**  
A: Yes, feed historical 5-min candle data to `on_new_candle()`.

---

## 🚀 Next Steps

1. ✅ Repository created
2. ✅ README added
3. ✅ requirements.txt added
4. ⏳ **Add main.py** (copy from conversation)
5. ⏳ **Add index.html** (copy from conversation)
6. ⏳ **Add data_feed_mock.py** (copy from conversation)
7. ✅ Run and test!

---

**Live Demo:** https://jsbin.com/rugezihame/edit?html,output
**Repository:** https://github.com/abbuudaykiranreddy8-commits/banknifty-decision-system
