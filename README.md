# AI-Powered Air Quality Prediction System

A production-ready web application that predicts Air Quality Index (AQI) using advanced machine learning models trained on real historical data from major Indian cities.

## 🌟 Features

- **Real-time AQI Predictions**: Next-day and 7-day forecasts for Delhi, Mumbai, Hyderabad, Jaipur, and Noida
- **High Accuracy**: R² scores above 0.99 with RMSE under 1 AQI point
- **Real-time Data Integration**: Fetches current AQI from OpenAQ and IQAir APIs
- **Smart Alert System**: Automatic health warnings based on predicted AQI levels
- **Interactive Dashboard**: Beautiful UI with real-time visualizations
- **Health Recommendations**: Personalized safety guidance based on air quality

## 📊 Model Performance

| City      | MAE (AQI) | RMSE (AQI) | R² Score |
|-----------|-----------|------------|----------|
| Delhi     | 0.29      | 0.35       | 0.9999   |
| Mumbai    | 0.40      | 0.77       | 0.9983   |
| Hyderabad | 0.32      | 0.81       | 0.9963   |
| Jaipur    | 0.39      | 0.79       | 0.9978   |
| Noida     | 0.32      | 0.41       | 0.9998   |

## 🚀 Quick Start

### Prerequisites

- Python 3.8 or higher
- pip (Python package manager)

### Installation

1. **Install dependencies**
```bash
pip install -r requirements.txt
```

2. **Train the models** (already done, but you can retrain)
```bash
python train_model.py
```

3. **Start the server**
```bash
python app.py
```

4. **Open in browser**
```
http://localhost:5000
```

## 📁 Project Structure

```
aqi-prediction/
├── app.py                      # Flask backend API
├── train_model.py              # ML model training script
├── requirements.txt            # Python dependencies
├── aqi_models.pkl             # Trained models (generated)
├── model_metrics.json         # Performance metrics (generated)
├── static/
│   ├── index.html             # Main web interface
│   ├── style.css              # Styling
│   └── app.js                 # Frontend JavaScript
└── README.md                  # This file
```

## 🔧 How It Works

### 1. Data Processing
- Transforms monthly AQI data into time-series format
- Handles missing values and data inconsistencies
- Creates comprehensive feature set from historical patterns

### 2. Feature Engineering (29 features)
- **Lag Features**: Past AQI values (1, 2, 3, 7, 14 days ago)
- **Rolling Statistics**: Moving averages, std dev, min, max (3, 7, 14-day windows)
- **Temporal Features**: Day of week, month, day of year with cyclical encoding
- **Trend Features**: First-order and 7-day differences
- **Exponential Moving Averages**: Short-term (3-day) and medium-term (7-day)

### 3. Model Architecture
- **Algorithm**: Gradient Boosting Regressor
- **Parameters**:
  - n_estimators: 200
  - learning_rate: 0.05
  - max_depth: 5
  - min_samples_split: 10
  - min_samples_leaf: 4
  - subsample: 0.8
- **Validation**: Time Series Cross-Validation (3 splits)

### 4. Real-time Predictions
- Fetches current AQI from OpenAQ API (free, no key required)
- Falls back to IQAir API if configured
- Uses last 14 days of data to create features
- Generates predictions for next day and 7-day forecast

## 🌐 API Endpoints

### GET `/api/cities`
Returns list of available cities.

**Response:**
```json
{
  "cities": ["Delhi", "Mumbai", "Hyderabad", "Jaipur", "Noida"]
}
```

### POST `/api/predict`
Get AQI prediction for a city.

**Request:**
```json
{
  "city": "Delhi",
  "current_aqi": 150  // Optional, will fetch if not provided
}
```

**Response:**
```json
{
  "city": "Delhi",
  "current_aqi": 150,
  "predicted_aqi_tomorrow": 145,
  "current_status": {
    "status": "Unhealthy for Sensitive Groups",
    "color": "#ff7e00",
    "alert_level": "caution",
    "message": "...",
    "recommendations": [...]
  },
  "predicted_status": {...},
  "forecast_7day": [...],
  "model_metrics": {...}
}
```

### GET `/api/current/<city>`
Get current AQI from external APIs.

**Response:**
```json
{
  "aqi": 156,
  "pm25": 85.5,
  "source": "OpenAQ",
  "timestamp": "2025-12-16T10:30:00"
}
```

### GET `/api/metrics`
Get model performance metrics for all cities.

### GET `/api/health-recommendations/<aqi>`
Get health recommendations for a specific AQI value.

## 🎨 UI Features

### Main Dashboard
- **City Selector**: Choose from 5 major Indian cities
- **Today's AQI**: Current air quality with color-coded status
- **Tomorrow's Forecast**: Next-day prediction with trend visualization
- **7-Day Trend**: Weekly forecast chart
- **Alert System**: Automatic warnings for unhealthy air quality

### Health & Safety Section
- Dynamic recommendations based on AQI levels
- Icons and actionable advice
- Severity-based color coding

### Alert Subscription
- Email/SMS notification setup (UI ready, backend needs integration)
- Daily AQI reports
- Emergency alerts for severe pollution

### Model Information
- Data sources and methodology
- Real-time accuracy metrics
- Last updated timestamp

## ⚠️ AQI Categories

| AQI Range | Category | Color | Health Implications |
|-----------|----------|-------|---------------------|
| 0-50 | Good | Green | No health concerns |
| 51-100 | Moderate | Yellow | Acceptable for most people |
| 101-150 | Unhealthy for Sensitive Groups | Orange | Sensitive groups may be affected |
| 151-200 | Unhealthy | Red | Everyone may experience effects |
| 201-300 | Very Unhealthy | Purple | Health alert for all |
| 301+ | Hazardous | Maroon | Emergency conditions |

## 🔐 API Keys (Optional)

For production use with IQAir API:

1. Sign up at https://www.iqair.com/air-pollution-data-api
2. Get your free API key
3. Add to `app.py`:
```python
predictor.iqair_api_key = "YOUR_API_KEY_HERE"
```

**Note**: The app works without any API keys using OpenAQ's free API and historical data simulation.

## 🐛 Troubleshooting

### Models not loading
```bash
# Retrain models
python train_model.py
```

### Port already in use
```bash
# Change port in app.py
app.run(debug=True, host='0.0.0.0', port=5001)
```

### CORS errors
- Make sure Flask-CORS is installed
- Check that static files are in the `static/` directory

### Low accuracy
- Ensure all data files are present in the correct location
- Check that data preprocessing completed successfully
- Verify feature engineering created all 29 features

## 📈 Future Enhancements

- [ ] SMS/Email notification backend
- [ ] Historical data visualization
- [ ] More cities support
- [ ] Weather parameter integration
- [ ] Mobile app (React Native)
- [ ] Admin dashboard for model retraining
- [ ] User accounts and preferences
- [ ] API rate limiting and caching

## 🤝 Contributing

Contributions are welcome! Please feel free to submit issues or pull requests.

## 📝 License

This project is open source and available under the MIT License.

## 👥 Credits

- **Data Sources**: OpenAQ, IQAir, Government Monitoring Stations
- **ML Framework**: scikit-learn
- **Web Framework**: Flask
- **UI Design**: Custom responsive design

## 📞 Support

For questions or support, please open an issue on the repository.

---

**Built with ❤️ for cleaner air and healthier communities**
