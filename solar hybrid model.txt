
//Optimization of hybrid solar-battery system for a small town in Malda using real load + irradiance data//

import requests
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# -----------------------------
# USER PARAMETERS
# -----------------------------
initial_soc_percent = 50
battery_unit = 5      # kWh per battery
panel_unit = 5        # m² step size
efficiency = 0.18

# -----------------------------
# 1. NASA DATA (Malda)
# -----------------------------
lat, lon = 25.0, 88.1

url = f"https://power.larc.nasa.gov/api/temporal/hourly/point?parameters=ALLSKY_SFC_SW_DWN&community=RE&longitude={lon}&latitude={lat}&start=20230101&end=20230102&format=JSON"

data = requests.get(url).json()
irr = data['properties']['parameter']['ALLSKY_SFC_SW_DWN']

df = pd.DataFrame(list(irr.items()), columns=['time', 'irradiance'])
df['time'] = pd.to_datetime(df['time'], format='%Y%m%d%H')
df['hour'] = df['time'].dt.hour
df['irradiance'] = df['irradiance'].fillna(0)

# -----------------------------
# 2. LOAD PROFILE
# -----------------------------
load = np.array([
    2,2,2,2,3,4,5,6,5,4,3,3,
    3,3,4,5,6,7,8,7,5,4,3,2
])

df = df.iloc[:24]
df['load'] = load

# -----------------------------
# BASE SOLAR MODEL (GRAPH 1 INPUT)
# -----------------------------
base_area = 10

df['solar_base'] = df['irradiance'] * efficiency * base_area / 1000

# -----------------------------
# STORAGE FUNCTION
# -----------------------------
def simulate(area, batteries):
    capacity = batteries * battery_unit
    soc = capacity * (initial_soc_percent / 100)

    grid_list = []
    battery_usage = []

    for i in range(24):
        solar = df['irradiance'][i] * efficiency * area / 1000
        demand = df['load'][i]

        if solar >= demand:
            soc = min(capacity, soc + (solar - demand))
            grid = 0
            batt_use = 0
        else:
            deficit = demand - solar
            if soc >= deficit:
                soc -= deficit
                grid = 0
            else:
                grid = deficit - soc
                soc = 0

            batt_use = deficit

        grid_list.append(grid)
        battery_usage.append(batt_use)

    return np.array(grid_list), np.array(battery_usage)

# -----------------------------
# GRAPH 1 (UNCHANGED)
# -----------------------------
grid_base, batt_base = simulate(base_area, 5)

plt.figure()
plt.plot(df['hour'], df['solar_base'], label='Solar Generation')
plt.plot(df['hour'], df['load'], label='Load Demand')
plt.plot(df['hour'], grid_base, label='Grid Usage')
plt.xlabel("Hour")
plt.ylabel("Power (kW)")
plt.title("Solar vs Load vs Grid Usage (Base Case)")
plt.legend()
plt.savefig("graph1_energy.png")

# -----------------------------
# GRAPH 2
# Battery Sizing vs Grid Dependency vs Time
# -----------------------------
battery_options = [1, 3, 5, 7, 10]

plt.figure()

for b in battery_options:
    grid, _ = simulate(base_area, b)
    plt.plot(range(24), grid, label=f"{b} Batteries")

plt.xlabel("Hour (0–23)")
plt.ylabel("Grid Dependency (kW)")
plt.title("Battery Sizing vs Grid Dependency (24H)")
plt.legend()
plt.savefig("graph2_battery_grid_time.png")

# -----------------------------
# GRAPH 3
# Solar Sizing vs Load vs Battery Usage
# -----------------------------
panel_sizes = [5, 10, 15, 20, 30, 40]

plt.figure()

for area in panel_sizes:
    grid, batt = simulate(area, 5)

    plt.plot(range(24), batt, label=f"{area} m²")

plt.plot(range(24), df['load'], '--', label="Load Demand")

plt.xlabel("Hour (0–23)")
plt.ylabel("Energy (kW equivalent)")
plt.title("Solar Panel Sizing vs Load vs Battery Usage")
plt.legend()
plt.savefig("graph3_solar_battery_load.png")

print("\n✅ All graphs generated successfully")