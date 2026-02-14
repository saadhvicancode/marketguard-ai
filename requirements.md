# MarketGuard AI - Requirements

**Hackathon Round 1 MVP**

---

## Problem

Farmers sell produce under pressure without understanding price trends, leading to income loss. They need simple market timing guidance: sell now or wait?

---

## Solution

AI system that:
- Forecasts mandi prices (3-14 days)
- Assesses risk (volatility + downside probability)
- Recommends: Sell Now / Wait X Days / Monitor
- Explains reasoning in simple language

---

## Functional Requirements

### Data
- Load CSV with historical mandi prices (60+ days)
- Support 3+ commodities (wheat, rice, vegetables)
- Validate data quality

### Forecasting
- Generate 14-day price forecast
- Provide confidence intervals
- Use ARIMA or similar statistical model

### Risk Assessment
- Calculate volatility (Low/Medium/High)
- Estimate downside probability
- Combine into simple risk score

### Recommendations
- Provide clear action: Sell Now / Wait / Monitor
- Suggest optimal timing (if wait)
- Explain reasoning (2-3 sentences)
- Show confidence level

### Interface
- Mobile-responsive web UI
- Input: commodity, mandi, current price
- Output: recommendation + chart + explanation
- REST API endpoint

---

## Non-Functional Requirements

- Response time: < 3 seconds
- Works on mobile browsers
- Handles 10 concurrent users
- Graceful error handling
- Clear error messages

---

## Out of Scope (Round 1)

- Real-time data feeds
- User authentication
- SMS/voice interface
- Multiple languages
- Advanced ML models
- Production deployment

---

## Responsible AI

**Transparency:** Clear explanations, show confidence levels, disclose limitations

**Safety:** Don't recommend waiting when risk is very high, abstain if data insufficient

**User Autonomy:** Advisory only, not prescriptive, user makes final decision

**Fairness:** Equal quality across commodities and regions

---

## Success Criteria

**Technical:**
- Generates forecasts for 3+ commodities
- Recommendations are logical
- Demo runs smoothly

**Presentation:**
- Clear problem/solution explanation
- Live demo with real data
- Articulate farmer benefit
- Address responsible AI

---

## Future Enhancements

- Ensemble models (XGBoost, Prophet)
- Real-time data integration
- Multilingual support (Hindi, regional)
- Voice input/output
- SMS alerts
- Weather integration
- User feedback system
