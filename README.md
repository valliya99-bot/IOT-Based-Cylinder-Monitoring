# IOT-Based-Cylinder-Monitoring
This project aims to solve real-life problems of LPG gas management by monitoring gas levels and detecting leaks using Python and sensor-based technology. It provides real-time tracking, alerts, and usage analysis to improve safety and efficiency.
# Smart LPG Gas Monitoring & Leak Detection System

import time
from datetime import datetime
import random

# ---------------- USER INPUT ----------------
MAX_WEIGHT = float(input("Enter full cylinder weight (kg): "))
EMPTY_WEIGHT = float(input("Enter empty cylinder weight (kg): "))

# ---------------- CONFIGURATION ----------------
LOW_GAS_THRESHOLD = 20   # Percentage
LEAK_THRESHOLD = 1.5     # Abnormal usage multiplier

# ---------------- STORAGE ----------------
history = []
last_weight = None
last_time = None

# ---------------- SENSOR SIMULATION ----------------
def read_sensor(base_weight):
    """
    Simulates sensor reading by reducing weight gradually
    """
    variation = random.uniform(0.1, 0.5)
    return max(base_weight - variation, EMPTY_WEIGHT)

# ---------------- CALCULATIONS ----------------
def calculate_gas(weight):
    gas_left = max(weight - EMPTY_WEIGHT, 0)
    total_gas = MAX_WEIGHT - EMPTY_WEIGHT
    percent = (gas_left / total_gas) * 100
    return round(gas_left, 2), round(percent, 2)

def calculate_usage(current, previous, hours):
    if previous is None or hours <= 0:
        return 0
    return round((previous - current) / hours, 3)

# ---------------- ALERT SYSTEM ----------------
def check_low_gas(percent):
    if percent <= LOW_GAS_THRESHOLD:
        print("⚠ ALERT: Low Gas! Refill soon.")

def check_leak(usage, avg_usage=0.4):
    if usage > avg_usage * LEAK_THRESHOLD:
        print("🚨 WARNING: Possible Gas Leak Detected!")

# ---------------- MAIN PROGRAM ----------------
print("\nSmart LPG Monitoring System Started...\n")

# Initial weight taken from user (real cylinder weight)
current_weight = float(input("Enter current cylinder weight (kg): "))

for _ in range(5):  # simulate readings

    current_time = datetime.now()

    # Simulate next reading
    current_weight = read_sensor(current_weight)

    gas_left, percent = calculate_gas(current_weight)

    # Time difference
    if last_time:
        hours = (current_time - last_time).total_seconds() / 3600
    else:
        hours = 0

    usage = calculate_usage(current_weight, last_weight, hours)

    # Store data
    history.append({
        "time": current_time.strftime("%Y-%m-%d %H:%M:%S"),
        "weight": current_weight,
        "gas_left": gas_left,
        "percent": percent,
        "usage": usage
    })

    # Display (like mobile output)
    print("----- LPG STATUS -----")
    print("Time:", current_time.strftime("%H:%M:%S"))
    print("Weight:", round(current_weight, 2), "kg")
    print("Gas Left:", gas_left, "kg")
    print("Percentage:", percent, "%")
    print("Usage:", usage, "kg/hr")

    # Alerts
    check_low_gas(percent)
    check_leak(usage)

    print("----------------------\n")

    # Update previous values
    last_weight = current_weight
    last_time = current_time

    time.sleep(2)

print("Monitoring Completed.")
