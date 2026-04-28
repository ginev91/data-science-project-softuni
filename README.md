# Thermal Sensitivity of National Power Grids

**Analyzing the Mathematical Relationship between Meteorological Factors and Electrical Load in Bulgaria (BG)**  

**Author:** Aleksandar Ginev  
**Project Type:** Technical Report / Data Science Research  

---

## Abstract

This research investigates the correlation between ambient temperature and electrical load within the Bulgarian (BG) power grid. Using high-resolution hourly data from the ENTSO-E Transparency Platform and Meteostat, we construct a mathematical model to quantify *Thermal Sensitivity*—defined as the change in Megawatts (MW) per degree Celsius.

Results demonstrate that weather-driven factors explain a significant percentage of load volatility, providing a roadmap for strategic energy arbitrage.

---

## 1. Introduction & Problem Formulation

Grid operators must maintain a real-time balance between supply and demand. This project quantifies the "chaos factor" introduced by meteorological variables.

The objective is to merge energy and weather datasets to define the *Point of Minimum Sensitivity* and model the impact of wind chill on the Bulgarian grid.

---

## 2. Data Acquisition & Vetting

Data was retrieved from two primary sources:

- **ENTSO-E:** Actual Total Load (BG) – 17,544 hourly rows  
- **Meteostat:** Hourly Meteorological Data (Sofia Station) – 17,544 hourly rows  

The vetting process involved:
- Linear Interpolation to handle missing values  
- UTC Synchronization to ensure a perfect 1:1 hourly match between load and weather  

---

## 3. Mathematical Foundations

We utilize several mathematical tools to linearize the grid's response:

- **The U-Curve:** Dividing demand into Heating, *Dead Band* (Comfort), and Cooling zones  
- **Degree Days ($HDD$, $CDD$):** Measuring thermal deficit and surplus  
- **Wind Chill Index ($T_{\text{wc}}$):** Accounting for convective heat loss in winter  
- **Pearson Correlation:** Verifying statistical strength before modeling  

---

## 4. Data Pre-processing & Consolidation

A Temporal Inner Join was performed to synchronize the datasets.

We engineered four key features:

- **HDD/CDD:** Based on $17^\circ\text{C}$ (heating) and $22^\circ\text{C}$ (cooling) baselines  
- **$T_{\text{wc}}$:** Wind chill–adjusted temperature  
- **is\_weekend:** Binary feature to isolate industrial demand drops  

---

## 5. Exploratory Data Analysis (EDA)

EDA confirmed three critical hypotheses:

- **Correlation:** Features like $HDD$ show strong positive correlation with load  
- **U-Curve Visualization:** Load rises at temperature extremes  
- **Weekend Effect:** Industrial scheduling shifts the demand curve downward  

---

## 6. Modeling & Hypothesis Testing

We implemented a **Multivariate 2nd-Degree Polynomial Regression** to capture non-linear thermal response.

- **Target Variable:** Total Load (MW)  
- **$R^2$ Score:** $0.4182$ (explains 41.8% of variance)  
- **RMSE:** $708.60 \ \text{MW}$  

---

## 7. Predictive Strategy: Seasonal Energy Storage

We integrate predicted load over the winter period:

- **Total Winter Energy Deficit:** $9{,}942.48 \ \text{GWh}$  
- **Peak Storage Requirement:** $4{,}337.07 \ \text{MW}$  

This supports the use of assets like the Chaira Pumped Storage Plant to shift summer solar surplus into winter demand.

---

## 8. Results & Interpretation

The analysis yielded a **Thermal Sensitivity ($X$)**:

$$
X = 99.02 \ \text{MW}/^\circ\text{C}
$$

- **Operational Impact:**  
  A $10^\circ\text{C}$ cold snap requires:

  $$
  \Delta P = 990.20 \ \text{MW}
  $$

  This is approximately equivalent to one reactor at Kozloduy Nuclear Power Plant.

- **Baseload:**  
  The non-thermal floor was identified as:

  $$
  P_{\text{base}} = 3430.09 \ \text{MW}
  $$

---

## 9. Conclusion & References

The model validates that Bulgarian grid volatility is a predictable function of weather and scheduling.

Future work includes developing a population-weighted temperature index across multiple cities to refine the $99.02 \ \text{MW}/^\circ\text{C}$ sensitivity coefficient.

---

## References

- ENTSO-E: Load Data Portal  
- Meteostat: Sofia Weather Station  
- Ministry of Energy (BG): Sustainable Energy Development Strategy  
- NIMH: Climatic Normals and Thermal Comfort Standards  
- Pedregosa, F. et al. (2011). *Scikit-learn: Machine Learning in Python*
