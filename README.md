# Kolkata Weather Prediction Model

## Overview

This project implements a machine learning-based weather prediction system for Kolkata, India. It leverages 37+ years of historical weather data (1985-2024) to predict average daily temperature and precipitation probability. The system provides a web interface for users to query predictions for any given date.

## Quick Summary

A machine learning application that predicts Kolkata's daily weather using 37+ years of historical data. Built with Random Forest models for temperature regression and rain classification, the system analyzes seasonal patterns through cyclical day-of-year encoding. The cleaned dataset (14,425 records) is processed to remove 46.6% missing precipitation values using linear interpolation. Users interact via a Flask web interface, entering dates in DD-MM format to receive temperature forecasts in Celsius and rain probability percentages. Employs 200-estimator Random Forest ensembles optimized for robust, non-linear weather pattern recognition.

## Project Structure

```
Weather prediction model/
├── kolkata_data.csv                    # Historical weather dataset (37+ years)
├── clean_data.py                       # Data cleaning module
├── clean.py                            # Data cleaning pipeline script
├── predict.py                          # Legacy command-line interface
├── frontend/
│   ├── app.py                          # Flask web server and ML backend
│   ├── templates/
│   │   └── index.html                  # Web interface
│   ├── static/
│   │   └── style.css                   # UI styling
│   └── README.md                       # Frontend documentation
└── README.md                           # This file
```

## Dataset

- **Source**: Historical weather data for Kolkata, India
- **Time Period**: 1985 - 2024 (37+ years)
- **Records**: 14,425 daily observations
- **Features**:
  - DATE: Date of observation
  - TAVG: Average daily temperature (°C)
  - PRCP: Daily precipitation (mm)

## Data Preprocessing

### Step 1: Missing Value Analysis
- Original dataset contained 6,729 missing values in PRCP column (46.6%)
- TAVG column had no missing values
- Missing values were distributed across the entire time period

### Step 2: Data Cleaning Pipeline

The data undergoes the following cleaning steps in `clean_data.py`:

1. **Temporal Sorting**: Data is sorted chronologically by date
2. **Linear Interpolation**: Missing precipitation values are filled using linear interpolation based on surrounding data points with `limit_direction='both'`
3. **Forward/Backward Fill**: Remaining gaps at the beginning or end of the dataset are filled using forward fill followed by backward fill
4. **Final Validation**: Any rows still containing NaN values are removed

**Result**: Cleaned dataset contains 14,424 records with zero missing values

### Step 3: Feature Engineering

For both regression and classification tasks:

1. **Day of Year Extraction**: Calendar day-of-year is extracted (1-365)
2. **Cyclical Encoding**: To capture seasonal patterns, day-of-year is converted to cyclical coordinates:
   - `doy_sin = sin(2π × day_of_year / 365)`
   - `doy_cos = cos(2π × day_of_year / 365)`
   
This approach preserves the circular nature of the calendar (e.g., Dec 31 and Jan 1 are adjacent).

### Step 4: Label Creation

- **Temperature Label (Regression)**: TAVG values are used directly as continuous targets
- **Rain Label (Classification)**: Binary classification where:
  - Rain = 1 if daily precipitation > 0.2 mm
  - Rain = 0 if daily precipitation ≤ 0.2 mm

## Machine Learning Models

### Model 1: Temperature Prediction (Regression)

**Algorithm**: Random Forest Regressor

**Hyperparameters**:
- Number of estimators: 200
- Random state: 42 (for reproducibility)

**Input Features**: 
- doy_sin (cyclical sine component)
- doy_cos (cyclical cosine component)

**Output**: 
- Average daily temperature in Celsius (continuous value)

**Rationale**: Random Forest is robust to seasonal variations and captures non-linear relationships between day-of-year and temperature without requiring manual feature scaling.

### Model 2: Rain Prediction (Classification)

**Algorithm**: Random Forest Classifier

**Hyperparameters**:
- Number of estimators: 200
- Class weight: balanced (to handle potential class imbalance)
- Random state: 42 (for reproducibility)

**Input Features**: 
- doy_sin (cyclical sine component)
- doy_cos (cyclical cosine component)

**Output**: 
- Probability of precipitation (0-100%)
- Binary classification: "Yes" (≥30% probability) or "No" (<30% probability)

**Rationale**: Random Forest Classifier effectively models precipitation patterns based on seasonal cycles and provides probability estimates for decision-making.

## Technical Stack

- **Backend**: Python 3.x
- **Web Framework**: Flask
- **Machine Learning**: scikit-learn
- **Data Processing**: pandas, NumPy
- **Frontend**: HTML5, CSS3, JavaScript
- **Server**: Flask development server

## Installation & Setup

### Prerequisites
- Python 3.7 or higher
- pip package manager

### Step 1: Install Dependencies

```bash
pip install flask pandas numpy scikit-learn
```

### Step 2: Run the Application

Navigate to the frontend directory and start the Flask server:

```bash
cd frontend
python app.py
```

### Step 3: Access the Web Interface

Open your web browser and navigate to:
```
http://127.0.0.1:5000
```

## Usage

1. **Enter a Date**: Input a date in DD-MM format (e.g., 23-07 for July 23rd)
2. **Get Predictions**: Click the "Predict" button
3. **View Results**:
   - Average Temperature: Expected temperature in Celsius
   - Rain Probability: Likelihood of precipitation as a percentage
   - Rain Expected: "Yes" (green) if probability ≥ 30%, "No" (red) if < 30%

## Model Performance Characteristics

- **Training Data**: 80% of cleaned dataset (11,539 records)
- **Test Data**: 20% of cleaned dataset (2,885 records)
- **Model Type**: Ensemble-based (Random Forest with 200 decision trees)
- **Prediction Basis**: Seasonal patterns derived from 37+ years of historical data

## Limitations

1. **Seasonal Dependencies**: Models primarily rely on day-of-year features; other meteorological factors (pressure, humidity, wind) are not included
2. **Historical Bias**: Predictions reflect historical climate patterns and may not account for climate change effects
3. **Local Applicability**: Models are trained specifically on Kolkata data and may not generalize to other regions
4. **Short-term Only**: Predictions are based on historical averages for the given date, not real-time atmospheric conditions

## Files and Modules

### clean_data.py
Provides the `clean_pipeline()` function for data loading and preprocessing.

### app.py
Main Flask application containing:
- Data loading and model training
- API endpoint for predictions (/predict)
- HTML template serving
- Static file management

### index.html
Web interface with form input and results display.

### style.css
Responsive UI styling with gradient backgrounds and animations.

## Future Enhancements

- Incorporate additional weather variables (humidity, pressure, wind speed)
- Implement time-series forecasting models (LSTM, ARIMA)
- Add multi-day forecast capability
- Deploy to cloud platform (AWS, Azure, Google Cloud)
- Create REST API for third-party integrations
- Add confidence intervals for predictions

## Data Privacy and Attribution

This weather data represents historical observations for Kolkata, India. Proper attribution should be given to the original data source when publishing or redistributing results.

## License

This project is open-source and available for educational and research purposes.


---

**Last Updated**: July 2026
**Dataset Coverage**: 1985 - 2024
**Total Records**: 14,425 daily observations
