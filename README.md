# FinCast — AI-Powered Stock Market Platform

## Hackathon Submission: AI for Bharat

This repository contains the requirements and design documentation for FinCast, an AI-powered stock market platform submitted for the AI for Bharat hackathon.

---

##  Live MVP

 **[https://fincast.web.app/](https://fincast.web.app/)**

---

##  Repository Contents

| File | Description |
|------|-------------|
| [requirements.md](requirements.md) | Detailed product requirements — user stories, acceptance criteria, features, non-functional requirements, risk assessment, and competitive analysis |
| [design.md](design.md) | Comprehensive system design — architecture, database schema, API design, ML pipeline, UI/UX wireframes, security, performance, and deployment |

---

##  What is FinCast?

FinCast is an AI-powered stock market platform that provides:

- AI Stock Recommendations — LSTM + XGBoost ensemble model with FinBERT sentiment analysis
- Real-Time Market Data — Live charts with technical indicators (RSI, MACD, SMA)
- ML Price Predictions — Predicted closing price with confidence scores and risk analysis
- Smart Alerts — FCM push notifications with auto-close position based on ML predictions
- Portfolio Management — Track holdings, P&L, sector allocation with Angel One integration
- News Sentiment — FinBERT-powered sentiment analysis on financial news

---

##  Tech Stack

| Layer | Technology |
|-------|------------|
| Frontend | React + TypeScript, TradingView Charts |
| Backend | Node.js + Express, FastAPI (ML) |
| Database | MongoDB Atlas, Redis Cloud |
| ML Models | LSTM, XGBoost, FinBERT |
| Notifications | Firebase Cloud Messaging (FCM) |
| Payments | Razorpay |
| Broker API | Angel One SmartAPI |
| Hosting | Firebase Hosting |

---

## 👥 Team

- **Manimaran K** — Team Leader  
- **Navin S** — Team Member  
- **Pavan K** — Team Member  
- **Nitin T** — Team Member  

Built with for the AI for Bharat hackathon.
