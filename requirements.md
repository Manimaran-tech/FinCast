# Requirements Document — Stock Market AI Platform

🚀 **Live MVP:** [https://fincast.web.app/](https://fincast.web.app/)

## Project Overview
An AI-powered stock market platform that provides intelligent stock recommendations, real-time market data, ML-based predictions, sentiment analysis, and automated portfolio management with risk alerts.

## Target Users
- Retail investors seeking data-driven stock recommendations
- Active traders needing real-time market insights
- Risk-conscious investors wanting automated stop-loss protection

## Core Features

### 1. AI Stock Recommendations Dashboard
**Priority:** P0 (Critical)

**User Story:**
As an investor, I want to see top 3 AI-recommended stocks from each sector at market open, so I can make informed investment decisions.

**Requirements:**
- Display top 3 stocks per sector (Technology, Finance, Healthcare, Energy, Consumer, etc.)
- Recommendations generated using LSTM + XGBoost hybrid model
- Sentiment analysis using FinBERT model on recent news
- Update recommendations daily at market open (9:15 AM IST)
- Show confidence score (0-100%) for each recommendation
- Display expected return percentage
- Show risk rating (Low/Medium/High)

**Acceptance Criteria:**
- Recommendations visible on dashboard homepage
- Each stock card shows: symbol, name, sector, confidence, expected return, risk
- Click on stock navigates to detailed stock view
- Data refreshes automatically at market open

---

### 2. Comprehensive Stock List & Filtering
**Priority:** P0 (Critical)

**User Story:**
As a user, I want to browse all available stocks organized by sector, so I can explore investment opportunities.

**Requirements:**
- Display all stocks grouped by sector
- Filter by sector, market cap, price range
- Search by stock symbol or company name
- Sort by price, volume, % change, market cap
- Pagination (50 stocks per page)
- Real-time price updates via WebSocket

**Acceptance Criteria:**
- Stock list loads within 2 seconds
- Filters apply instantly without page reload
- Search returns results as user types (debounced)
- Price updates reflect within 1 second of market change

---

### 3. Detailed Stock View with Live Charts
**Priority:** P0 (Critical)

**User Story:**
As a trader, I want to view detailed stock information with live charts and technical indicators, so I can analyze trading opportunities.

**Requirements:**
- **Live OHLCV Chart:** TradingView Lightweight Charts with candlestick view
- **Time Intervals:** 1min, 5min, 15min, 1hour, 1day
- **Real-time Data:**
  - Current Price (LTP)
  - Open, High, Low, Close
  - Volume
  - Day Change (₹ and %)
  - Market Cap
  - P/E Ratio
  - 52-week High/Low

- **Technical Indicators:**
  - SMA 50 (Simple Moving Average)
  - RSI (Relative Strength Index)
  - MACD (Moving Average Convergence Divergence)
  - MACD Signal Line
  - Direction indicator (Bullish/Bearish/Neutral)

- **Chart Features:**
  - Zoom in/out
  - Pan across time
  - Crosshair with price/time tooltip
  - Toggle indicators on/off

**Acceptance Criteria:**
- Chart renders within 1 second
- Price updates in real-time via WebSocket
- Technical indicators calculate correctly
- Chart responsive on mobile devices
- Historical data loads for past 1 year

---

### 4. News Integration & Sentiment Analysis
**Priority:** P1 (High)

**User Story:**
As an investor, I want to read latest news about a stock with AI sentiment analysis, so I can understand market sentiment.

**Requirements:**
- Integrate news API (NewsAPI, Alpha Vantage, or Finnhub)
- Display 10 most recent news articles per stock
- Show article: title, source, published time, thumbnail
- Sentiment analysis using FinBERT:
  - Positive (green badge)
  - Negative (red badge)
  - Neutral (gray badge)
- Overall sentiment score for stock (aggregate of all news)
- Filter news by sentiment (Positive/Negative/Neutral)
- Click article opens in new tab

**Acceptance Criteria:**
- News loads within 2 seconds
- Sentiment badges display correctly
- Overall sentiment score updates when new articles arrive
- News refreshes every 15 minutes

---

### 5. ML Price Prediction & Risk Analysis
**Priority:** P1 (High)

**User Story:**
As a trader, I want to see AI-predicted closing price and risk analysis, so I can plan my trades.

**Requirements:**
- **ML Insight Panel** showing:
  - Predicted closing price for current trading day
  - Confidence interval (min-max range)
  - Prediction confidence score
  - Risk analysis:
    - Volatility score (Low/Medium/High)
    - Downside risk percentage
    - Upside potential percentage
  - Model used (LSTM + XGBoost)
  - Last updated timestamp

- **Risk Factors Display:**
  - Market risk
  - Sector risk
  - Stock-specific risk
  - News sentiment impact

- **Prediction Accuracy Tracking:**
  - Show previous day's prediction vs actual close
  - Display model accuracy percentage

**Acceptance Criteria:**
- Prediction updates every hour during market hours
- Risk analysis recalculates when new data arrives
- Confidence score between 0-100%
- Historical prediction accuracy visible

---

### 6. Firebase Cloud Messaging (FCM) Notification Bot
**Priority:** P1 (High)

**User Story:**
As an investor, I want automated alerts and position management, so I can protect my investments from losses.

**Requirements:**
- **Two Notification Modes:**

  **Mode 1: Alert Only**
  - Send push notification when stock price approaches stop-loss
  - Alert triggers at 95% of stop-loss value
  - Notification includes: stock name, current price, stop-loss, % away
  - User can snooze alert for 15 minutes
  - Alert repeats if price continues to fall

  **Mode 2: Auto-Close Position**
  - Monitor stock price in real-time
  - If ML prediction shows price will fall below stop-loss before market close:
    - Send notification: "Auto-closing position for [STOCK]"
    - Place sell order via Angel One API
    - Send confirmation notification with order details
  - User can disable auto-close for specific stocks
  - Requires user confirmation during setup (one-time)

- **Notification Settings:**
  - Enable/disable notifications per stock
  - Choose mode (Alert Only / Auto-Close)
  - Set stop-loss value manually
  - Set stop-loss as percentage below buy price
  - Notification sound customization
  - Quiet hours (no alerts during specified times)

- **In-App Notification Center:**
  - View notification history (last 30 days)
  - Mark notifications as read/unread
  - Filter by type (alerts, predictions, portfolio updates)
  - Quick actions from notifications (view stock, close position)
  - Notification preferences per stock

**Acceptance Criteria:**
- Notifications deliver within 5 seconds of trigger
- Auto-close executes order within 10 seconds
- User can toggle modes without app restart
- FCM notifications work on both web and mobile browsers
- Notification history persists for 30 days
- No false alerts (99.5% accuracy)

---

### 7. Portfolio Management
**Priority:** P0 (Critical)

**User Story:**
As an investor, I want to manage my stock portfolio, so I can track my investments and performance.

**Requirements:**
- **Portfolio Dashboard:**
  - Total portfolio value
  - Total invested amount
  - Total profit/loss (₹ and %)
  - Day's profit/loss
  - Best performer (stock with highest gain)
  - Worst performer (stock with highest loss)

- **Holdings List:**
  - Stock symbol and name
  - Quantity held
  - Average buy price
  - Current price (real-time)
  - Current value
  - Profit/loss (₹ and %)
  - Allocation percentage (pie chart)

- **Portfolio Actions:**
  - Add stock manually (symbol, quantity, buy price, buy date)
  - Edit holding (update quantity, average price)
  - Remove stock from portfolio
  - Sync with Angel One holdings (if connected)

- **Portfolio Analytics:**
  - Sector-wise allocation (pie chart)
  - Performance over time (line chart)
  - Risk distribution (Low/Medium/High stocks)
  - Dividend tracking
  - Tax P&L report (FIFO calculation)

- **Watchlist:**
  - Add stocks to watchlist
  - Real-time price updates
  - Set price alerts (notify when price reaches target)
  - Quick buy button (redirects to broker)

**Acceptance Criteria:**
- Portfolio syncs with Angel One API
- Real-time price updates for all holdings
- Profit/loss calculations accurate to 2 decimal places
- Charts render within 1 second
- Add/edit/delete operations persist immediately

---

### 8. User Authentication & Authorization
**Priority:** P0 (Critical)

**Requirements:**
- Email/password registration
- Email verification
- Login with JWT token
- Password reset via email
- Session management (7-day expiry)
- Secure password hashing (bcrypt)
- Rate limiting (5 login attempts per 15 minutes)

**Acceptance Criteria:**
- Registration completes within 3 seconds
- Login returns JWT token
- Protected routes require valid token
- Token refresh before expiry

---

### 9. Angel One SmartAPI Integration
**Priority:** P0 (Critical)

**Requirements:**
- User can link Angel One account
- OAuth-style API key authorization
- Fetch real-time market data
- Place buy/sell orders
- Fetch user holdings and positions
- Get fund balance
- WebSocket connection for live prices
- Handle API rate limits gracefully

**Acceptance Criteria:**
- API connection establishes within 2 seconds
- WebSocket reconnects automatically on disconnect
- Orders execute within 1 second
- Error messages display clearly

---

### 10. Payment Integration (Razorpay)
**Priority:** P2 (Medium)

**Requirements:**
- **Free Tier:** Basic features, 5 stocks in portfolio, 3 alerts
- **Premium Tier:** ₹499/month
  - Unlimited portfolio stocks
  - Unlimited alerts
  - Auto-close position feature
  - Priority ML predictions
  - Advanced analytics

- Payment via Razorpay Checkout
- Support UPI, cards, net banking
- Subscription auto-renewal
- Invoice generation
- Payment history

**Acceptance Criteria:**
- Payment flow completes in under 30 seconds
- Subscription activates immediately after payment
- Auto-renewal notification 3 days before
- Refund processing within 7 days

---

## Non-Functional Requirements

### Performance
- Page load time < 2 seconds
- API response time < 500ms (95th percentile)
- WebSocket latency < 100ms
- Support 1000 concurrent users

### Security
- HTTPS only
- JWT token encryption
- API keys stored in environment variables
- SQL injection prevention
- XSS protection
- CORS configuration
- Rate limiting on all endpoints

### Scalability
- Horizontal scaling support
- Database connection pooling
- Redis caching for frequently accessed data
- CDN for static assets

### Reliability
- 99.5% uptime during market hours
- Automatic failover for WebSocket
- Database backups every 6 hours
- Error logging and monitoring

### Usability
- Mobile-responsive design
- Accessible (WCAG 2.1 AA guidelines)
- Loading states for all async operations
- Error messages in plain language
- Keyboard navigation support

### Browser Support
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+

---

## Technical Constraints

### APIs
- Angel One SmartAPI rate limits: 10 requests/second
- News API: 100 requests/day (free tier)
- FCM: Unlimited push notifications

### Database
- MongoDB Atlas: 512MB storage (free tier)
- Redis Cloud: 30MB cache (free tier)

### ML Models
- Model inference time < 200ms
- Model retraining: Daily at 6 AM IST
- Model accuracy target: >75%

---

## Competitive Analysis

### How We Compare to Existing Platforms

| Feature | Zerodha Streak | Smallcase | Groww | FinCast (Our Platform) |
|---------|----------------|-----------|-------|------------------------|
| **AI Recommendations** | ❌ No | ❌ No | ❌ No | ✅ LSTM + XGBoost + FinBERT |
| **ML Price Predictions** | ❌ No | ❌ No | ❌ No | ✅ Real-time predictions |
| **Auto-Close Position** | ❌ No | ❌ No | ❌ No | ✅ ML-driven automation |
| **News Sentiment** | ❌ No | ❌ No | ❌ No | ✅ FinBERT analysis |
| **Real-Time Charts** | ✅ Yes | ⚠️ Basic | ✅ Yes | ✅ TradingView + Indicators |
| **Portfolio Management** | ✅ Yes | ✅ Yes | ✅ Yes | ✅ Yes + AI insights |
| **Algo Trading** | ✅ Yes | ❌ No | ❌ No | ⚠️ Future |
| **Curated Baskets** | ❌ No | ✅ Yes | ⚠️ Limited | ⚠️ Future |
| **Target Users** | Algo traders | Theme investors | Beginners | AI-powered investors |

### Our Unique Value Propositions

1. **AI-First Approach**
   - Zerodha Streak: Rule-based algo trading (no ML)
   - Smallcase: Manual curation by experts
   - FinCast: AI recommendations using ensemble ML models

2. **Proactive Risk Management**
   - Others: Manual stop-loss orders
   - FinCast: ML predicts price drops and auto-closes positions

3. **Sentiment-Driven Insights**
   - Others: Basic news aggregation
   - FinCast: FinBERT sentiment analysis integrated with predictions

4. **Real-Time Everything**
   - Others: Delayed data or basic real-time
   - FinCast: WebSocket architecture with <100ms latency

5. **Democratized AI**
   - Others: Premium features for high-value customers
   - FinCast: AI recommendations free for all, premium for automation

---

## Out of Scope (Future Enhancements)
- Options and futures trading
- Mutual funds integration
- Social trading features
- Backtesting engine
- Mobile native apps (iOS/Android)
- Multi-language support
- Voice commands
- Crypto trading

---

## Success Metrics
- User registration: 100+ users in first week
- Daily active users: 50+
- Average session duration: 10+ minutes
- Portfolio sync success rate: >95%
- ML prediction accuracy: >75%
- Notification delivery rate: >99%
- Payment conversion rate: >5%

---

## Risk Assessment

| Risk | Impact | Probability | Mitigation |
|------|--------|-------------|------------|
| Angel One API downtime | High | Low | Cache data, fallback to mock data |
| ML model low accuracy | Medium | Medium | Use ensemble models, feature engineering |
| WebSocket connection drops | High | Medium | Auto-reconnect, exponential backoff |
| Database storage limit | Medium | Low | Data cleanup, upgrade to paid tier |
| Payment gateway issues | High | Low | Test mode during hackathon, error handling |
| News API rate limit | Low | High | Cache news, update every 15 minutes |

---

## Glossary
- **LTP:** Last Traded Price
- **OHLCV:** Open, High, Low, Close, Volume
- **SMA:** Simple Moving Average
- **RSI:** Relative Strength Index (0-100, >70 overbought, <30 oversold)
- **MACD:** Moving Average Convergence Divergence
- **Stop-Loss:** Price at which to sell to limit losses
- **FinBERT:** Financial BERT model for sentiment analysis
- **LSTM:** Long Short-Term Memory neural network
- **XGBoost:** Extreme Gradient Boosting algorithm
