# Thermal Sensitivity of National Power Grids
### Analyzing the Mathematical Relationship between Meteorological Factors and Electrical Load in Bulgaria (BG)

**Author:** Aleksandar Ginev  
**Project Type:** Technical Report / Data Science Research  

---

## 1. Introduction & Problem Formulation
### The Problem
Electrical energy grids must maintain a perfect, real-time balance between supply and demand. Any significant deviation can lead to frequency instability or catastrophic blackouts. While industrial cycles provide a baseline, **weather** acts as the primary "chaos factor" that shifts consumer behavior unpredictably.

### The Goal
The objective of this project is to construct a mathematical model that quantifies the "Thermal Sensitivity" of the Bulgarian power grid. Specifically, we aim to calculate how a $1^\circ\\text{C}$ change in ambient temperature impacts the total electrical load in Megawatts (MW).

### Strategic Prediction: Seasonal Storage
A critical secondary goal is to use this sensitivity model to predict **Seasonal Energy Deficits**. By understanding how much extra energy we need during extreme winters, we can calculate the capacity required for summer energy storage (e.g., pumped-storage hydro or batteries), effectively "storing the summer sun" to ensure cheaper, more stable winters.

---

## 2. Data Acquisition & Vetting (The 2 Sources)
To meet the project requirements, data was consolidated from two independent providers:

1.  **Source 1 (Meteorological):** Hourly historical data retrieved via the **Meteostat API**. Key features include Temperature ($^\circ\text{C}$), Humidity (%), and Wind Speed (km/h).
2.  **Source 2 (Grid Load):** Hourly "Actual Total Load" data from the **ENTSO-E Transparency Platform** for the Bulgaria (BG) control area.

### Data Vetting & Validation
* **Unit Consistency:** Ensuring all timestamps are in UTC and power is measured in Megawatts (MW).
* **Missing Values:** Identifying "NaN" entries caused by sensor failures and applying linear interpolation.
* **Daylight Savings:** Adjusting for the 23-hour and 25-hour days in March and October to prevent timestamp misalignment.

---

## 3. Mathematical Foundations
### 3.1 Piecewise Functions (HDD & CDD)
Energy demand is not linear. We use **Heating Degree Days (HDD)** and **Cooling Degree Days (CDD)** to model the "Base Temperature" ($T_b$):
* $HDD = \max(0, T_b - T_{actual})$
* $CDD = \max(0, T_{actual} - T_b)$

### 3.2 Derivatives and Sensitivity
By taking the derivative of our final regression model $\frac{dL}{dT}$, we can find the **Point of Minimum Sensitivity**. 

### 3.3 Seasonal Storage Math: Cumulative Deficit
To predict storage needs, we integrate the load prediction $L(T)$ over the winter period $W$:
$$S_{GWh} = \frac{1}{1000} \int_{t \in W} \max(0, L(T_t) - G_{baseline}) \, dt$$
*(Where $G_{baseline}$ is the cheap renewable/nuclear generation capacity).*

---

## 4. Data Pre-processing & Consolidation
### Merging Strategy
We perform a **Temporal Inner Join** on the hourly timestamps to pair MW measurements with specific weather observations.

### Feature Engineering: The Wind Chill Index
To improve winter accuracy, we calculate the **Wind Chill Index ($T_{wc}$)**:
$$T_{wc} = 13.12 + 0.6215T - 11.37v^{0.16} + 0.3965Tv^{0.16}$$

---

## 5. Exploratory Data Analysis (EDA)
* **The "U-Curve":** Scatter plots revealing how load increases during both extreme cold and extreme heat.
* **Seasonality:** Mathematically isolating the "Weekend Effect" to ensure industrial drops don't skew thermal sensitivity data.

---

## 6. Modeling & Hypothesis Testing
### Polynomial Regression
We fit a 2nd-degree polynomial to capture the non-linear relationship:
$$y = \\beta_0 + \\beta_1x + \\beta_2x^2 + \epsilon$$

### Loss Function (MSE)
We minimize the **Mean Squared Error** to optimize the model:
$$MSE = \frac{1}{n} \sum_{i=1}^{n} (y_i - \hat{y}_i)^2$$

---

## 7. Results & Interpretation
* **Sensitivity Metric:** "For every $1^\circ\text{C}$ drop below $15^\circ\text{C}$, the Bulgarian load increases by $X$ MW."
* **Storage Prediction:** Calculation of the GWh surplus needed from summer production to cover the winter thermal-load spike.

---

## 8. Conclusion & References
### Summary
The model confirms that temperature and wind chill are highly predictive of grid load. By quantifying this, Bulgaria can better plan the utilization of assets like the **Chaira Pumped Storage Plant** to arbitrage energy between seasons.

### References
1.  ENTSO-E Transparency Platform - [https://transparency.entsoe.eu/](https://transparency.entsoe.eu/)
2.  Meteostat Developers Portal - [https://dev.meteostat.net/](https://dev.meteostat.net/)
3.  Standard ISO 8601 for Date and Time Representations.
