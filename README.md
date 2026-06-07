# 🌊 Floody

**Real-Time Indian Flood Prediction & Alert System**

> Production-ready flood forecasting platform using live Open-Meteo weather data, ML-based risk prediction, and real-time alerts for Indian states and districts.

![Architecture](https://img.shields.io/badge/Architecture-Microservices-blue) ![Data](https://img.shields.io/badge/Data-Real_Time-green) ![API](https://img.shields.io/badge/API-Open_Meteo-orange) ![ML](https://img.shields.io/badge/ML-XGBoost-red)

---

## 🏗️ Architecture

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Next.js 16    │────▶│  Express API    │────▶│  FastAPI + ML   │
│   Frontend      │     │  Backend        │     │  AI Cortex      │
│   :3000         │     │  :4000          │     │  :8000          │
└─────────────────┘     └────────┬────────┘     └────────┬────────┘
                                 │                        │
                        ┌────────▼────────┐     ┌────────▼────────┐
                        │  PostgreSQL     │     │  Open-Meteo API │
                        │  Database       │     │  (Free, No Key) │
                        └─────────────────┘     └─────────────────┘
```

## ⚡ Quick Start

### Option 1: One-Click (Windows)
```bash
start.cmd
```

### Option 2: Docker Compose
```bash
docker-compose up --build
```

### Option 3: Manual
```bash
# Terminal 1 — AI Cortex
cd ai-cortex
pip install -r requirements.txt
python ml/train.py              # Train ML model (one-time)
uvicorn main:app --reload --port 8000

# Terminal 2 — Backend
cd backend
npm install
npx ts-node index.ts

# Terminal 3 — Frontend
cd frontend-command
npm install
npm run dev
```

Open **http://localhost:3000** in your browser.

---

## 📡 Data Sources

| Source | Data | Cost | Latency |
|--------|------|------|---------|
| **Open-Meteo** | Rainfall, soil moisture, temperature, discharge | Free, no key | Real-time |
| **NDMA SACHET** | Official disaster alerts (planned) | Free | 15-30 min |
| **CWC India-WRIS** | River gauge data (planned) | Free | 1-6 hrs |

## 🧠 ML Model

- **Algorithm**: XGBoost Regressor (200 estimators, depth 6)
- **Features**: rainfall_24h, rainfall_7d, soil_moisture, river_discharge, humidity, temperature, wind_speed, weather_code
- **Training**: Synthetic data modeled on INDOFLOODS patterns (5000 samples)
- **Fallback**: Physics-based rule engine when model unavailable
- **Output**: Flood probability (0-1), Risk level (LOW/MODERATE/HIGH/SEVERE), Risk score (0-10)

Train the model:
```bash
cd ai-cortex && python ml/train.py
```

## 🔌 API Endpoints

### AI Cortex (:8000)
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | Service health check |
| `/predict` | POST | ML flood risk prediction |
| `/weather?lat=&lon=` | GET | Real-time weather data |
| `/discharge?lat=&lon=` | GET | River discharge data |
| `/alerts?lat=&lon=` | GET | Flood alerts for location |
| `/predict/bulk` | POST | Bulk predictions (map) |

### Backend (:4000)
| Endpoint | Method | Description |
|----------|--------|-------------|
| `/health` | GET | API health check |
| `/auth/signup` | POST | User registration |
| `/auth/login` | POST | User authentication |
| `/risk/calculate` | POST | Proxied risk prediction |
| `/api/weather/:lat/:lon` | GET | Weather proxy |
| `/api/alerts/:lat/:lon` | GET | Alerts proxy |
| `/api/predict/bulk` | POST | Bulk prediction proxy |

## 🖥️ Frontend Features

- **Citizen Dashboard**: Live risk card, real flood probability, evacuation routes, SOS alerts, family notification, state/district analysis with 10+ Indian states
- **NDRF Command Map**: Click-anywhere live predictions, bulk risk visualization, auto-refreshing markers, real-time telemetry feed
- **22 Indian Language Support**: Hindi, Bengali, Telugu, Tamil, Marathi, and more
- **3-Tier Fallback**: Backend → AI Cortex → Direct Open-Meteo (works even if servers are down)

## 📁 Project Structure

```
floodsense-ai/
├── ai-cortex/                 # Python ML Engine
│   ├── main.py                # FastAPI endpoints
│   ├── services/
│   │   ├── weather_service.py # Open-Meteo integration
│   │   └── alert_service.py   # Alert generation
│   ├── ml/
│   │   ├── model.py           # ML prediction engine
│   │   └── train.py           # Model training script
│   ├── requirements.txt
│   └── Dockerfile
├── backend/                   # Node.js API Server
│   ├── index.ts               # Express + Socket.IO
│   ├── prisma/schema.prisma   # Database schema
│   ├── package.json
│   ├── tsconfig.json
│   └── Dockerfile
├── frontend-command/          # Next.js Frontend
│   ├── src/
│   │   ├── app/               # Next.js pages
│   │   ├── components/        # React components
│   │   ├── data/statesData.ts # Indian states data
│   │   └── lib/api.ts         # API client (3-tier fallback)
│   ├── package.json
│   └── Dockerfile
├── docker-compose.yml         # Full stack orchestration
├── start.cmd                  # Windows one-click start
├── start.sh                   # Linux/Mac start
└── README.md
```

## 🔧 Environment Variables

Copy the example files and adjust:
```bash
cp backend/.env.example backend/.env
cp frontend-command/.env.local.example frontend-command/.env.local
```

| Variable | Default | Description |
|----------|---------|-------------|
| `PORT` | 4000 | Backend port |
| `AI_CORTEX_URL` | http://localhost:8000 | AI Cortex URL |
| `DATABASE_URL` | postgresql://... | PostgreSQL connection |
| `NEXT_PUBLIC_API_URL` | http://localhost:4000 | Frontend → Backend |
| `NEXT_PUBLIC_AI_CORTEX_URL` | http://localhost:8000 | Frontend → AI Cortex |

