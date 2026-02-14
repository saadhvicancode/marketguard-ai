# MarketGuard AI - System Design

**Hackathon Round 1 MVP**

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    USER INTERFACE LAYER                      │
│         Mobile Web App | SMS/USSD | Web Portal              │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                      API GATEWAY                             │
│         Authentication, Rate Limiting, Load Balancing        │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                    CORE SERVICES LAYER                       │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ Forecasting  │  │     Risk     │  │Recommendation│     │
│  │   Service    │  │  Assessment  │  │   Engine     │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│  ┌──────────────┐  ┌──────────────┐                        │
│  │Explainability│  │   Feedback   │                        │
│  │   Service    │  │   Collector  │                        │
│  └──────────────┘  └──────────────┘                        │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                      DATA LAYER                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐     │
│  │ Data Pipeline│  │  Time Series │  │    Model     │     │
│  │   (ETL)      │  │   Database   │  │   Registry   │     │
│  └──────────────┘  └──────────────┘  └──────────────┘     │
│  ┌──────────────┐  ┌──────────────┐                        │
│  │  User Data   │  │  Cache Layer │                        │
│  │   Store      │  │   (Redis)    │                        │
│  └──────────────┘  └──────────────┘                        │
└─────────────────────────────────────────────────────────────┘
                              │
┌─────────────────────────────────────────────────────────────┐
│                   EXTERNAL DATA SOURCES                      │
│  • Government Mandi Portals  • Weather APIs                 │
│  • Agricultural Databases    • Market News Feeds            │
└─────────────────────────────────────────────────────────────┘
```

**Round 1 Simplifications:**
- Single server deployment (no microservices)
- SQLite or CSV for data storage
- In-memory caching (no Redis)
- No external API integrations

**Stack:**
- Backend: Python + Flask/FastAPI
- ML: statsmodels (ARIMA), pandas, numpy
- Frontend: React or HTML/CSS/JS + Chart.js
- Data: SQLite or CSV files

---

## Core Components

### 1. Data Layer
- Load CSV with columns: `date, mandi_id, commodity, modal_price`
- Validate: no nulls, prices > 0, dates in order
- Store: SQLite or in-memory pandas DataFrame

### 2. Forecasting Engine

```python
from statsmodels.tsa.arima.model import ARIMA

def forecast_price(prices, horizon=14):
    model = ARIMA(prices, order=(1,1,1))
    fitted = model.fit()
    forecast = fitted.forecast(steps=horizon)
    conf_int = fitted.get_forecast(steps=horizon).conf_int()
    
    return {
        'predictions': forecast.tolist(),
        'lower': conf_int.iloc[:, 0].tolist(),
        'upper': conf_int.iloc[:, 1].tolist()
    }
```

### 3. Risk Assessment

```python
def assess_risk(prices, forecast, current_price):
    # Volatility
    volatility = prices.pct_change().std()
    vol_level = "HIGH" if volatility > 0.05 else "MEDIUM" if volatility > 0.02 else "LOW"
    
    # Downside probability
    below_current = sum(1 for p in forecast['predictions'] if p < current_price)
    downside_prob = below_current / len(forecast['predictions'])
    
    return {'volatility': vol_level, 'downside_prob': downside_prob}
```

### 4. Recommendation Logic

```python
def recommend(current_price, forecast, risk):
    max_price = max(forecast['predictions'])
    gain = (max_price - current_price) / current_price
    
    if risk['downside_prob'] > 0.6:
        return {'action': 'SELL_NOW', 'reason': 'High risk of decline'}
    elif gain < 0.03:
        return {'action': 'SELL_NOW', 'reason': 'Minimal expected gain'}
    elif gain > 0.05 and risk['volatility'] != 'HIGH':
        days = forecast['predictions'].index(max_price) + 1
        return {'action': 'WAIT', 'days': days, 'reason': f'Expected gain {gain*100:.1f}%'}
    else:
        return {'action': 'MONITOR', 'reason': 'Uncertain conditions'}
```

### 5. API Endpoint

```python
@app.post("/api/recommend")
def get_recommendation(request):
    # Get historical data
    prices = load_prices(request.commodity, request.mandi_id)
    
    # Forecast
    forecast = forecast_price(prices[-60:])  # Last 60 days
    
    # Risk
    risk = assess_risk(prices, forecast, request.current_price)
    
    # Recommend
    rec = recommend(request.current_price, forecast, risk)
    
    return {
        'recommendation': rec,
        'forecast': forecast,
        'risk': risk,
        'explanation': generate_explanation(rec, request.current_price)
    }
```

---

## UI Flow

1. User selects: Commodity, Mandi, Current Price
2. Click "Get Recommendation"
3. Display:
   - **Action**: SELL NOW / WAIT X DAYS / MONITOR
   - **Reason**: Simple explanation
   - **Chart**: Price history + forecast (optional)
   - **Risk**: Volatility level + downside probability

---

## File Structure

```
marketguard-ai/
├── app.py              # Flask API
├── forecaster.py       # ARIMA forecasting
├── recommender.py      # Decision logic
├── data/
│   └── sample_mandi_prices.csv
├── static/             # CSS, JS
├── templates/          # HTML
└── requirements.txt
```

---

## Demo Scenario

**Input:** Farmer has wheat at ₹2200 in Pune Mandi

**Processing:**
- Load 60 days of wheat prices
- Forecast next 14 days
- Calculate risk metrics
- Generate recommendation

**Output:**
- Action: "Wait 7 days"
- Reason: "Prices expected to rise 5.2%"
- Risk: "Low volatility, 25% downside probability"
- Chart: Historical + forecast with confidence bands

---

## Round 1 Priorities

**Must Have:**
- ✅ ARIMA forecasting working
- ✅ Basic risk calculation
- ✅ Recommendation logic
- ✅ REST API endpoint
- ✅ Simple web UI
- ✅ Works with sample data

**Nice to Have:**
- Price chart visualization
- Multiple commodities
- Mobile responsive design

**Future:**
- Ensemble models
- Real-time data
- Multilingual
- SMS integration

---

## Success Criteria

- System generates sensible recommendations
- Demo runs without errors
- Clear explanation of value to farmers
- Responsible AI considerations addressed
