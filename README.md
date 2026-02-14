# MarketGuard AI

**AI-Powered Decision Support for Agricultural Market Timing**

MarketGuard AI helps smallholder farmers avoid distress sales by providing short-term market timing recommendations and risk intelligence using agricultural mandi price data.

---

## Problem

Smallholder farmers face economic vulnerability due to:
- Immediate cash pressure forcing premature sales at suboptimal prices
- Lack of market timing intelligence and price trend analysis
- Inability to assess price volatility and downside risk
- Significant income loss from poor timing decisions

## Solution

MarketGuard AI provides:
- **Short-term price forecasting** (3–14 days) using ensemble ML models
- **Risk assessment** quantifying volatility and downside probability
- **Sell vs. wait recommendations** with optimal timing windows
- **Simple explanations** in local languages with voice support
- **Offline capability** for low-connectivity rural environments

---

## Repository Structure

```
marketguard-ai/
├── README.md                    # Project overview
├── requirements.md              # Functional and non-functional requirements
├── design.md                    # Technical system design
└── data/
    └── sample_mandi_prices.csv  # Sample dataset
```

---

## Key Features

**Intelligent Forecasting**
- Ensemble of ARIMA, XGBoost, and baseline models
- Commodity-specific model selection
- Confidence intervals and uncertainty quantification

**Risk-Aware Recommendations**
- Historical volatility analysis
- Downside probability estimation
- Risk-adjusted expected returns

**Explainable AI**
- Simple, jargon-free explanations
- Visual price charts with forecast bands
- Clear confidence levels

**Responsible Design**
- Advisory system, not prescriptive
- Safety guardrails against harmful recommendations
- Bias monitoring and fairness checks
- Comprehensive audit trails

**Rural-First Design**
- Mobile-first for low-end smartphones
- Offline capability with local caching
- SMS/USSD support for feature phones
- Voice input/output for low-literacy users
- Multilingual support

---

## Technical Approach

### Architecture
```
Historical Data → Forecasting → Risk Assessment → Decision Logic → Recommendation
                       ↓              ↓                ↓              ↓
                  Price Trends   Volatility      Risk-Reward    Sell/Wait
                  Confidence    Downside Risk    Analysis       + Timing
```

### Decision Logic
```python
if urgency == 'immediate':
    recommend SELL_NOW
elif risk_score > HIGH_THRESHOLD:
    recommend SELL_NOW (too risky to wait)
elif expected_gain < MINIMUM_THRESHOLD:
    recommend SELL_NOW (not worth waiting)
elif forecast_confidence < LOW_THRESHOLD:
    recommend MONITOR (uncertain conditions)
else:
    recommend WAIT (with optimal timing)
```

### Technology Stack
- **Backend**: Python 3.9+, FastAPI, Celery, Redis
- **ML/Data**: scikit-learn, statsmodels, XGBoost, Prophet, MLflow
- **Database**: TimescaleDB (time series)
- **Frontend**: React Native (mobile), React (web admin)
- **Infrastructure**: Kubernetes, Prometheus, Grafana

---

## Responsible AI Principles

**Transparency**: All recommendations include clear explanations and confidence levels

**User Autonomy**: Advisory recommendations only, users maintain full decision authority

**Safety**: Multiple guardrails prevent harmful recommendations, abstention when uncertainty is high

**Fairness**: Equal service quality across regions and demographics, continuous bias monitoring

**Accountability**: Comprehensive audit logs and outcome tracking

---

## Implementation Roadmap

**Phase 1 (Months 1-3)**: MVP with basic forecasting, rule-based recommendations, mobile app, single region pilot

**Phase 2 (Months 4-6)**: Ensemble models, risk assessment, explainability layer, SMS integration, 3 regions

**Phase 3 (Months 7-9)**: Horizontal scaling, advanced ML, multi-language support, offline capability, national rollout

**Phase 4 (Months 10-12)**: Personalization, weather integration, community features, third-party API

---

## Limitations

- **Short-term only**: 3–14 day forecasts, not long-term planning
- **Unpredictable events**: Cannot predict policy changes, extreme weather, or market shocks
- **Data dependency**: Accuracy depends on quality and availability of mandi data
- **Regional coverage**: Limited to areas with sufficient historical data

---

## Sample Data

`data/sample_mandi_prices.csv` contains synthetic mandi price data:
- Multiple commodities (wheat, rice, tomato)
- Multiple mandi locations (Pune, Meerut, Bangalore, Ludhiana)
- Daily price records with min, max, modal prices
- Quantity traded and source information

---

## Status

**Current Phase**: Requirements and design  
**Version**: 1.0  
**Last Updated**: February 6, 2026

---

*MarketGuard AI - Empowering farmers with market intelligence*
