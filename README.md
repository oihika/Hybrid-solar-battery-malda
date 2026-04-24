# Hybrid-solar-battery-malda
A Python-based simulation and optimization of a hybrid solar–battery energy system using NASA irradiance data for Malda, featuring load modeling, battery state-of-charge analysis, and grid dependency reduction.

Here is a clean, GitHub-ready README.md with badges, visuals, and your updated name (Oihika Arpit). You can paste it directly into your repository.
Markdown
# ⚡ Optimization of Hybrid Solar-Battery System for Malda

![Python](https://img.shields.io/badge/Python-3.10+-blue.svg)
![NASA API](https://img.shields.io/badge/NASA-POWER%20API-red.svg)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen.svg)
![License](https://img.shields.io/badge/License-MIT-yellow.svg)
![Research](https://img.shields.io/badge/Domain-Renewable%20Energy-orange.svg)

---

## 🌞 Project Overview

This project simulates and optimizes a **hybrid solar photovoltaic (PV) and battery storage system** for a small town in **Malda, India**, using real irradiance data from the NASA POWER API and a synthetic residential load profile.

The objective is to reduce **grid dependency** and identify optimal sizing of **solar panels and battery storage**.

---

## 📊 System Architecture
🌍 NASA Solar Irradiance Data
                 ↓
        ☀️ Solar PV Model
                 ↓
 ⚡ Hourly Energy Balance Simulation
    ↙                      ↘
🔋 Battery Storage        🔌 Grid Backup ↘                      ↙ 🏠 Load Demand Fulfillment

---

## 🧠 Methodology

- 📡 NASA POWER API for real solar irradiance data  
- 🏠 Synthetic 24-hour residential load profile  
- ☀️ Solar PV generation modeling  
- 🔋 Battery SOC-based charge/discharge logic  
- 📉 Grid usage minimization analysis  
- ⚙️ Parameter variation for optimization  

---

## 📁 Project Structure
📦 solar-hybrid-malda ┣ 📜 main.py ┣ 📊 graph1_energy.png ┣ 📊 graph2_battery_grid_time.png ┣ 📊 graph3_solar_battery_load.png ┣ 📜 README.md

---

## 📈 Results & Visualizations

### ☀️ Solar vs Load vs Grid Usage
![Graph 1](graph1_energy.png)

---

### 🔋 Battery Sizing vs Grid Dependency
![Graph 2](graph2_battery_grid_time.png)

---

### ⚡ Solar Panel Sizing vs Battery Usage
![Graph 3](graph3_solar_battery_load.png)

---

## 🔍 Key Insights

- Increasing battery capacity significantly reduces grid dependency  
- Solar oversizing improves daytime generation but requires storage balance  
- Optimal system requires **combined PV + battery sizing strategy**  
- Night-time demand remains the main driver of grid usage  

---

## ⚙️ Technologies Used

- Python 🐍  
- NumPy, Pandas  
- Matplotlib 📊  
- NASA POWER API 🌍  

---

## 🚀 How to Run

```bash
git clone https://github.com/your-username/solar-hybrid-malda.git
cd solar-hybrid-malda
pip install -r requirements.txt
python main.py

🔮 Future Improvements
Real smart meter load integration
AI-based demand forecasting
Cost optimization (LCOE analysis)
Hybrid solar-wind system expansion
Real-time microgrid dashboard (Streamlit)

👨‍💻 Author
Oihika Arpit
Electronics & Communication Engineering
Renewable Energy & Data Science Enthusiast.

📜 License
This project is licensed under the MIT License.
