# Stock Market AI Platform 📈🤖

> AI-powered stock market platform with real-time data, ML predictions, sentiment analysis, and automated risk management.

🚀 **Live MVP:** [https://fincast.web.app/](https://fincast.web.app/)

[![Hackathon](https://img.shields.io/badge/Hackathon-2024-blue)](https://github.com)
[![AI/ML](https://img.shields.io/badge/AI%2FML-LSTM%20%2B%20XGBoost-green)](https://github.com)
[![React](https://img.shields.io/badge/React-19-61dafb)](https://github.com)
[![TypeScript](https://img.shields.io/badge/TypeScript-5-blue)](https://github.com)
[![Live Demo](https://img.shields.io/badge/Live-MVP-success)](https://fincast.web.app/)

---

## 🎯 Problem Statement

Retail investors face significant challenges:
- Lack of AI-powered insights for stock selection
- Manual portfolio management is time-consuming and risky
- No proactive risk management tools
- Difficulty in analyzing market sentiment
- Missing real-time alerts for price movements

---

## 💡 Solution

An intelligent stock market platform that combines:
- **AI Recommendations**: LSTM + XGBoost ensemble model for stock predictions
- **Sentiment Analysis**: FinBERT model for news sentiment
- **Real-Time Data**: WebSocket-powered live prices and charts
- **Automated Risk Management**: ML-driven auto-close position feature
- **Multi-Device Notifications**: Firebase Cloud Messaging across devices

---

## ✨ Key Features

### 1. 🤖 AI Stock Recommendations
- Top 3 stocks per sector at market open
- LSTM + XGBoost hybrid model
- FinBERT sentiment analysis
- Confidence scores (0-100%)
- Expected return & risk rating

### 2. 📊 Live Charts & Technical Indicators
- TradingView Lightweight Charts (OHLCV candlestick)
- Real-time price updates via WebSocket
- Technical indicators: SMA 50, RSI, MACD
- Multiple time intervals (1min to 1day)
- Responsive & interactive

### 3. 🔮 ML Price Predictions
- Predicted closing price for current day
- Confidence interval (min-max range)
- Risk analysis (volatility, downside, upside)
- Historical accuracy tracking
- Hourly updates during market hours

### 4. 📰 News & Sentiment Analysis
- Latest news articles per stock
- FinBERT sentiment analysis (Positive/Negative/Neutral)
- Overall sentiment score
- Filter by sentiment
- Refreshes every 15 minutes

### 5. 🔔 Smart Notifications
- **Alert Only Mode**: Notify when price approaches stop-loss
- **Auto-Close Mode**: ML-driven automatic position closing
- Multi-device FCM push notifications
- In-app notification center with history
- Quiet hours & customization

### 6. 💼 Portfolio Management
- Real-time portfolio tracking
- Profit/loss analytics
- Sector-wise allocation (pie chart)
- Performance over time (line chart)
- Sync with Angel One holdings
- Watchlist with price alerts

### 7. 💳 Payment Integration
- Freemium model (Free + Premium ₹499/month)
- Razorpay integration (UPI, cards, net banking)
- Subscription management
- Invoice generation

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    CLIENT LAYER                          │
│  React 19 + Vite + TypeScript + TradingView Charts     │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                   API GATEWAY                            │
│  Express.js / FastAPI + JWT Auth + Rate Limiting       │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                  SERVICE LAYER                           │
│  Market Service | ML Service | Notification Service     │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│               EXTERNAL SERVICES                          │
│  Angel One SmartAPI | News API | Razorpay | Firebase   │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                   DATA LAYER                             │
│  MongoDB Atlas | Redis Cloud | Firebase                │
└─────────────────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

### Frontend
- **Framework**: React 19 with Vite 7
- **Language**: TypeScript 5
- **Charts**: TradingView Lightweight Charts, Recharts
- **Animations**: Framer Motion
- **Icons**: Lucide React
- **Real-time**: Socket.IO Client
- **Forms**: React Hook Form + Zod

### Backend
- **Runtime**: Node.js + Express.js (TypeScript)
- **ML Service**: FastAPI (Python)
- **WebSocket**: Socket.IO Server
- **Authentication**: JWT
- **Validation**: Zod (Node) / Pydantic (Python)

### APIs & Integrations
- **Broker**: Angel One SmartAPI (orders, live data, portfolio)
- **Payments**: Razorpay (subscriptions, UPI, cards)
- **Notifications**: Firebase Cloud Messaging (FCM)
- **News**: NewsAPI / Finnhub

### Database
- **Primary**: MongoDB Atlas (users, portfolios, alerts)
- **Cache**: Redis Cloud (prices, sessions, rate limiting)

### ML/AI
- **Models**: LSTM + XGBoost (ensemble)
- **Sentiment**: FinBERT (ProsusAI/finbert)
- **Libraries**: scikit-learn, PyTorch, pandas, NumPy
- **Indicators**: TA-Lib / pandas-ta (RSI, MACD, SMA)

### Deployment
- **Frontend**: Firebase Hosting (free tier) - Live at https://fincast.web.app/
  - Alternative: Vercel (unlimited deploys, custom domain)
- **Backend**: Railway (500 hrs/month, free PostgreSQL)
  - Alternative: Render (750 hrs/month free)
- **Database**: MongoDB Atlas (512MB free forever)
- **Cache**: Redis Cloud (30MB free forever)
- **Domain**: Firebase subdomain (fincast.web.app) or custom domain via Vercel

**Total cost: ₹0 for the hackathon demo.**

---

## 🆚 Competitive Advantage

### vs Existing Platforms

| Feature | Zerodha Streak | Smallcase | Groww | FinCast |
|---------|----------------|-----------|-------|---------|
| AI Recommendations | ❌ | ❌ | ❌ | ✅ |
| ML Price Predictions | ❌ | ❌ | ❌ | ✅ |
| Auto-Close Position | ❌ | ❌ | ❌ | ✅ |
| News Sentiment (FinBERT) | ❌ | ❌ | ❌ | ✅ |
| Real-Time Charts | ✅ | ⚠️ | ✅ | ✅ |
| Portfolio Management | ✅ | ✅ | ✅ | ✅ |

### What Makes Us Different

1. **AI-First Approach**
   - Others: Rule-based or manual curation
   - Us: LSTM + XGBoost + FinBERT ensemble

2. **Proactive Risk Management**
   - Others: Manual stop-loss orders
   - Us: ML predicts drops and auto-closes

3. **Sentiment-Driven Insights**
   - Others: Basic news aggregation
   - Us: FinBERT sentiment integrated with predictions

4. **Real-Time Everything**
   - Others: Delayed or basic real-time
   - Us: WebSocket with <100ms latency

---

## 🌐 Live Demo

**MVP URL:** [https://fincast.web.app/](https://fincast.web.app/)

Experience the platform live with:
- AI-powered stock recommendations
- Real-time market data and charts
- ML price predictions
- Portfolio management
- News sentiment analysis

---

## 📚 Documentation

### 📄 [Requirements Document](requirements.md)
Comprehensive requirements with:
- 10 core features with user stories
- Acceptance criteria
- Non-functional requirements
- 48-hour hackathon timeline
- Success metrics & risk assessment

### 🎨 [Design Document](design.md)
Detailed design with:
- System architecture diagrams
- Database schemas (7 collections)
- API design (40+ endpoints)
- ML pipeline & feature engineering
- UI/UX mockups
- Security & performance optimization
- Deployment architecture

---

## 🚀 Getting Started

### Prerequisites
```bash
# Node.js 18+
node --version

# Python 3.10+
python --version

# MongoDB & Redis (or use cloud services)
```

### Installation
```bash
# Clone repository
git clone https://github.com/[username]/stock-market-ai-platform.git
cd stock-market-ai-platform

# Install frontend dependencies
npm install

# Install backend dependencies
cd backend
npm install

# Install ML dependencies
cd ml
pip install -r requirements.txt
```

### Environment Variables
```bash
# Backend (.env)
DATABASE_URL=mongodb+srv://...
REDIS_URL=redis://...
JWT_SECRET=...
ANGELONE_API_KEY=...
NEWS_API_KEY=...
RAZORPAY_KEY_ID=...
FIREBASE_PROJECT_ID=...

# Frontend (.env)
VITE_API_URL=http://localhost:3000
VITE_WS_URL=ws://localhost:3000
VITE_RAZORPAY_KEY_ID=...
```

### Run Development
```bash
# Frontend
npm run dev

# Backend
cd backend
npm run dev

# ML Service
cd ml
uvicorn main:app --reload
```

---

## 📊 ML Model Details

### Stock Price Prediction
- **Model**: Ensemble (LSTM 60% + XGBoost 40%)
- **Features**: OHLCV, technical indicators, sentiment, lag features
- **Training**: Daily at 6 AM IST
- **Accuracy Target**: >75%
- **Inference Time**: <200ms

### Sentiment Analysis
- **Model**: FinBERT (ProsusAI/finbert)
- **Input**: News headlines + descriptions
- **Output**: Positive/Negative/Neutral + confidence score
- **Update Frequency**: Every 15 minutes

---

## 🎯 Success Metrics

- **User Registration**: 100+ users in first week
- **Daily Active Users**: 50+
- **Session Duration**: 10+ minutes average
- **ML Prediction Accuracy**: >75%
- **Notification Delivery**: >99%
- **Payment Conversion**: >5%

---

## 🔒 Security

- HTTPS only
- JWT authentication (7-day expiry)
- bcrypt password hashing (10 rounds)
- API keys encrypted (AES-256-CBC)
- Rate limiting (5 login attempts per 15 min)
- CORS configuration
- XSS & SQL injection prevention

---

## 📈 Performance

- Page load time: <2 seconds
- API response time: <500ms (95th percentile)
- WebSocket latency: <100ms
- Support: 1000 concurrent users
- Uptime: 99.5% during market hours

---

## 💰 Business Model

### Free Tier
- 5 stocks in portfolio
- 3 price alerts
- Basic features

### Premium Tier (₹499/month)
- Unlimited portfolio stocks
- Unlimited alerts
- Auto-close position feature
- Priority ML predictions
- Advanced analytics

---

## 🤝 Contributing

This is a hackathon project. Contributions, issues, and feature requests are welcome!

---

## 📝 License

This project is for hackathon purposes.

---

## 👥 Team

Built with ❤️ for the hackathon

---

## 🙏 Acknowledgments

- Angel One for SmartAPI
- TradingView for Lightweight Charts
- Hugging Face for FinBERT model
- All open-source contributors

---

## 📞 Contact

For questions or feedback, please open an issue in this repository.

---

**⭐ Star this repo if you find it interesting!**

**🏆 Built for [Hackathon Name] 2024**
