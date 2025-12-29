# 🚀 BANKNIFTY Intraday Options Decision Support System

Conservative, rule-based intraday decision support system for BANKNIFTY options trading.

**⚠️ IMPORTANT: Personal use only | Manual execution | NO automated trading**

## 📋 System Overview

This is a private, rule-based system that provides CE/PE/NO TRADE signals for BANKNIFTY intraday options.

### Key Features

- ✅ Market context classification (Bullish/Bearish/Range)
- ✅ Strict breakout/breakdown entry rules
- ✅ Setup invalidation detection
- ✅ Hard no-trade filters (fake breakouts, VWAP chop, midday range)
- ✅ Clean UI: Only signal + reason + levels
- ✅ Password protected
- ✅ Manual execution only

## 🛠️ Complete File List

You need to create these 5 files:

1. **main.py** (400+ lines) - FastAPI backend with all rule engines
2. **index.html** - Frontend UI with real-time updates
3. **requirements.txt** - ✅ Already added
4. **data_feed_mock.py** - Test data generator
5. **README.md** - This file

## 📥 Get Complete Code Files

### All code files are provided in the conversation above.

Scroll up in the chat to find:
- Complete `main.py` (VWAPCalculator, ContextEngine, SignalEngine, RiskEngine, NoTradeEngine)
- Complete `index.html` (Beautiful minimal UI)
- Complete `data_feed_mock.py` (Mock data feeder)

**OR** Download from: https://jsbin.com/rugezihame/edit?html,output (Visual demo)

## 🚀 Quick Start

```bash
# 1. Clone this repository
git clone https://github.com/abbuudaykiranreddy8-commits/banknifty-decision-system.git
cd banknifty-decision-system

# 2. Create virtual environment
python -m venv venv

# Windows
venv\Scripts\activate

# Mac/Linux
source venv/bin/activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Add the missing files (main.py, index.html, data_feed_mock.py)
#    Copy from the conversation above

# 5. Run backend
uvicorn main:app --reload

# 6. Open index.html in browser
# Login: user / strong_password
```

## 📊 System Architecture

```
Data Feed → Candle Aggregator → VWAP Calculator
                                  ↓
                          Context Engine
                    (Bullish/Bearish/Range)
                                  ↓
                    ┌─────────────┴─────────────┐
                    ↓                           ↓
             Signal Engine              No-Trade Engine
           (CE/PE Logic)              (Hard Filters)
                    ↓                           ↓
                    └─────────────┬─────────────┘
                                  ↓
                          Risk Engine
                      (Invalidation)
                                  ↓
                     Decision Output
                  (BUY CE/PE or NO TRADE)
```

## 🔌 Production Data Integration

Replace `data_feed_mock.py` with real broker API:

### Supported Broker APIs:
- Zerodha Kite Connect
- Upstox API
- ICICI Breeze
- Angel One SmartAPI
- TrueData / GlobalDataFeeds

### Integration Pattern:

```python
from kiteconnect import KiteTicker

def on_ticks(ws, ticks):
    # Aggregate ticks into 5-min candles
    candle = build_candle(ticks)
    decision = decision_service.on_new_candle(candle)
    # decision.signal, decision.reason available

kws = KiteTicker(api_key, access_token)
kws.on_ticks = on_ticks
kws.connect(threaded=True)
```

## ⚙️ Configuration

### Auth Credentials
Edit `main.py` line ~360:
```python
def auth(credentials: HTTPBasicCredentials = Depends(security)):
    if credentials.username != "your_username" or credentials.password != "your_password":
        raise HTTPException(status_code=401)
```

### Signal Parameters
Edit `SignalEngine` class in `main.py`:
- `round_step` - Round number intervals (default: 500)
- Body/wick ratio threshold
- Time filters

## 📸 Screenshots

**Live Demo:** https://jsbin.com/rugezihame/edit?html,output

### Signal Examples:
- ✅ BUY CE - Bullish breakout above day high
- ✅ BUY PE - Bearish breakdown below prev day low
- ⛔ NO TRADE - Range/chop detected

## ⚠️ Disclaimer

- Personal use only
- Educational and decision-support tool
- No automated order placement
- No guarantees or profit promises
- Trading involves substantial risk
- Always verify signals manually before execution

## 📞 Support

For issues or questions, refer to code comments in `main.py`.

---

**Status:** ✅ Backend complete | ⏳ Add main.py, index.html, data_feed_mock.py from conversation

**Repository:** https://github.com/abbuudaykiranreddy8-commits/banknifty-decision-system
