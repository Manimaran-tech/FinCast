# Design Document — Stock Market AI Platform

🚀 **Live MVP:** [https://fincast.web.app/](https://fincast.web.app/) build using **Kiro**

## System Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         CLIENT LAYER                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │ Web Browser  │  │    Mobile    │  │     FCM      │          │
│  │ (React App)  │  │   Browser    │  │ Push Notif   │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      API GATEWAY LAYER                           │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │  Express.js / FastAPI (REST + WebSocket)                 │  │
│  │  - Authentication (JWT)                                   │  │
│  │  - Rate Limiting                                          │  │
│  │  - Request Validation (Zod/Pydantic)                     │  │
│  └──────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                      SERVICE LAYER                               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   Market     │  │      ML      │  │  Notification│          │
│  │   Service    │  │   Service    │  │   Service    │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    EXTERNAL SERVICES                             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │  Angel One   │  │   News API   │  │  Razorpay    │          │
│  │   SmartAPI   │  │              │  │              │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                       DATA LAYER                                 │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐          │
│  │   MongoDB    │  │     Redis    │  │   Firebase   │          │
│  │    Atlas     │  │    Cloud     │  │              │          │
│  └──────────────┘  └──────────────┘  └──────────────┘          │
└─────────────────────────────────────────────────────────────────┘
```

---

## Component Design

### 1. Frontend Architecture (React + TypeScript)

```
src/
├── components/
│   ├── Header/              # Navigation, user menu
│   ├── Sidebar/             # Sector navigation
│   ├── Layout/              # Page wrapper
│   ├── StockCard/           # Stock recommendation card
│   ├── StockChart/          # TradingView chart wrapper
│   ├── TechnicalIndicators/ # RSI, MACD, SMA display
│   ├── NewsCard/            # News article with sentiment
│   ├── MLInsightPanel/      # Prediction & risk analysis
│   ├── PortfolioSummary/    # Portfolio overview
│   ├── NotificationSettings/# FCM configuration
│   └── PaymentModal/        # Razorpay checkout
├── pages/
│   ├── Dashboard/           # AI recommendations
│   ├── Market/              # All stocks list
│   ├── StockDetail/         # Individual stock view
│   ├── Portfolio/           # User holdings
│   ├── News/                # News feed
│   └── Notifications/       # Notification center
├── services/
│   ├── api.ts               # Axios instance
│   ├── auth.ts              # JWT management
│   ├── websocket.ts         # Socket.IO client
│   └── fcm.ts               # Firebase messaging
├── hooks/
│   ├── useStockData.ts      # Real-time stock hook
│   ├── usePortfolio.ts      # Portfolio management
│   └── useNotifications.ts  # FCM hook
├── types/
│   └── index.ts             # TypeScript interfaces
└── utils/
    ├── formatters.ts        # Price, date formatting
    └── calculations.ts      # P&L, percentage calc
```


### 2. Backend Architecture (Node.js + Express)

```
backend/
├── src/
│   ├── routes/
│   │   ├── auth.ts          # Login, register, JWT
│   │   ├── stocks.ts        # Stock data endpoints
│   │   ├── portfolio.ts     # Portfolio CRUD
│   │   ├── predictions.ts   # ML predictions
│   │   ├── news.ts          # News & sentiment
│   │   ├── notifications.ts # FCM management
│   │   └── payments.ts      # Razorpay integration
│   ├── services/
│   │   ├── angelone.ts      # Angel One SDK wrapper
│   │   ├── ml.ts            # ML model inference
│   │   ├── sentiment.ts     # FinBERT sentiment
│   │   └── fcm.ts           # Firebase messaging
│   ├── models/
│   │   ├── User.ts          # Mongoose user schema
│   │   ├── Portfolio.ts     # Holdings schema
│   │   ├── Alert.ts         # Price alerts schema
│   │   └── Prediction.ts    # ML predictions cache
│   ├── middleware/
│   │   ├── auth.ts          # JWT verification
│   │   ├── rateLimit.ts     # Rate limiting
│   │   └── validation.ts    # Zod validation
│   ├── websocket/
│   │   └── priceStream.ts   # Socket.IO price relay
│   └── utils/
│       ├── cache.ts         # Redis operations
│       └── logger.ts        # Winston logger
└── ml/
    ├── models/
    │   ├── lstm_model.pkl   # Trained LSTM
    │   └── xgboost_model.pkl# Trained XGBoost
    ├── train.py             # Model training script
    └── predict.py           # Inference script
```

---

## Database Schema Design

### MongoDB Collections

#### 1. users
```javascript
{
  _id: ObjectId,
  email: String (unique, indexed),
  passwordHash: String,
  name: String,
  phone: String,
  emailVerified: Boolean,
  angelOneApiKey: String (encrypted),
  angelOneClientId: String,
  fcmToken: String,
  fcmTokens: [String], // Support multiple devices
  notificationPreferences: {
    enabled: Boolean,
    sound: Boolean,
    vibration: Boolean,
    quietHoursStart: String,
    quietHoursEnd: String
  },
  subscription: {
    tier: "free" | "premium",
    expiresAt: Date,
    razorpaySubscriptionId: String
  },
  createdAt: Date,
  updatedAt: Date
}
```


#### 2. portfolios
```javascript
{
  _id: ObjectId,
  userId: ObjectId (indexed),
  stocks: [
    {
      symbol: String,
      name: String,
      quantity: Number,
      avgBuyPrice: Number,
      buyDate: Date,
      sector: String,
      currentPrice: Number (updated real-time),
      currentValue: Number,
      profitLoss: Number,
      profitLossPercent: Number
    }
  ],
  totalInvested: Number,
  totalCurrentValue: Number,
  totalProfitLoss: Number,
  lastSyncedAt: Date,
  createdAt: Date,
  updatedAt: Date
}
```

#### 3. alerts
```javascript
{
  _id: ObjectId,
  userId: ObjectId (indexed),
  symbol: String (indexed),
  alertType: "stop_loss" | "target_price" | "price_change",
  triggerPrice: Number,
  currentPrice: Number,
  mode: "alert_only" | "auto_close",
  isActive: Boolean,
  triggeredAt: Date,
  notificationSent: Boolean,
  orderPlaced: Boolean,
  orderId: String,
  createdAt: Date
}
```

#### 4. predictions
```javascript
{
  _id: ObjectId,
  symbol: String (indexed),
  date: Date (indexed),
  predictedClose: Number,
  confidenceScore: Number,
  confidenceInterval: {
    min: Number,
    max: Number
  },
  riskAnalysis: {
    volatility: "low" | "medium" | "high",
    downsideRisk: Number,
    upsidePotential: Number
  },
  modelUsed: "lstm_xgboost",
  actualClose: Number (filled after market close),
  accuracy: Number,
  createdAt: Date
}
```


#### 5. news
```javascript
{
  _id: ObjectId,
  symbol: String (indexed),
  title: String,
  description: String,
  url: String,
  source: String,
  publishedAt: Date (indexed),
  imageUrl: String,
  sentiment: {
    label: "positive" | "negative" | "neutral",
    score: Number (0-1),
    model: "finbert"
  },
  createdAt: Date
}
```

#### 6. recommendations
```javascript
{
  _id: ObjectId,
  date: Date (indexed),
  sector: String (indexed),
  stocks: [
    {
      symbol: String,
      name: String,
      confidenceScore: Number,
      expectedReturn: Number,
      riskRating: "low" | "medium" | "high",
      currentPrice: Number,
      targetPrice: Number,
      reasons: [String]
    }
  ],
  generatedAt: Date,
  marketOpen: Date
}
```

#### 7. notifications
```javascript
{
  _id: ObjectId,
  userId: ObjectId (indexed),
  type: String (indexed), // alert_triggered, position_closed, price_prediction, etc.
  title: String,
  body: String,
  data: {
    symbol: String,
    price: Number,
    action: String,
    url: String,
    metadata: Object
  },
  priority: "high" | "normal" | "low",
  read: Boolean (indexed),
  actionTaken: Boolean,
  createdAt: Date (indexed),
  expiresAt: Date
}

// TTL index to auto-delete old notifications after 30 days
db.notifications.createIndex({ createdAt: 1 }, { expireAfterSeconds: 2592000 });
```

### Redis Cache Structure

```
# Price cache (TTL: 5 seconds)
stock:price:{symbol} → { ltp, open, high, low, close, volume, timestamp }

# User session (TTL: 7 days)
session:{userId} → { token, expiresAt, lastActivity }

# Rate limiting (TTL: 60 seconds)
ratelimit:{userId}:{endpoint} → request_count

# ML predictions cache (TTL: 1 hour)
prediction:{symbol}:{date} → { predictedClose, confidence, risk }

# News sentiment cache (TTL: 15 minutes)
news:sentiment:{symbol} → { overall_score, positive_count, negative_count }
```

---

## API Design

### REST Endpoints


#### Authentication
```
POST   /api/auth/register
POST   /api/auth/login
POST   /api/auth/logout
POST   /api/auth/refresh-token
POST   /api/auth/forgot-password
POST   /api/auth/reset-password
GET    /api/auth/verify-email/:token
```

#### Stocks
```
GET    /api/stocks                    # List all stocks
GET    /api/stocks/:symbol            # Stock details
GET    /api/stocks/:symbol/chart      # OHLCV data
GET    /api/stocks/:symbol/indicators # Technical indicators
GET    /api/stocks/sectors            # List sectors
GET    /api/stocks/sector/:name       # Stocks by sector
GET    /api/stocks/search?q=          # Search stocks
```

#### Recommendations
```
GET    /api/recommendations           # Today's top picks
GET    /api/recommendations/:sector   # Sector-specific
GET    /api/recommendations/history   # Past recommendations
```

#### Portfolio
```
GET    /api/portfolio                 # User portfolio
POST   /api/portfolio/stocks          # Add stock
PUT    /api/portfolio/stocks/:id      # Update holding
DELETE /api/portfolio/stocks/:id      # Remove stock
POST   /api/portfolio/sync            # Sync with Angel One
GET    /api/portfolio/analytics       # Performance charts
```

#### Predictions
```
GET    /api/predictions/:symbol       # ML prediction
GET    /api/predictions/:symbol/risk  # Risk analysis
GET    /api/predictions/:symbol/accuracy # Historical accuracy
```

#### News
```
GET    /api/news/:symbol              # Stock news
GET    /api/news/:symbol/sentiment    # Sentiment analysis
GET    /api/news/trending             # Trending news
```


#### Alerts
```
GET    /api/alerts                    # User alerts
POST   /api/alerts                    # Create alert
PUT    /api/alerts/:id                # Update alert
DELETE /api/alerts/:id                # Delete alert
POST   /api/alerts/:id/toggle         # Enable/disable
```

#### Notifications
```
GET    /api/notifications              # Get notification history
POST   /api/notifications/fcm-token   # Register FCM token
PUT    /api/notifications/fcm-token   # Update FCM token
DELETE /api/notifications/fcm-token   # Remove FCM token
GET    /api/notifications/settings    # Notification preferences
PUT    /api/notifications/settings    # Update preferences
PATCH  /api/notifications/:id/read    # Mark as read
DELETE /api/notifications/:id          # Delete notification
POST   /api/notifications/test         # Send test notification
```

#### Payments
```
POST   /api/payments/create-order     # Razorpay order
POST   /api/payments/verify           # Verify payment
GET    /api/payments/subscription     # Subscription status
POST   /api/payments/cancel           # Cancel subscription
GET    /api/payments/history          # Payment history
```

#### Angel One Integration
```
POST   /api/angelone/connect          # Link account
POST   /api/angelone/disconnect       # Unlink account
GET    /api/angelone/holdings         # Fetch holdings
POST   /api/angelone/order            # Place order
GET    /api/angelone/orders           # Order history
GET    /api/angelone/funds            # Fund balance
```

### WebSocket Events

#### Client → Server
```javascript
// Subscribe to stock prices
socket.emit('subscribe', { symbols: ['RELIANCE', 'TCS', 'INFY'] })

// Unsubscribe
socket.emit('unsubscribe', { symbols: ['RELIANCE'] })

// Ping for connection check
socket.emit('ping')
```

#### Server → Client
```javascript
// Real-time price update
socket.on('price_update', {
  symbol: 'RELIANCE',
  ltp: 2450.50,
  change: 12.30,
  changePercent: 0.50,
  volume: 1234567,
  timestamp: '2026-02-11T10:30:00Z'
})

// Alert triggered
socket.on('alert_triggered', {
  alertId: '...',
  symbol: 'TCS',
  type: 'stop_loss',
  message: 'TCS approaching stop-loss at ₹3500'
})

// New notification
socket.on('notification', {
  notificationId: '...',
  type: 'alert_triggered',
  title: 'TCS Alert',
  body: 'Price approaching stop-loss',
  data: { symbol: 'TCS', price: 3500 },
  timestamp: '2026-02-11T10:30:00Z'
})

// Pong response
socket.on('pong')
```

---

## ML Model Design


### Stock Price Prediction Pipeline

```
┌─────────────────────────────────────────────────────────────┐
│                    DATA COLLECTION                           │
│  Angel One API → Historical OHLCV (1 year)                  │
│  News API → Headlines & sentiment                           │
│  Technical Indicators → RSI, MACD, SMA, Bollinger Bands    │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│                  FEATURE ENGINEERING                         │
│  • Price features: returns, volatility, momentum            │
│  • Technical indicators: RSI, MACD, SMA50, SMA200          │
│  • Volume features: volume ratio, OBV                       │
│  • Sentiment features: news sentiment score                 │
│  • Time features: day of week, month, quarter              │
│  • Lag features: price_lag_1, price_lag_5, price_lag_10    │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│                    MODEL TRAINING                            │
│  ┌──────────────┐         ┌──────────────┐                 │
│  │     LSTM     │         │   XGBoost    │                 │
│  │  (Sequence)  │         │  (Tabular)   │                 │
│  └──────────────┘         └──────────────┘                 │
│         ↓                         ↓                         │
│  ┌─────────────────────────────────────┐                   │
│  │      Ensemble (Weighted Avg)        │                   │
│  │      LSTM: 60% | XGBoost: 40%       │                   │
│  └─────────────────────────────────────┘                   │
└─────────────────────────────────────────────────────────────┘
                          ↓
┌─────────────────────────────────────────────────────────────┐
│                    PREDICTION OUTPUT                         │
│  • Predicted closing price                                  │
│  • Confidence interval (min, max)                           │
│  • Confidence score (0-100%)                                │
│  • Risk metrics (volatility, downside risk)                 │
└─────────────────────────────────────────────────────────────┘
```

### Model Training Schedule
- **Initial Training:** Before hackathon demo
- **Retraining:** Daily at 6:00 AM IST (before market open)
- **Training Data:** Past 1 year of OHLCV + news sentiment
- **Validation:** 80/20 train-test split, walk-forward validation

### Model Evaluation Metrics
- **MAE (Mean Absolute Error):** Target < ₹50
- **RMSE (Root Mean Squared Error):** Target < ₹75
- **MAPE (Mean Absolute Percentage Error):** Target < 5%
- **Directional Accuracy:** Target > 70% (up/down prediction)


### Sentiment Analysis (FinBERT)

```python
# Model: ProsusAI/finbert
# Input: News headline + description
# Output: {label: "positive"|"negative"|"neutral", score: 0-1}

from transformers import AutoTokenizer, AutoModelForSequenceClassification
import torch

tokenizer = AutoTokenizer.from_pretrained("ProsusAI/finbert")
model = AutoModelForSequenceClassification.from_pretrained("ProsusAI/finbert")

def analyze_sentiment(text):
    inputs = tokenizer(text, return_tensors="pt", truncation=True, max_length=512)
    outputs = model(**inputs)
    probs = torch.nn.functional.softmax(outputs.logits, dim=-1)
    label = ["positive", "negative", "neutral"][probs.argmax()]
    score = probs.max().item()
    return {"label": label, "score": score}
```

---

## UI/UX Design

### Design System

#### Color Palette
```css
/* Primary Colors */
--primary-green: #10B981;      /* Bullish, positive */
--primary-red: #EF4444;        /* Bearish, negative */
--primary-blue: #3B82F6;       /* Neutral, info */
--primary-purple: #8B5CF6;     /* Premium features */

/* Background */
--bg-primary: #FFFFFF;
--bg-secondary: #F9FAFB;
--bg-tertiary: #F3F4F6;

/* Text */
--text-primary: #111827;
--text-secondary: #6B7280;
--text-tertiary: #9CA3AF;

/* Borders */
--border-light: #E5E7EB;
--border-medium: #D1D5DB;
```

#### Typography
```css
/* Font Family */
--font-primary: 'Inter', -apple-system, sans-serif;
--font-mono: 'JetBrains Mono', monospace;

/* Font Sizes */
--text-xs: 0.75rem;    /* 12px */
--text-sm: 0.875rem;   /* 14px */
--text-base: 1rem;     /* 16px */
--text-lg: 1.125rem;   /* 18px */
--text-xl: 1.25rem;    /* 20px */
--text-2xl: 1.5rem;    /* 24px */
--text-3xl: 1.875rem;  /* 30px */
```


### Page Layouts

#### 1. Dashboard (AI Recommendations)
```
┌────────────────────────────────────────────────────────────┐
│  Header: Logo | Search | Notifications | Profile           │
├────────────────────────────────────────────────────────────┤
│ Sidebar │  Main Content                                    │
│         │  ┌──────────────────────────────────────────┐   │
│ • Home  │  │  Market Summary                          │   │
│ • Market│  │  NIFTY 50: 21,450 (+0.5%)               │   │
│ • Port. │  │  SENSEX: 70,800 (+0.3%)                 │   │
│ • News  │  └──────────────────────────────────────────┘   │
│ • Alerts│                                                  │
│         │  ┌──────────────────────────────────────────┐   │
│ Sectors │  │  🤖 AI Recommendations (Market Open)     │   │
│ • Tech  │  └──────────────────────────────────────────┘   │
│ • Bank  │                                                  │
│ • Pharma│  Technology Sector                               │
│ • Auto  │  ┌─────────┐ ┌─────────┐ ┌─────────┐          │
│         │  │ TCS     │ │ INFY    │ │ WIPRO   │          │
│         │  │ ₹3,850  │ │ ₹1,650  │ │ ₹450    │          │
│         │  │ 🟢 85%  │ │ 🟢 78%  │ │ 🟡 65%  │          │
│         │  │ +5.2%   │ │ +4.8%   │ │ +3.1%   │          │
│         │  └─────────┘ └─────────┘ └─────────┘          │
│         │                                                  │
│         │  Banking Sector                                  │
│         │  ┌─────────┐ ┌─────────┐ ┌─────────┐          │
│         │  │ HDFC    │ │ ICICI   │ │ SBI     │          │
│         │  │ ₹1,680  │ │ ₹1,120  │ │ ₹780    │          │
│         │  │ 🟢 82%  │ │ 🟢 75%  │ │ 🟡 68%  │          │
│         │  │ +4.5%   │ │ +3.9%   │ │ +2.8%   │          │
│         │  └─────────┘ └─────────┘ └─────────┘          │
└────────────────────────────────────────────────────────────┘
```


#### 2. Stock Detail Page
```
┌────────────────────────────────────────────────────────────┐
│  ← Back to Market    RELIANCE    ⭐ Add to Watchlist       │
├────────────────────────────────────────────────────────────┤
│  ┌──────────────────────────────────────────────────────┐ │
│  │  ₹2,450.50  🟢 +12.30 (+0.50%)                       │ │
│  │  Open: 2,438  High: 2,455  Low: 2,430  Vol: 1.2M    │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  ┌──────────────────────────────────────────────────────┐ │
│  │  📈 TradingView Chart (Candlestick)                  │ │
│  │  [1D] [5D] [1M] [3M] [1Y] [5Y]                       │ │
│  │                                                       │ │
│  │      2500 ┤     ╭─╮                                  │ │
│  │      2450 ┤   ╭─╯ ╰╮                                 │ │
│  │      2400 ┤ ╭─╯    ╰─╮                               │ │
│  │      2350 ┴─────────────                             │ │
│  │           9:15  11:00  1:00  3:30                    │ │
│  │                                                       │ │
│  │  Indicators: [SMA 50] [RSI] [MACD] [Bollinger]      │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  ┌─────────────────────┐  ┌──────────────────────────┐   │
│  │ Technical Indicators│  │ 🤖 ML Insight            │   │
│  │                     │  │                          │   │
│  │ SMA 50: ₹2,420     │  │ Predicted Close: ₹2,465  │   │
│  │ RSI: 62 (Neutral)  │  │ Confidence: 78%          │   │
│  │ MACD: 8.5 🟢       │  │ Range: ₹2,440-₹2,490     │   │
│  │ Signal: 7.2        │  │                          │   │
│  │ Direction: Bullish │  │ Risk: Medium             │   │
│  │                     │  │ Volatility: 2.3%         │   │
│  │ [Buy] [Sell]       │  │ Downside: -1.2%          │   │
│  └─────────────────────┘  │ Upside: +3.5%            │   │
│                            │                          │   │
│                            │ Model: LSTM+XGBoost      │   │
│                            │ Updated: 2 mins ago      │   │
│                            └──────────────────────────┘   │
│                                                            │
│  ┌──────────────────────────────────────────────────────┐ │
│  │  📰 News & Sentiment                                 │ │
│  │  Overall: 🟢 Positive (72%)                          │ │
│  │                                                       │ │
│  │  🟢 Reliance announces Q4 results, beats estimates   │ │
│  │     Economic Times • 2 hours ago                     │ │
│  │                                                       │ │
│  │  🟢 Jio subscriber base crosses 500M milestone       │ │
│  │     Business Standard • 5 hours ago                  │ │
│  │                                                       │ │
│  │  🔴 Oil prices surge, may impact margins             │ │
│  │     Reuters • 1 day ago                              │ │
│  └──────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
```


#### 3. Portfolio Page
```
┌────────────────────────────────────────────────────────────┐
│  My Portfolio                    [Sync with Angel One]     │
├────────────────────────────────────────────────────────────┤
│  ┌──────────────────────────────────────────────────────┐ │
│  │  Total Value: ₹5,45,230  🟢 +₹45,230 (+9.05%)       │ │
│  │  Invested: ₹5,00,000     Today: 🟢 +₹2,150 (+0.39%) │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  ┌─────────────────┐  ┌──────────────────────────────┐   │
│  │ Allocation      │  │ Performance (6M)             │   │
│  │                 │  │                              │   │
│  │   Tech 40%      │  │  ₹5.5L ┤         ╱           │   │
│  │   Bank 30%      │  │  ₹5.0L ┤       ╱             │   │
│  │   Pharma 20%    │  │  ₹4.5L ┤     ╱               │   │
│  │   Auto 10%      │  │  ₹4.0L ┴─────────────        │   │
│  │                 │  │        Aug Oct Dec Feb       │   │
│  └─────────────────┘  └──────────────────────────────┘   │
│                                                            │
│  Holdings                                                  │
│  ┌──────────────────────────────────────────────────────┐ │
│  │ RELIANCE  Energy  100 shares  Avg: ₹2,400            │ │
│  │ Current: ₹2,450  Value: ₹2,45,000  🟢 +₹5,000 (+2%) │ │
│  │ [Set Alert] [Sell]                                   │ │
│  ├──────────────────────────────────────────────────────┤ │
│  │ TCS  Technology  50 shares  Avg: ₹3,800              │ │
│  │ Current: ₹3,850  Value: ₹1,92,500  🟢 +₹2,500 (+1%) │ │
│  │ [Set Alert] [Sell]                                   │ │
│  ├──────────────────────────────────────────────────────┤ │
│  │ HDFCBANK  Banking  80 shares  Avg: ₹1,650            │ │
│  │ Current: ₹1,680  Value: ₹1,34,400  🟢 +₹2,400 (+2%) │ │
│  │ [Set Alert] [Sell]                                   │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  [+ Add Stock Manually]                                    │
└────────────────────────────────────────────────────────────┘
```


#### 4. Notification Settings Modal
```
┌────────────────────────────────────────────────────────────┐
│  🔔 Notification Settings                          [Close] │
├────────────────────────────────────────────────────────────┤
│                                                            │
│  Alert Mode for RELIANCE                                   │
│                                                            │
│  ○ Alert Only                                              │
│    Notify me when price approaches stop-loss               │
│                                                            │
│  ● Auto-Close Position                                     │
│    Automatically sell if ML predicts price will fall       │
│    below stop-loss before market close                     │
│                                                            │
│  ┌──────────────────────────────────────────────────────┐ │
│  │ ⚠️  Auto-Close will place sell orders automatically  │ │
│  │    Make sure you have sufficient holdings            │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  Stop-Loss Settings                                        │
│  ┌──────────────────────────────────────────────────────┐ │
│  │ Stop-Loss Price: ₹ [2,300]                           │ │
│  │ Current Price: ₹2,450                                │ │
│  │ Distance: -6.12%                                     │ │
│  └──────────────────────────────────────────────────────┘ │
│                                                            │
│  Notification Channels                                     │
│  ☑ Push Notifications (FCM)                               │
│  ☑ In-App Notifications                                   │
│  ☐ Email Alerts                                            │
│                                                            │
│  Quiet Hours                                               │
│  ☑ Enable  From: [22:00] To: [08:00]                      │
│                                                            │
│                              [Cancel]  [Save Settings]     │
└────────────────────────────────────────────────────────────┘
```

---

## Security Design

### Authentication Flow
```
1. User Registration
   ├─ Email + Password → bcrypt hash (10 rounds)
   ├─ Generate email verification token
   ├─ Send verification email
   └─ Store user in DB (emailVerified: false)

2. Email Verification
   ├─ Click link with token
   ├─ Verify token (JWT with 24h expiry)
   ├─ Update emailVerified: true
   └─ Redirect to login

3. Login
   ├─ Validate email + password
   ├─ Generate JWT access token (7 days)
   ├─ Generate refresh token (30 days)
   ├─ Store session in Redis
   └─ Return tokens to client

4. Protected Routes
   ├─ Extract JWT from Authorization header
   ├─ Verify signature + expiry
   ├─ Check session in Redis
   ├─ Attach user to request
   └─ Proceed to route handler
```


### Data Encryption
```javascript
// Sensitive data encryption (Angel One API keys)
const crypto = require('crypto');

const ENCRYPTION_KEY = process.env.ENCRYPTION_KEY; // 32 bytes
const IV_LENGTH = 16;

function encrypt(text) {
  const iv = crypto.randomBytes(IV_LENGTH);
  const cipher = crypto.createCipheriv('aes-256-cbc', Buffer.from(ENCRYPTION_KEY), iv);
  let encrypted = cipher.update(text);
  encrypted = Buffer.concat([encrypted, cipher.final()]);
  return iv.toString('hex') + ':' + encrypted.toString('hex');
}

function decrypt(text) {
  const parts = text.split(':');
  const iv = Buffer.from(parts.shift(), 'hex');
  const encrypted = Buffer.from(parts.join(':'), 'hex');
  const decipher = crypto.createDecipheriv('aes-256-cbc', Buffer.from(ENCRYPTION_KEY), iv);
  let decrypted = decipher.update(encrypted);
  decrypted = Buffer.concat([decrypted, decipher.final()]);
  return decrypted.toString();
}
```

### Rate Limiting Strategy
```javascript
// Express rate limiter
const rateLimit = require('express-rate-limit');

// General API rate limit
const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // 100 requests per window
  message: 'Too many requests, please try again later'
});

// Auth endpoints (stricter)
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5, // 5 login attempts
  skipSuccessfulRequests: true
});

// WebSocket connection limit
const wsLimiter = {
  maxConnections: 10, // per user
  maxSubscriptions: 50 // stocks per connection
};
```

---

## Performance Optimization

### Caching Strategy
```
┌─────────────────────────────────────────────────────────┐
│                    Cache Layers                          │
├─────────────────────────────────────────────────────────┤
│ 1. Browser Cache (Service Worker)                       │
│    - Static assets: 1 year                              │
│    - API responses: 5 minutes                           │
│                                                          │
│ 2. CDN Cache (CloudFront)                               │
│    - Images, CSS, JS: 1 year                            │
│    - HTML: No cache                                     │
│                                                          │
│ 3. Redis Cache (Application)                            │
│    - Stock prices: 5 seconds                            │
│    - ML predictions: 1 hour                             │
│    - News: 15 minutes                                   │
│    - User sessions: 7 days                              │
│                                                          │
│ 4. Database Query Cache                                 │
│    - MongoDB indexes on frequently queried fields       │
│    - Aggregation pipeline results: 5 minutes            │
└─────────────────────────────────────────────────────────┘
```


### Database Indexing
```javascript
// MongoDB indexes for optimal query performance

// users collection
db.users.createIndex({ email: 1 }, { unique: true });
db.users.createIndex({ "subscription.tier": 1 });

// portfolios collection
db.portfolios.createIndex({ userId: 1 }, { unique: true });
db.portfolios.createIndex({ "stocks.symbol": 1 });

// alerts collection
db.alerts.createIndex({ userId: 1 });
db.alerts.createIndex({ symbol: 1 });
db.alerts.createIndex({ isActive: 1, triggerPrice: 1 });
db.alerts.createIndex({ createdAt: 1 }, { expireAfterSeconds: 2592000 }); // 30 days TTL

// predictions collection
db.predictions.createIndex({ symbol: 1, date: -1 });
db.predictions.createIndex({ createdAt: 1 }, { expireAfterSeconds: 7776000 }); // 90 days TTL

// news collection
db.news.createIndex({ symbol: 1, publishedAt: -1 });
db.news.createIndex({ publishedAt: -1 });
db.news.createIndex({ createdAt: 1 }, { expireAfterSeconds: 2592000 }); // 30 days TTL

// recommendations collection
db.recommendations.createIndex({ date: -1, sector: 1 });
```

### WebSocket Optimization
```javascript
// Batch price updates to reduce message frequency
const BATCH_INTERVAL = 1000; // 1 second
const priceUpdateQueue = new Map();

function queuePriceUpdate(symbol, data) {
  priceUpdateQueue.set(symbol, data);
}

setInterval(() => {
  if (priceUpdateQueue.size > 0) {
    io.emit('price_batch', Array.from(priceUpdateQueue.entries()));
    priceUpdateQueue.clear();
  }
}, BATCH_INTERVAL);

// Subscription management
const userSubscriptions = new Map(); // userId -> Set<symbol>

socket.on('subscribe', ({ symbols }) => {
  const userId = socket.userId;
  if (!userSubscriptions.has(userId)) {
    userSubscriptions.set(userId, new Set());
  }
  const subs = userSubscriptions.get(userId);
  
  // Limit subscriptions per user
  if (subs.size + symbols.length > 50) {
    socket.emit('error', { message: 'Subscription limit reached (50 stocks)' });
    return;
  }
  
  symbols.forEach(symbol => subs.add(symbol));
  socket.join(symbols); // Join Socket.IO rooms
});
```

---

## Error Handling


### Error Response Format
```javascript
// Standardized error response
{
  "success": false,
  "error": {
    "code": "INVALID_CREDENTIALS",
    "message": "Email or password is incorrect",
    "details": null,
    "timestamp": "2026-02-11T10:30:00Z",
    "requestId": "req_abc123"
  }
}

// Error codes
const ErrorCodes = {
  // Authentication (1xxx)
  INVALID_CREDENTIALS: 1001,
  TOKEN_EXPIRED: 1002,
  UNAUTHORIZED: 1003,
  EMAIL_NOT_VERIFIED: 1004,
  
  // Validation (2xxx)
  VALIDATION_ERROR: 2001,
  MISSING_REQUIRED_FIELD: 2002,
  INVALID_FORMAT: 2003,
  
  // Business Logic (3xxx)
  INSUFFICIENT_FUNDS: 3001,
  STOCK_NOT_FOUND: 3002,
  PORTFOLIO_LIMIT_REACHED: 3003,
  ALERT_LIMIT_REACHED: 3004,
  
  // External API (4xxx)
  ANGELONE_API_ERROR: 4001,
  NEWS_API_ERROR: 4002,
  RAZORPAY_ERROR: 4003,
  
  // Server (5xxx)
  INTERNAL_SERVER_ERROR: 5001,
  DATABASE_ERROR: 5002,
  CACHE_ERROR: 5003
};
```

### Global Error Handler
```javascript
// Express error middleware
app.use((err, req, res, next) => {
  // Log error
  logger.error({
    message: err.message,
    stack: err.stack,
    requestId: req.id,
    userId: req.user?.id,
    path: req.path,
    method: req.method
  });
  
  // Determine status code
  const statusCode = err.statusCode || 500;
  
  // Send response
  res.status(statusCode).json({
    success: false,
    error: {
      code: err.code || 'INTERNAL_SERVER_ERROR',
      message: err.message || 'An unexpected error occurred',
      details: process.env.NODE_ENV === 'development' ? err.stack : null,
      timestamp: new Date().toISOString(),
      requestId: req.id
    }
  });
});
```

---

## Monitoring & Logging

### Logging Strategy
```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' }),
    new winston.transports.Console({
      format: winston.format.combine(
        winston.format.colorize(),
        winston.format.simple()
      )
    })
  ]
});

// Log levels: error, warn, info, http, debug
```


### Metrics to Track
```javascript
// Application metrics
const metrics = {
  // Performance
  apiResponseTime: { p50, p95, p99 },
  websocketLatency: { avg, max },
  mlInferenceTime: { avg, max },
  
  // Business
  activeUsers: { daily, weekly, monthly },
  portfolioSyncs: { success, failure },
  ordersPlaced: { count, value },
  alertsTriggered: { count, byType },
  
  // System
  cpuUsage: { avg, max },
  memoryUsage: { avg, max },
  databaseConnections: { active, idle },
  cacheHitRate: { percentage },
  
  // Errors
  errorRate: { byEndpoint, byCode },
  apiFailures: { angelone, news, razorpay }
};
```

---

## System Interaction Flow

### High-Level Sequence Diagram

```
User → Frontend → API Gateway → Services → External APIs → Database

┌──────┐     ┌──────────┐     ┌─────────┐     ┌─────────┐     ┌──────────┐     ┌──────────┐
│ User │     │ Frontend │     │   API   │     │ Service │     │ External │     │ Database │
│      │     │ (React)  │     │ Gateway │     │  Layer  │     │   APIs   │     │ (Mongo)  │
└──┬───┘     └────┬─────┘     └────┬────┘     └────┬────┘     └────┬─────┘     └────┬─────┘
   │              │                 │                │                │                │
   │ 1. Visit     │                 │                │                │                │
   │ Dashboard    │                 │                │                │                │
   ├─────────────>│                 │                │                │                │
   │              │                 │                │                │                │
   │              │ 2. GET /api/    │                │                │                │
   │              │ recommendations │                │                │                │
   │              ├────────────────>│                │                │                │
   │              │                 │                │                │                │
   │              │                 │ 3. Verify JWT  │                │                │
   │              │                 │ & Rate Limit   │                │                │
   │              │                 ├───────────────>│                │                │
   │              │                 │                │                │                │
   │              │                 │                │ 4. Check Redis │                │
   │              │                 │                │ Cache          │                │
   │              │                 │                ├───────────────>│                │
   │              │                 │                │                │                │
   │              │                 │                │ 5. Cache Miss  │                │
   │              │                 │                │ Query MongoDB  │                │
   │              │                 │                ├───────────────────────────────>│
   │              │                 │                │                │                │
   │              │                 │                │ 6. Return Data │                │
   │              │                 │                │<───────────────────────────────┤
   │              │                 │                │                │                │
   │              │                 │ 7. Return JSON │                │                │
   │              │                 │<───────────────┤                │                │
   │              │                 │                │                │                │
   │              │ 8. Return Data  │                │                │                │
   │              │<────────────────┤                │                │                │
   │              │                 │                │                │                │
   │ 9. Display   │                 │                │                │                │
   │ Recommendations                │                │                │                │
   │<─────────────┤                 │                │                │                │
   │              │                 │                │                │                │
   │ 10. Click    │                 │                │                │                │
   │ Stock (TCS)  │                 │                │                │                │
   ├─────────────>│                 │                │                │                │
   │              │                 │                │                │                │
   │              │ 11. GET /api/   │                │                │                │
   │              │ stocks/TCS      │                │                │                │
   │              ├────────────────>│                │                │                │
   │              │                 │                │                │                │
   │              │                 │ 12. Market     │                │                │
   │              │                 │ Service        │                │                │
   │              │                 ├───────────────>│                │                │
   │              │                 │                │                │                │
   │              │                 │                │ 13. Angel One  │                │
   │              │                 │                │ API (Live Data)│                │
   │              │                 │                ├───────────────>│                │
   │              │                 │                │                │                │
   │              │                 │                │ 14. ML Service │                │
   │              │                 │                │ (Prediction)   │                │
   │              │                 │                ├───────────────>│                │
   │              │                 │                │                │                │
   │              │                 │ 15. Combined   │                │                │
   │              │                 │ Response       │                │                │
   │              │<────────────────┤<───────────────┤                │                │
   │              │                 │                │                │                │
   │ 16. Display  │                 │                │                │                │
   │ Stock Detail │                 │                │                │                │
   │<─────────────┤                 │                │                │                │
   │              │                 │                │                │                │
   │ 17. WebSocket│                 │                │                │                │
   │ Connection   │                 │                │                │                │
   ├─────────────>│                 │                │                │                │
   │              │                 │                │                │                │
   │              │ 18. Subscribe   │                │                │                │
   │              │ to TCS prices   │                │                │                │
   │              ├────────────────>│                │                │                │
   │              │                 │                │                │                │
   │              │                 │ 19. Angel One  │                │                │
   │              │                 │ WebSocket Feed │                │                │
   │              │                 ├───────────────────────────────>│                │
   │              │                 │                │                │                │
   │              │ 20. Real-time   │                │                │                │
   │              │ Price Updates   │                │                │                │
   │<─────────────┤<────────────────┤<───────────────┤<───────────────┤                │
   │              │                 │                │                │                │
```

### Key Interaction Patterns

1. **Authentication Flow**: JWT verification on every API request
2. **Caching Strategy**: Redis first, MongoDB fallback
3. **Real-Time Updates**: WebSocket for prices, alerts, notifications
4. **ML Integration**: Async prediction service with caching
5. **External APIs**: Angel One for market data, News API for sentiment

---

## Deployment Architecture

### Hackathon Deployment (Free Tier)
```
┌─────────────────────────────────────────────────────────┐
│              Firebase Hosting (Frontend)                 │
│  - React app build                                      │
│  - Automatic HTTPS                                      │
│  - Global CDN                                           │
│  - Custom domain: fincast.web.app                      │
│  - Alternative: Vercel (stockai.vercel.app)            │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│                 Railway (Backend)                        │
│  - Node.js + Express server                             │
│  - WebSocket support                                    │
│  - Environment variables                                │
│  - Auto-deploy from GitHub                              │
│  - Free PostgreSQL addon (not used)                     │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│              MongoDB Atlas (Database)                    │
│  - M0 Free Tier (512MB)                                 │
│  - Shared cluster                                       │
│  - Automatic backups                                    │
└─────────────────────────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────┐
│              Redis Cloud (Cache)                         │
│  - 30MB Free Tier                                       │
│  - Single node                                          │
└─────────────────────────────────────────────────────────┘
```

### Environment Variables
```bash
# Backend (.env)
NODE_ENV=production
PORT=3000
DATABASE_URL=mongodb+srv://...
REDIS_URL=redis://...
JWT_SECRET=...
ENCRYPTION_KEY=...

# Angel One
ANGELONE_API_KEY=...
ANGELONE_CLIENT_ID=...

# News API
NEWS_API_KEY=...

# Razorpay
RAZORPAY_KEY_ID=...
RAZORPAY_KEY_SECRET=...

# Firebase
FIREBASE_PROJECT_ID=...
FIREBASE_PRIVATE_KEY=...
FIREBASE_CLIENT_EMAIL=...

# Frontend (.env)
VITE_API_URL=https://api.stockai.railway.app
VITE_WS_URL=wss://api.stockai.railway.app
VITE_RAZORPAY_KEY_ID=...
VITE_FIREBASE_CONFIG=...

# Note: Live MVP deployed at https://fincast.web.app/ uses Firebase Hosting
# Alternative deployment options: Vercel, Netlify, or custom domain
```

---

## Testing Strategy


### Testing Pyramid (Hackathon Scope)

```
                    ┌─────────────┐
                    │   Manual    │  Demo testing
                    │   Testing   │  User flows
                    └─────────────┘
                   ┌───────────────┐
                   │  Integration  │  API endpoints
                   │    Tests      │  Database ops
                   └───────────────┘
                ┌─────────────────────┐
                │    Unit Tests       │  Utility functions
                │                     │  Calculations
                └─────────────────────┘
```

### Critical Test Cases
```javascript
// 1. Authentication
✓ User can register with valid email
✓ User cannot register with duplicate email
✓ User can login with correct credentials
✓ User cannot login with wrong password
✓ JWT token expires after 7 days

// 2. Portfolio Management
✓ User can add stock to portfolio
✓ Portfolio calculates P&L correctly
✓ Portfolio syncs with Angel One API
✓ User cannot exceed portfolio limit (free tier)

// 3. Alerts & Notifications
✓ Alert triggers when price reaches stop-loss
✓ FCM notification sends successfully
✓ Auto-close places sell order correctly
✓ User can disable alerts

// 4. ML Predictions
✓ Prediction API returns valid response
✓ Confidence score is between 0-100
✓ Risk analysis calculates correctly

// 5. WebSocket
✓ Client connects to WebSocket server
✓ Price updates received in real-time
✓ Connection reconnects on disconnect
✓ Subscription limit enforced

// 6. Payments
✓ Razorpay order creates successfully
✓ Payment verification works
✓ Subscription activates after payment
```

---

## Data Flow Diagrams

### 1. Real-Time Price Update Flow
```
Angel One WebSocket
        │
        ▼
Backend WebSocket Handler
        │
        ├─→ Update Redis Cache (5s TTL)
        │
        ├─→ Check Active Alerts
        │   └─→ If triggered → Send FCM Notification
        │
        └─→ Broadcast to Connected Clients
                │
                ▼
        Frontend WebSocket Client
                │
                ├─→ Update Chart
                ├─→ Update Stock Card
                └─→ Update Portfolio Value
```


### 2. ML Prediction Flow
```
Scheduled Job (Daily 6 AM)
        │
        ▼
Fetch Historical Data (Angel One API)
        │
        ├─→ OHLCV (1 year)
        ├─→ Technical Indicators (RSI, MACD, SMA)
        └─→ News Sentiment (FinBERT)
        │
        ▼
Feature Engineering
        │
        ├─→ Price features (returns, volatility)
        ├─→ Technical features (normalized indicators)
        ├─→ Sentiment features (aggregated scores)
        └─→ Lag features (price_lag_1, price_lag_5)
        │
        ▼
Model Inference
        │
        ├─→ LSTM Model (60% weight)
        └─→ XGBoost Model (40% weight)
        │
        ▼
Ensemble Prediction
        │
        ├─→ Predicted Close Price
        ├─→ Confidence Interval (min, max)
        ├─→ Confidence Score (0-100)
        └─→ Risk Analysis (volatility, downside, upside)
        │
        ▼
Store in MongoDB + Redis Cache
        │
        ▼
Serve via API (/api/predictions/:symbol)
```

### 3. Auto-Close Position Flow
```
Price Update Received
        │
        ▼
Check Active Alerts (mode: auto_close)
        │
        ▼
Fetch ML Prediction for Stock
        │
        ▼
Is predicted_close < stop_loss?
        │
        ├─ NO → Continue monitoring
        │
        └─ YES → Trigger Auto-Close
                │
                ├─→ Send FCM Notification
                │   "Auto-closing position for [STOCK]"
                │
                ├─→ Place Sell Order (Angel One API)
                │   └─→ Market order for full quantity
                │
                ├─→ Update Alert Status
                │   └─→ orderPlaced: true, orderId: "..."
                │
                └─→ Send Confirmation Notification
                    "Position closed: [STOCK] at ₹[PRICE]"
```

---

## Notification Center Design

### In-App Notification Center

The notification center provides a centralized hub for all user notifications, accessible from the header bell icon.

#### Notification Types
```javascript
const NotificationTypes = {
  ALERT_TRIGGERED: 'alert_triggered',        // Stop-loss or target price reached
  POSITION_CLOSED: 'position_closed',        // Auto-close executed
  PRICE_PREDICTION: 'price_prediction',      // New ML prediction available
  PORTFOLIO_UPDATE: 'portfolio_update',      // Holdings synced
  MARKET_OPEN: 'market_open',                // Market opened with recommendations
  NEWS_SENTIMENT: 'news_sentiment',          // Significant sentiment change
  PAYMENT_SUCCESS: 'payment_success',        // Subscription activated
  SYSTEM: 'system'                           // System announcements
};
```

#### Notification Schema
```javascript
{
  _id: ObjectId,
  userId: ObjectId (indexed),
  type: String,
  title: String,
  body: String,
  data: {
    symbol: String,
    price: Number,
    action: String,
    url: String
  },
  priority: "high" | "normal" | "low",
  read: Boolean,
  actionTaken: Boolean,
  createdAt: Date (indexed),
  expiresAt: Date
}
```

#### UI Layout
```
┌────────────────────────────────────────────────────────────┐
│  🔔 Notifications (3 unread)                      [Mark All]│
├────────────────────────────────────────────────────────────┤
│  Filters: [All] [Alerts] [Predictions] [Portfolio]         │
├────────────────────────────────────────────────────────────┤
│  ┌──────────────────────────────────────────────────────┐ │
│  │ 🔴 RELIANCE Alert                        2 mins ago  │ │
│  │ Price ₹2,450 approaching stop-loss ₹2,300           │ │
│  │ [View Stock] [Dismiss]                               │ │
│  └──────────────────────────────────────────────────────┘ │
│  ┌──────────────────────────────────────────────────────┐ │
│  │ ✅ Position Closed                       5 mins ago  │ │
│  │ TCS sold at ₹3,850 (Auto-close triggered)           │ │
│  │ [View Portfolio]                                     │ │
│  └──────────────────────────────────────────────────────┘ │
│  ┌──────────────────────────────────────────────────────┐ │
│  │ 🤖 New Prediction                        1 hour ago  │ │
│  │ INFY predicted close: ₹1,665 (82% confidence)       │ │
│  │ [View Details]                                       │ │
│  └──────────────────────────────────────────────────────┘ │
│  ┌──────────────────────────────────────────────────────┐ │
│  │ 📊 Portfolio Synced                      2 hours ago │ │
│  │ 5 holdings updated from Angel One                   │ │
│  │ [View Portfolio]                                     │ │
│  └──────────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────────┘
```

#### Notification Actions
```javascript
// Quick actions from notifications
const notificationActions = {
  alert_triggered: [
    { label: 'View Stock', action: 'navigate', url: '/stock/:symbol' },
    { label: 'Close Position', action: 'sell', confirm: true },
    { label: 'Dismiss', action: 'dismiss' }
  ],
  position_closed: [
    { label: 'View Portfolio', action: 'navigate', url: '/portfolio' },
    { label: 'View Order', action: 'navigate', url: '/orders/:orderId' }
  ],
  price_prediction: [
    { label: 'View Details', action: 'navigate', url: '/stock/:symbol' },
    { label: 'Add to Watchlist', action: 'watchlist' }
  ]
};
```

---

## Razorpay Integration Flow

### Payment Flow Diagram
```
User clicks "Upgrade to Premium"
        │
        ▼
Frontend: POST /api/payments/create-order
        │
        ▼
Backend: Create Razorpay Order
        │
        └─→ amount: 49900 (₹499.00)
        └─→ currency: INR
        └─→ receipt: order_[timestamp]
        │
        ▼
Return order_id to Frontend
        │
        ▼
Frontend: Open Razorpay Checkout
        │
        └─→ User enters payment details
        └─→ User completes payment
        │
        ▼
Razorpay: Payment Success
        │
        ├─→ Frontend: payment_id, order_id, signature
        │
        └─→ Backend Webhook: payment.captured event
        │
        ▼
Frontend: POST /api/payments/verify
        │
        └─→ payment_id, order_id, signature
        │
        ▼
Backend: Verify Signature
        │
        └─→ HMAC SHA256 verification
        │
        ▼
Update User Subscription
        │
        └─→ tier: "premium"
        └─→ expiresAt: Date.now() + 30 days
        └─→ razorpaySubscriptionId: payment_id
        │
        ▼
Return Success Response
        │
        ▼
Frontend: Show Success Message
        └─→ Redirect to Dashboard
```


### Razorpay Checkout Code
```javascript
// Frontend (React)
const handleUpgrade = async () => {
  try {
    // Create order
    const { data } = await api.post('/payments/create-order', {
      plan: 'premium',
      amount: 49900 // ₹499.00 in paise
    });
    
    // Razorpay options
    const options = {
      key: import.meta.env.VITE_RAZORPAY_KEY_ID,
      amount: data.amount,
      currency: data.currency,
      name: 'Stock Market AI',
      description: 'Premium Subscription',
      order_id: data.orderId,
      handler: async (response) => {
        // Verify payment
        const verifyRes = await api.post('/payments/verify', {
          razorpay_payment_id: response.razorpay_payment_id,
          razorpay_order_id: response.razorpay_order_id,
          razorpay_signature: response.razorpay_signature
        });
        
        if (verifyRes.data.success) {
          toast.success('Subscription activated!');
          navigate('/dashboard');
        }
      },
      prefill: {
        name: user.name,
        email: user.email,
        contact: user.phone
      },
      theme: {
        color: '#3B82F6'
      }
    };
    
    const razorpay = new window.Razorpay(options);
    razorpay.open();
  } catch (error) {
    toast.error('Payment failed. Please try again.');
  }
};
```

---

## Firebase Cloud Messaging Setup

### FCM Integration Steps
```javascript
// 1. Initialize Firebase (frontend)
import { initializeApp } from 'firebase/app';
import { getMessaging, getToken, onMessage } from 'firebase/messaging';

const firebaseConfig = {
  apiKey: import.meta.env.VITE_FIREBASE_API_KEY,
  projectId: import.meta.env.VITE_FIREBASE_PROJECT_ID,
  messagingSenderId: import.meta.env.VITE_FIREBASE_SENDER_ID,
  appId: import.meta.env.VITE_FIREBASE_APP_ID
};

const app = initializeApp(firebaseConfig);
const messaging = getMessaging(app);

// 2. Request permission and get token
export const requestNotificationPermission = async () => {
  try {
    const permission = await Notification.requestPermission();
    if (permission === 'granted') {
      const token = await getToken(messaging, {
        vapidKey: import.meta.env.VITE_FIREBASE_VAPID_KEY
      });
      
      // Send token to backend
      await api.post('/notifications/fcm-token', { token });
      return token;
    }
  } catch (error) {
    console.error('FCM permission error:', error);
  }
};

// 3. Listen for foreground messages
onMessage(messaging, (payload) => {
  console.log('Foreground message:', payload);
  
  // Show notification
  new Notification(payload.notification.title, {
    body: payload.notification.body,
    icon: '/logo.png',
    badge: '/badge.png'
  });
});
```


### Backend FCM Service
```javascript
// Backend (Node.js)
const admin = require('firebase-admin');

// Initialize Firebase Admin
admin.initializeApp({
  credential: admin.credential.cert({
    projectId: process.env.FIREBASE_PROJECT_ID,
    privateKey: process.env.FIREBASE_PRIVATE_KEY.replace(/\\n/g, '\n'),
    clientEmail: process.env.FIREBASE_CLIENT_EMAIL
  })
});

// Send notification
async function sendNotification(userId, notification) {
  try {
    // Get user's FCM token from database
    const user = await User.findById(userId);
    if (!user.fcmToken) {
      console.log('User has no FCM token');
      return;
    }
    
    const message = {
      token: user.fcmToken,
      notification: {
        title: notification.title,
        body: notification.body
      },
      data: notification.data || {},
      android: {
        priority: 'high',
        notification: {
          sound: 'default',
          channelId: 'stock_alerts'
        }
      },
      apns: {
        payload: {
          aps: {
            sound: 'default',
            badge: 1
          }
        }
      }
    };
    
    const response = await admin.messaging().send(message);
    console.log('Notification sent:', response);
    return response;
  } catch (error) {
    console.error('FCM send error:', error);
    
    // If token is invalid, remove it
    if (error.code === 'messaging/invalid-registration-token') {
      await User.findByIdAndUpdate(userId, { fcmToken: null });
    }
  }
}

// Example: Send stop-loss alert
async function sendStopLossAlert(userId, stock, currentPrice, stopLoss) {
  await sendNotification(userId, {
    title: `⚠️ ${stock.symbol} Alert`,
    body: `Price ₹${currentPrice} approaching stop-loss ₹${stopLoss}`,
    data: {
      type: 'stop_loss_alert',
      symbol: stock.symbol,
      currentPrice: currentPrice.toString(),
      stopLoss: stopLoss.toString()
    }
  });
  
  // Also save to notification history
  await Notification.create({
    userId,
    type: 'alert_triggered',
    title: `${stock.symbol} Alert`,
    body: `Price ₹${currentPrice} approaching stop-loss ₹${stopLoss}`,
    data: { symbol: stock.symbol, price: currentPrice },
    priority: 'high',
    read: false
  });
}

// Example: Send position closed notification
async function sendPositionClosedNotification(userId, stock, price, quantity) {
  await sendNotification(userId, {
    title: `✅ Position Closed`,
    body: `${stock.symbol}: ${quantity} shares sold at ₹${price}`,
    data: {
      type: 'position_closed',
      symbol: stock.symbol,
      price: price.toString(),
      quantity: quantity.toString()
    }
  });
  
  await Notification.create({
    userId,
    type: 'position_closed',
    title: 'Position Closed',
    body: `${stock.symbol}: ${quantity} shares sold at ₹${price}`,
    data: { symbol: stock.symbol, price, quantity },
    priority: 'high',
    read: false
  });
}

// Example: Send daily recommendations
async function sendDailyRecommendations(userId, recommendations) {
  const topPicks = recommendations.slice(0, 3).map(r => r.symbol).join(', ');
  
  await sendNotification(userId, {
    title: `🤖 Today's Top Picks`,
    body: `AI recommends: ${topPicks}`,
    data: {
      type: 'market_open',
      recommendations: JSON.stringify(recommendations)
    }
  });
  
  await Notification.create({
    userId,
    type: 'market_open',
    title: "Today's Top Picks",
    body: `AI recommends: ${topPicks}`,
    data: { recommendations },
    priority: 'normal',
    read: false
  });
}
```

### Multi-Device FCM Support
```javascript
// Support multiple devices per user
async function sendNotificationToAllDevices(userId, notification) {
  const user = await User.findById(userId);
  
  if (!user.fcmTokens || user.fcmTokens.length === 0) {
    console.log('User has no FCM tokens');
    return;
  }
  
  const messages = user.fcmTokens.map(token => ({
    token,
    notification: {
      title: notification.title,
      body: notification.body
    },
    data: notification.data || {},
    android: {
      priority: 'high',
      notification: {
        sound: 'default',
        channelId: 'stock_alerts'
      }
    }
  }));
  
  try {
    const response = await admin.messaging().sendEach(messages);
    console.log(`Sent to ${response.successCount} devices`);
    
    // Remove invalid tokens
    const invalidTokens = [];
    response.responses.forEach((resp, idx) => {
      if (!resp.success && 
          resp.error.code === 'messaging/invalid-registration-token') {
        invalidTokens.push(user.fcmTokens[idx]);
      }
    });
    
    if (invalidTokens.length > 0) {
      await User.findByIdAndUpdate(userId, {
        $pull: { fcmTokens: { $in: invalidTokens } }
      });
    }
    
    return response;
  } catch (error) {
    console.error('FCM batch send error:', error);
  }
}
```

---

## Responsive Design Breakpoints

```css
/* Mobile First Approach */

/* Extra Small (Mobile) */
@media (min-width: 320px) {
  .container { padding: 1rem; }
  .stock-card { width: 100%; }
  .chart { height: 300px; }
}

/* Small (Large Mobile) */
@media (min-width: 640px) {
  .container { padding: 1.5rem; }
  .stock-card { width: calc(50% - 1rem); }
  .chart { height: 350px; }
}

/* Medium (Tablet) */
@media (min-width: 768px) {
  .container { padding: 2rem; }
  .sidebar { display: block; }
  .stock-card { width: calc(33.333% - 1rem); }
  .chart { height: 400px; }
}

/* Large (Desktop) */
@media (min-width: 1024px) {
  .container { max-width: 1200px; margin: 0 auto; }
  .chart { height: 500px; }
}

/* Extra Large (Wide Desktop) */
@media (min-width: 1280px) {
  .container { max-width: 1400px; }
  .chart { height: 600px; }
}
```

---

## Accessibility Guidelines


### WCAG 2.1 AA Compliance Checklist

```
✓ Color Contrast
  - Text: 4.5:1 minimum ratio
  - Large text (18pt+): 3:1 minimum
  - Use green/red with additional indicators (icons, text)

✓ Keyboard Navigation
  - All interactive elements focusable
  - Logical tab order
  - Visible focus indicators
  - Escape key closes modals

✓ Screen Reader Support
  - Semantic HTML (header, nav, main, article)
  - ARIA labels for icons
  - Alt text for images
  - Live regions for price updates

✓ Forms
  - Label associated with inputs
  - Error messages announced
  - Required fields indicated
  - Clear validation feedback

✓ Responsive
  - Text scales up to 200%
  - No horizontal scrolling
  - Touch targets 44x44px minimum
```

### ARIA Labels Example
```jsx
// Stock card with accessibility
<article 
  className="stock-card"
  aria-label={`${stock.name} stock information`}
>
  <h3>{stock.symbol}</h3>
  <p className="price" aria-label={`Current price ${stock.price} rupees`}>
    ₹{stock.price}
  </p>
  <p 
    className={stock.change >= 0 ? 'positive' : 'negative'}
    aria-label={`${stock.change >= 0 ? 'Up' : 'Down'} ${Math.abs(stock.changePercent)} percent`}
  >
    <span aria-hidden="true">{stock.change >= 0 ? '🟢' : '🔴'}</span>
    {stock.changePercent}%
  </p>
  <button 
    aria-label={`View details for ${stock.name}`}
    onClick={() => navigate(`/stock/${stock.symbol}`)}
  >
    View Details
  </button>
</article>

// Live price updates
<div 
  role="status" 
  aria-live="polite" 
  aria-atomic="true"
  className="sr-only"
>
  {priceUpdate && `${priceUpdate.symbol} price updated to ${priceUpdate.price}`}
</div>
```

---

## Performance Budgets

### Target Metrics
```
First Contentful Paint (FCP): < 1.5s
Largest Contentful Paint (LCP): < 2.5s
Time to Interactive (TTI): < 3.5s
Cumulative Layout Shift (CLS): < 0.1
First Input Delay (FID): < 100ms

Bundle Sizes:
- Initial JS: < 200KB (gzipped)
- Initial CSS: < 50KB (gzipped)
- Total page weight: < 1MB
```

### Optimization Techniques
```javascript
// 1. Code splitting
const Dashboard = lazy(() => import('./pages/Dashboard'));
const StockDetail = lazy(() => import('./pages/StockDetail'));
const Portfolio = lazy(() => import('./pages/Portfolio'));

// 2. Image optimization
<img 
  src={stock.logo} 
  alt={stock.name}
  loading="lazy"
  width="48"
  height="48"
/>

// 3. Debounce search
const debouncedSearch = useMemo(
  () => debounce((query) => searchStocks(query), 300),
  []
);

// 4. Virtualized lists (react-window)
import { FixedSizeList } from 'react-window';

<FixedSizeList
  height={600}
  itemCount={stocks.length}
  itemSize={80}
  width="100%"
>
  {({ index, style }) => (
    <StockRow stock={stocks[index]} style={style} />
  )}
</FixedSizeList>
```

---

## Conclusion

This design document provides a comprehensive blueprint for building the Stock Market AI Platform. The architecture is optimized for rapid development during a hackathon while maintaining scalability and best practices.

Key design decisions:
- Monolithic backend for simplicity (Express.js or FastAPI)
- MongoDB for flexible schema during rapid iteration
- Redis for high-performance caching
- WebSocket for real-time price updates
- Pre-trained ML models for quick deployment
- Free-tier services to minimize costs
- Mobile-first responsive design
- Accessibility built-in from the start

The system is designed to handle 1000+ concurrent users with sub-second response times and 99.5% uptime during market hours.
