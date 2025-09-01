# 📊 Opioid Overdose Data Analysis (Connecticut, 2014–2018)

## 🔹 Project Overview
This project analyzes **opioid-related overdose deaths** in Connecticut between **2014 and 2018** using SQL.  
The objective was to perform **exploratory data analysis (EDA)** and uncover temporal, demographic, geographic, and substance-related patterns contributing to the opioid crisis.  

---

## 🔹 Dataset
- **Table Name:** `death.overdose`  
- **Records:** 5091
- **Features include:**  
  - Date of death  
  - Demographics (Sex, Race, Age)  
  - Substances involved (Heroin, Fentanyl, Cocaine, etc.)  
  - Geographic details (Residence City, Location)  

---

## 🔹 SQL Analysis Performed
### 1. Total Records
```sql
SELECT COUNT(*) AS total_records FROM death.overdose;


### 2. Deaths by year
SELECT 
    YEAR(STR_TO_DATE(Date, '%m/%d/%Y')) AS death_year,
    COUNT(*) AS deaths_count
FROM death.overdose
GROUP BY death_year
ORDER BY death_year;

### 3. Most common drugs involved in deaths
SELECT 
    'Heroin' AS drug, SUM(Heroin) AS count FROM death.overdose UNION ALL
    SELECT 'Cocaine', SUM(Cocaine) FROM death.overdose UNION ALL
    SELECT 'Fentanyl', SUM(Fentanyl) FROM death.overdose UNION ALL
    SELECT 'Oxycodone', SUM(Oxycodone) FROM death.overdose UNION ALL
    SELECT 'Benzodiazepine', SUM(Benzodiazepine) FROM death.overdose UNION ALL
    SELECT 'Methadone', SUM(Methadone) FROM death.overdose
ORDER BY count DESC;

### 4. Demographics analysis
SELECT 
    Sex,
    Race,
    Round(AVG(Age), 2) AS average_age,
    COUNT(*) AS deaths_count
FROM death.overdose
WHERE Sex IS not NULL
GROUP BY Sex, Race
ORDER BY deaths_count DESC;

### 5. Top 5 cities with most drug overdose deaths
SELECT 
    ResidenceCity,
    COUNT(*) AS deaths_count
FROM death.overdose
WHERE ResidenceCity IS NOT NULL 
    AND ResidenceCity != ''
GROUP BY ResidenceCity
ORDER BY deaths_count DESC
LIMIT 5;

### 6. Most common drug combinations
SELECT
    CONCAT_WS('+',
        CASE WHEN Heroin = 1 THEN 'Heroin' END,
        CASE WHEN Fentanyl = 1 THEN 'Fentanyl' END,
        CASE WHEN Cocaine = 1 THEN 'Cocaine' END
    ) AS drug_combination,
    COUNT(*) AS cases
FROM death.overdose
WHERE (Heroin + Fentanyl + Cocaine) >= 2
GROUP BY drug_combination
ORDER BY cases DESC
LIMIT 10;

### 7. Deaths by location type
SELECT 
    Location,
    COUNT(*) AS deaths_count
FROM death.overdose
WHERE Location IS NOT NULL
GROUP BY Location
ORDER BY deaths_count DESC;

### 8. Monthly trend of deaths
SELECT 
    YEAR(STR_TO_DATE(Date, '%m/%d/%Y')) AS year,
    MONTH(STR_TO_DATE(Date, '%m/%d/%Y')) AS month,
    COUNT(*) AS monthly_deaths
FROM death.overdose
GROUP BY year, month
ORDER BY year, month;
