import requests
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# -----------------------------
# USER PARAMETERS
# -----------------------------
initial_soc_percent = 50
battery_unit = 5   # kWh
efficiency = 0.18
base_area = 10

# -----------------------------
# NASA DATA
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
# LOAD PROFILE
# -----------------------------
load = np.array([
    2,2,2,2,3,4,5,6,5,4,3,3,
    3,3,4,5,6,7,8,7,5,4,3,2
])

df = df.iloc[:24]
df['load'] = load

# -----------------------------
# SIMULATION FUNCTION (WITH SOC)
# -----------------------------
def simulate(area, batteries):
    capacity = batteries * battery_unit
    soc = capacity * (initial_soc_percent / 100)

    soc_list = []
    grid_list = []
    solar_list = []

    for i in range(24):
        solar = df['irradiance'][i] * efficiency * area / 1000
        demand = df['load'][i]

        if solar >= demand:
            soc = min(capacity, soc + (solar - demand))
            grid = 0
        else:
            deficit = demand - solar
            if soc >= deficit:
                soc -= deficit
                grid = 0
            else:
                grid = deficit - soc
                soc = 0

        soc_list.append(soc)
        grid_list.append(grid)
        solar_list.append(solar)

    return np.array(solar_list), np.array(grid_list), np.array(soc_list)

# -----------------------------
# RUN SIMULATION
# -----------------------------
solar, grid, soc = simulate(base_area, 5)

# -----------------------------
# GRAPH 1: ENERGY FLOW
# -----------------------------
plt.figure()
plt.plot(df['hour'], solar, label='Solar Output')
plt.plot(df['hour'], df['load'], label='Load')
plt.plot(df['hour'], grid, label='Grid')
plt.xlabel("Hour")
plt.ylabel("kW")
plt.title("Energy Flow Analysis")
plt.legend()
plt.savefig("graph1_energy.png")

# -----------------------------
# GRAPH 2: BATTERY SOC
# -----------------------------
plt.figure()
plt.plot(df['hour'], soc, marker='o')
plt.xlabel("Hour")
plt.ylabel("Battery SOC (kWh)")
plt.title("Battery State of Charge (SOC) vs Time")
plt.grid()
plt.savefig("graph2_soc.png")

# -----------------------------
# GRAPH 3: BATTERY SIZE IMPACT
# -----------------------------
battery_options = [1, 3, 5, 7, 10]

plt.figure()

for b in battery_options:
    _, grid_temp, _ = simulate(base_area, b)
    plt.plot(range(24), grid_temp, label=f"{b} Batteries")

plt.xlabel("Hour")
plt.ylabel("Grid Usage (kW)")
plt.title("Battery Size vs Grid Dependency")
plt.legend()
plt.savefig("graph3_battery_grid.png")

print("✅ All graphs generated successfully")