## Data Description

### 1. Overview
This dataset contains daily historical weather observations for **Vietnam’s 63 provinces and centrally governed cities** (based on the administrative structure before any provincial mergers).  
The data spans from **2021-01-01** to **2025-12-01**, and is designed primarily to support **tourism-related analytics**, providing detailed daily weather information across the country.

### 2. Source
Weather data is collected from the **Open-Meteo Historical Weather API**.  
Documentation: https://open-meteo.com/en/docs/historical-weather-api

### 3. Dataset Statistics
- **Rows:** 113,148
- **Columns:** 7  
- **Granularity:** 1 record = weather conditions of a province/city on a specific day

### 4. Column Description

| Column Name           | Type                   | Description |
|-----------------------|------------------------|-------------|
| **date**              | datetime64[ns, UTC]    | Timestamp of the weather observation (UTC). Converted from local time to standardized UTC format. |
| **location.name**     | object                 | Name of the province or centrally governed city in Vietnam. |
| **location.terrain**  | object                 | Type of geographical terrain (e.g., *đồng bằng*, *miền núi*, *cao nguyên*...). |
| **location.region**   | object                 | Vietnam's macro-region classification (e.g., *Đồng Bằng Sông Cửu Long*, *Bắc Trung Bộ*, *Tây Nguyên*...). |
| **location.lat**      | float64                | Latitude coordinate of the province/city center. |
| **location.lon**      | float64                | Longitude coordinate of the province/city center. |
| **weather_code**      | int64                  | WMO weather condition code (Table 4677). Each code maps to a specific weather state such as drizzle, rain, fog, clear sky, etc. |
| **weather_description** | object               | Human-readable weather condition derived from `weather_code` (e.g., *Light drizzle*, *Heavy rain*, *Sunny*). |
| **temp**              | float32                | Average daily temperature (°C). |
| **temp_min**          | float32                | Minimum temperature of the day (°C). |
| **temp_max**          | float32                | Maximum temperature of the day (°C). |
| **wind_speed**        | float32                | Average wind speed throughout the day (m/s). |
| **humidity**          | float32                | Average relative humidity (%) for the day. |
| **rain_sum**          | float32 (nullable)     | Total rainfall amount for the day (mm). Missing values occur for dates/locations where precipitation was not reported. |





### 5. Data Quality
- **Missing values:** 0  
- **Duplicate rows:** 0  
- **Consistency:** Weather codes follow standard WMO 4677 definitions, and date ranges are continuous for all provinces.

### 6. Notes
- Weather description is inferred programmatically from WMO codes, ensuring consistent categorization.  
- The dataset can be used for tourism analytics, trend analysis, climate pattern detection, and recommendation systems.
