# Thermal Sensitivity of National Power Grids

**Analyzing the Mathematical Relationship between Meteorological Factors and Electrical Load in Bulgaria (BG)**

**Author:** Aleksandar Ginev  
**Project Type:** Technical Report / Data Science Research  

---

## 1. Introduction & Problem Formulation

### The Problem

Electrical energy grids must maintain a perfect, real-time balance between supply and demand. Any significant deviation can lead to frequency instability or catastrophic blackouts. While industrial cycles provide a baseline, weather acts as the primary "chaos factor" that shifts consumer behavior unpredictably.

### The Goal

The objective of this project is to construct a mathematical model that quantifies the thermal sensitivity of the Bulgarian power grid. Specifically, we aim to calculate how a $1^\circ\text{C}$ change in ambient temperature impacts the total electrical load in Megawatts (MW).

### Strategic Prediction: Seasonal Storage

A critical secondary goal is to use this sensitivity model to predict seasonal energy deficits. By understanding the extra energy required during extreme winters, we calculate the capacity needed for summer energy storage (e.g., pumped-storage hydro or batteries), effectively "storing the summer sun" to ensure grid stability.

---

## 2. Data Acquisition & Vetting

To meet project requirements, data was consolidated from two independent providers:

- **Source 1 (Meteorological):** Hourly historical data for Sofia (Station 15614) via the Meteostat API. Features include Temperature ($^\circ\text{C}$), Humidity (%), and Wind Speed (km/h).
- **Source 2 (Grid Load):** Hourly "Actual Total Load" data from the ENTSO-E Transparency Platform for the Bulgarian (BG) control area.

### Data Validation Workflow

- **Unit Consistency:** Synchronization of all timestamps to UTC and power measurements to Megawatts (MW).  
- **Missing Values:** Identifying sensor failures and applying linear interpolation to maintain time-series continuity.  
- **Temporal Alignment:** Standardizing Year/Month/Day/Hour into an ISO-8601 index to perform a mathematical inner join.

---

## 3. Mathematical Foundations

### 3.1 Piecewise Functions (HDD & CDD)

Energy demand is non-linear. We use Heating Degree Days (HDD) and Cooling Degree Days (CDD) to model deviation from the base temperature ($T_b$):

$$
HDD = \max(0, T_b - T_{\text{actual}})
$$

$$
CDD = \max(0, T_{\text{actual}} - T_b)
$$

---

### 3.2 Seasonal Storage Math

To predict storage needs, we integrate the load prediction $L(T)$ over the winter period ($W$):

$$
S_{\text{GWh}} = \frac{1}{1000} \int_{t \in W} \max\left(0, L(T_t) - G_{\text{baseline}}\right)\, dt
$$

Where $G_{\text{baseline}}$ represents cheap renewable or nuclear generation capacity.

---

## 4. Modeling & Feature Engineering

- **Wind Chill Index ($T_{wc}$):** Applied to winter observations to account for convective heat loss, increasing heating demand beyond raw temperature estimates.  
- **The Weekend Effect:** A binary feature to isolate industrial demand drops and prevent schedule-driven noise from skewing the model.  
- **Polynomial Regression:** A 2nd-degree model capturing the "U-curve" relationship where demand rises at both temperature extremes.  

---

## 5. Results & Interpretation

### 5.1 Quantitative Results

- **Thermal Sensitivity:** 99.02 MW/$^\circ\text{C}$  
- **Baseload:** 3,430.09 MW  
- **Operational Impact:** A 10°C cold snap creates a demand spike of:

$$
\approx 990.20 \ \text{MW}
$$

This is roughly equivalent to the output of a 1,000 MW nuclear reactor.

---

### 5.2 The "Dead Band"

The analysis identified a "Dead Band" between 17°C and 22°C. In this range, the grid reaches peak efficiency with minimal weather-driven load, representing an optimal window for maintenance and stable pricing.

---

## 6. Conclusion & References

### Summary

The model confirms that temperature and wind chill are highly predictive of Bulgarian grid load. By quantifying the 99.02 MW/$^\circ\text{C}$ sensitivity, this research provides a roadmap for utilizing storage assets to bridge the gap between high-production summer months and high-demand winter periods.

---

### References

- ENTSO-E Transparency Platform — https://transparency.entsoe.eu/  
- Meteostat Developers Portal — https://dev.meteostat.net/  
- Ministry of Energy (Bulgaria) — Sustainable Energy Development Strategy  
- ISO 8601 Standard for Date and Time Representation  
