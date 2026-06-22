<div align="center">

#  Ballistix

### Advanced Trajectory Prediction & Simulation Platform

[![Live Demo](https://img.shields.io/badge/_Live_Demo-ballistix.vercel.app-blue?style=for-the-badge)](https://ballistix.vercel.app)
[![API](https://img.shields.io/badge/_API-ballistix.onrender.com-green?style=for-the-badge)](https://ballistix.onrender.com/docs)

<p align="center">
  <strong>A full-stack application combining physics-based simulation with machine learning<br/>for accurate projectile trajectory prediction and real-time visualization.</strong>
</p>

[Live Demo](https://ballistix.vercel.app) • [API Docs](https://ballistix.onrender.com/docs) • [Features](#-features) • [Quick Start](#-quick-start) • [API Reference](#-api-reference)

</div>

---

##  Overview


**Ballistix** is an advanced platform for simulating and predicting projectile and missile trajectories, combining physics-based simulation with machine learning and a modern, interactive UI.

- **Modern UI**: Dark theme, animated hero, glassmorphism, responsive design
- **Navigation**: Footer and CTA buttons on the About page now switch views (Home, Ballistic, Analytics, Settings, Launch Simulator)
- **Physics Engine**: Runge-Kutta 4 integration with ISA atmospheric model
- **Machine Learning**: Random Forest model with 98% R² accuracy
- **Real-time Visualization**: Animated trajectory plots, multiple projectile types
- **Missile Presets**: 20+ real-world missile profiles

---


##  Features

| Feature | Description |
|---------|-------------|
|  **Modern UI** | Dark theme, animated hero, glassmorphism, responsive |
|  **Footer Navigation** | Home, Ballistic, Analytics, Settings (About page) |
|  **Launch Simulator** | CTA button launches trajectory simulation |
|  **Physics Simulation** | Newtonian mechanics, altitude-dependent gravity, air density |
|  **ML Predictions** | Random Forest, instant impact predictions |
|  **Analytics Dashboard** | Compare physics vs ML, track accuracy |
|  **Missile Database** | ICBMs, SLBMs, cruise missiles, real specs |
|  **3D Globe View** | Visualize long-range trajectories |
|  **User Sessions** | Per-user history and settings |

---

##  Quick Start

### Prerequisites

```bash
Node.js >= 18.0.0
Python >= 3.10
npm >= 9.0.0
```

### Option 1: Use Live Version

 **Frontend**: https://ballistix.vercel.app  
 **API Docs**: https://ballistix.onrender.com/docs

### Option 2: Run Locally

```bash
# Clone repository
git clone https://github.com/Revu-15/projectile-prediction.git
cd projectile-prediction

# Backend setup
cd backend
python -m venv venv
venv\Scripts\activate        # Windows
# source venv/bin/activate   # macOS/Linux
pip install -r requirements.txt
uvicorn app.main:app --reload --port 8000

# Frontend setup (new terminal)
cd frontend
npm install
npm run dev
```

**Access:**
- Frontend: http://localhost:5173
- API: http://localhost:8000
- API Docs: http://localhost:8000/docs

---

##  Project Structure


```
projectile-prediction/
├── backend/
│   ├── app/
│   │   ├── main.py            # FastAPI API endpoints
│   │   ├── sim.py             # Physics engine (RK4 + ISA)
│   │   ├── model_store.py     # ML model management
│   │   ├── database.py        # SQLAlchemy config
│   │   └── db_models.py       # Data models
│   ├── models/
│   │   ├── rf_model.joblib    # Trained Random Forest
│   │   ├── scaler.joblib      # Feature scaler
│   │   └── model_metadata.json
│   ├── requirements.txt
│   └── runtime.txt
│   └── static/
│       └── ballistic/         # Static previews
│
├── frontend/
│   ├── src/
│   │   ├── app.jsx            # Main app & routing
│   │   ├── Ballistic.jsx      # Missile simulation
│   │   ├── analytics.jsx      # Analytics dashboard
│   │   ├── About.jsx          # Modern landing page (with navigation)
│   │   ├── Settings.jsx       # User preferences
│   │   ├── Auth.jsx           # Authentication
│   │   ├── AnimationCanvas.jsx
│   │   └── styles.css         # Global styles
│   ├── index.html
│   ├── vercel.json
│   └── package.json
│
├── DEPLOYMENT.md
├── LICENSE
└── README.md
```

---

##  Physics Engine

The simulation uses **Runge-Kutta 4 (RK4)** numerical integration with:

### Equations of Motion
```
dx/dt = vₓ
dy/dt = vᵧ
dvₓ/dt = -k·(ρ/ρ₀)·|v|·vₓ
dvᵧ/dt = -g(h) - k·(ρ/ρ₀)·|v|·vᵧ
```

### Atmospheric Model (ISA)
| Altitude | Layer | Model |
|----------|-------|-------|
| 0-11 km | Troposphere | Linear temp decrease |
| 11-20 km | Lower Stratosphere | Isothermal (216.65K) |
| 20-32 km | Upper Stratosphere | Linear temp increase |
| 32-84 km | Mesosphere | Exponential decay |
| >84 km | Thermosphere | Negligible density |

### Physical Constants
```python
R_EARTH = 6,371,000 m      # Earth radius
G = 6.67430×10⁻¹¹          # Gravitational constant
M_EARTH = 5.972×10²⁴ kg    # Earth mass
ρ₀ = 1.225 kg/m³           # Sea-level air density
```

---

##  API Reference

### Base URL
```
Production: https://ballistix.onrender.com
Local: http://localhost:8000
```

### Endpoints

#### Health Check
```http
GET /health
```
```json
{
  "status": "ok",
  "model_loaded": true,
  "api_version": "1.0.0"
}
```

#### Predict Trajectory
```http
POST /predict
Content-Type: application/json

{
  "v0": 300,
  "angle": 45,
  "drag": 0.01,
  "dt": 0.01,
  "release_height": 0
}
```

**Response:**
```json
{
  "xs": [0.0, 2.1, 4.2, ...],
  "ys": [0.0, 1.5, 2.9, ...],
  "impact_physics": 9174.32,
  "impact_ml": 9168.45,
  "max_height": 2295.68,
  "max_range": 9174.32,
  "flight_time": 61.24,
  "trajectory_points": 6124
}
```

#### Model Info
```http
GET /model/info
```

#### Retrain Model
```http
POST /retrain
Content-Type: application/json

{
  "n_samples": 1200,
  "use_forest": true
}
```

### cURL Examples

```bash
# Health check
curl https://ballistix.onrender.com/health

# Run prediction
curl -X POST https://ballistix.onrender.com/predict \
  -H "Content-Type: application/json" \
  -d '{"v0":300,"angle":45,"drag":0.01,"dt":0.01,"release_height":0}'
```

---

##  Machine Learning

### Model Details
| Parameter | Value |
|-----------|-------|
| Algorithm | Random Forest Regressor |
| Trees | 100 estimators |
| R² Score | ~0.99 |
| Training Samples | 1,200 |
| Features | v0, angle, drag, release_height |
| Target | Impact distance |

### Training Pipeline
```
Physics Simulation → Generate Dataset → Train/Test Split (80/20)
                                              ↓
                                        StandardScaler
                                              ↓
                                      Random Forest Fit
                                              ↓
                                    Cross-Validation (5-fold)
                                              ↓
                                      Save Model + Metadata
```

---

##  Missile Database

Pre-configured profiles with verified specifications:

### ICBMs
| Missile | Country | Range | Speed |
|---------|---------|-------|-------|
| Minuteman III | 🇺🇸 USA | 13,000 km | Mach 23 |
| Trident II D5 | 🇺🇸 USA | 11,300 km | Mach 24 |
| RS-28 Sarmat | 🇷🇺 Russia | 18,000 km | Mach 20.7 |
| DF-41 | 🇨🇳 China | 12,000-15,000 km | Mach 25 |
| Agni-V | 🇮🇳 India | 5,500-8,000 km | Mach 24 |

### SLBMs
| Missile | Country | Range | Platform |
|---------|---------|-------|----------|
| Trident II | 🇺🇸 USA | 11,300 km | Ohio-class |
| R-30 Bulava | 🇷🇺 Russia | 8,300 km | Borei-class |
| JL-3 | 🇨🇳 China | 10,000+ km | Type 096 |

### Cruise Missiles
| Missile | Country | Range | Speed |
|---------|---------|-------|-------|
| Tomahawk | 🇺🇸 USA | 2,500 km | Mach 0.75 |
| BrahMos | 🇮🇳/🇷🇺 | 450 km | Mach 2.8 |
| Kalibr | 🇷🇺 Russia | 2,500 km | Mach 0.8 |

*Sources: CSIS Missile Threat, FAS, Jane's Defence*

---

##  Tech Stack


### Frontend
- **React 18** - UI framework
- **Vite 5** - Build tool
- **Recharts** - Data visualization
- **Three.js** - 3D globe
- **Lucide React** - Icons
- **Axios** - HTTP client
- **Modern CSS** - Glassmorphism, gradients, responsive

### Backend
- **FastAPI** - Web framework
- **Uvicorn** - ASGI server
- **Scikit-learn** - ML models
- **NumPy/SciPy** - Numerical computing
- **Pandas** - Data processing
- **SQLAlchemy** - ORM
- **Pydantic** - Data validation

### Deployment
- **Vercel** - Frontend hosting
- **Render** - Backend hosting
- **GitHub** - Version control

---

##  Navigation & Usage

- The About page features a modern landing design with animated hero, glassmorphism, and a dark theme.
- Footer navigation buttons (Home, Ballistic, Analytics, Settings) and the "Launch Simulator" CTA are fully functional, switching views using the `onNavigate` prop.
- Navigation view names: `trajectory`, `ballistic`, `analytics`, `settings`.

---

---

##  Performance

| Metric | Value |
|--------|-------|
| API Response (predict) | < 100ms |
| Physics Simulation | ~100k steps/sec |
| ML Inference | < 5ms |
| Frontend Bundle | 645 KB (gzipped: 183 KB) |
| Lighthouse Score | 85+ |

---

##  Security

- ✅ Input validation with Pydantic
- ✅ CORS configured per environment
- ✅ No sensitive data in client storage
- ✅ HTTPS enforced in production
- ⚠️ Rate limiting recommended for production

---

##  Contributing

1. Fork the repository
2. Create feature branch (`git checkout -b feature/amazing-feature`)
3. Commit changes (`git commit -m 'Add amazing feature'`)
4. Push to branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---


<div align="center">


**[Live Demo](https://ballistix.vercel.app)** • **[API Docs](https://ballistix.onrender.com/docs)** • 

</div>
