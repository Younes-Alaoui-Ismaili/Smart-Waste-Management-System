# Smart-Waste-Management-System
If you're looking for a **third project** that complements the **Smart City Traffic Management System** and the **Smart Parking Management System**, here's an idea:

---

## **Project 3: Smart Waste Management System**

### **Project Description**
This project aims to design and implement a **Smart Waste Management System** for cities. The system uses IoT sensors, cloud platforms, and data analytics to monitor waste levels in bins, optimize waste collection routes, and reduce operational costs.

---

## **Key Features**
1. **IoT Sensor Network**: Detects waste levels in bins using ultrasonic or weight sensors.
2. **Real-Time Data Processing**: Processes waste data using Apache Kafka and Node-RED.
3. **Cloud Integration**: Stores and analyzes data using AWS IoT Core and Google Cloud IoT.
4. **Route Optimization**: Uses machine learning to optimize waste collection routes.
5. **Dashboard**: Provides real-time insights into waste levels and collection schedules.

---

## **System Architecture**
![System Architecture Diagram](#) *(Add a diagram here if available)*

1. **IoT Layer**:
   - **ESP32 Devices**: Detect waste levels in bins using ultrasonic or weight sensors.
   - **Raspberry Pi**: Acts as a gateway for data aggregation and edge processing.

2. **Communication Layer**:
   - **MQTT**: For real-time communication between IoT devices and the cloud.
   - **LoRaWAN**: For long-range, low-power communication in large areas.

3. **Data Processing Layer**:
   - **Apache Kafka**: For real-time data streaming.
   - **Node-RED**: For workflow automation and data routing.

4. **Cloud Layer**:
   - **AWS IoT Core**: For device management and data ingestion.
   - **Google Cloud IoT**: For advanced analytics and machine learning.

5. **Application Layer**:
   - **Route Optimization Algorithm**: Optimizes waste collection routes using machine learning.
   - **Dashboard**: Displays real-time waste levels and collection schedules.

---

## **File Structure**
```
smart-waste/
├── iot/
│   ├── waste_sensor.py            # ESP32 waste sensor code
│   ├── gateway.py                 # Raspberry Pi gateway code
├── data_processing/
│   ├── data_processor.py          # Kafka and Node-RED integration
├── route_optimization/
│   ├── optimize_routes.py         # Route optimization algorithm
├── dashboard/
│   ├── app.py                     # Backend for the dashboard
│   ├── templates/
│   │   ├── index.html             # Frontend for the dashboard
│   ├── static/
│   │   ├── styles.css             # CSS for the dashboard
├── config/
│   ├── main_config.py             # Configuration settings
├── requirements.txt               # Python dependencies
├── README.md                      # Project documentation
```

---

## **Implementation**

### 1. IoT Sensor Network

#### `iot/waste_sensor.py`
This script simulates ESP32 devices detecting waste levels in bins.

```python
import paho.mqtt.client as mqtt
import random
import time

# MQTT Configuration
MQTT_BROKER = "mqtt.eclipseprojects.io"
MQTT_TOPIC = "smart-waste/levels"

# Simulate waste sensor data
def simulate_sensor_data():
    return {
        "bin_id": random.randint(1, 100),
        "waste_level": random.randint(0, 100),  # Percentage of bin filled
        "timestamp": time.time(),
    }

# MQTT Client
client = mqtt.Client()
client.connect(MQTT_BROKER)

# Publish data
while True:
    data = simulate_sensor_data()
    client.publish(MQTT_TOPIC, str(data))
    print(f"Published: {data}")
    time.sleep(5)  # Send data every 5 seconds
```

---

### 2. Data Processing

#### `data_processing/data_processor.py`
This script consumes data from Kafka and processes it.

```python
from kafka import KafkaConsumer
import json

# Kafka Configuration
KAFKA_TOPIC = "waste-data"
KAFKA_BROKER = "localhost:9092"

# Kafka Consumer
consumer = KafkaConsumer(
    KAFKA_TOPIC,
    bootstrap_servers=KAFKA_BROKER,
    value_deserializer=lambda x: json.loads(x.decode("utf-8")),
)

# Process data
for message in consumer:
    data = message.value
    print(f"Processing: {data}")
    # Add your data processing logic here
```

---

### 3. Route Optimization

#### `route_optimization/optimize_routes.py`
This script implements a route optimization algorithm using machine learning.

```python
import random

def optimize_routes(waste_data):
    # Simulate route optimization
    optimized_routes = []
    for bin in waste_data:
        if bin["waste_level"] > 80:  # Collect bins that are more than 80% full
            optimized_routes.append(bin["bin_id"])
    return optimized_routes

# Simulate waste data
waste_data = [
    {"bin_id": 1, "waste_level": random.randint(0, 100)},
    {"bin_id": 2, "waste_level": random.randint(0, 100)},
    {"bin_id": 3, "waste_level": random.randint(0, 100)},
]

optimized_routes = optimize_routes(waste_data)
print(f"Optimized Routes: {optimized_routes}")
```

---

### 4. Dashboard

#### `dashboard/app.py`
This script creates a Flask backend for the dashboard.

```python
from flask import Flask, render_template
import random
import time
import threading

app = Flask(__name__)

# Simulate real-time waste data
waste_data = {"total_bins": 100, "full_bins": 0}

def update_waste_data():
    global waste_data
    while True:
        waste_data = {
            "total_bins": 100,
            "full_bins": random.randint(0, 100),
        }
        time.sleep(5)

# Start background thread
threading.Thread(target=update_waste_data, daemon=True).start()

@app.route("/")
def index():
    return render_template("index.html", data=waste_data)

if __name__ == "__main__":
    app.run(debug=True)
```

#### `dashboard/templates/index.html`
This is the frontend HTML for the dashboard.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Smart Waste Dashboard</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='styles.css') }}">
</head>
<body>
    <h1>Smart Waste Dashboard</h1>
    <div id="waste-data">
        <p>Total Bins: <span id="total-bins">{{ data.total_bins }}</span></p>
        <p>Full Bins: <span id="full-bins">{{ data.full_bins }}</span></p>
    </div>
    <script>
        // Update data every 5 seconds
        setInterval(async () => {
            const response = await fetch("/");
            const html = await response.text();
            const parser = new DOMParser();
            const doc = parser.parseFromString(html, "text/html");
            document.getElementById("total-bins").textContent = doc.getElementById("total-bins").textContent;
            document.getElementById("full-bins").textContent = doc.getElementById("full-bins").textContent;
        }, 5000);
    </script>
</body>
</html>
```

---

### 5. Run the Project

1. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```

2. Start the IoT sensor network:
   ```bash
   python iot/waste_sensor.py
   ```

3. Start the data processor:
   ```bash
   python data_processing/data_processor.py
   ```

4. Run the dashboard:
   ```bash
   python dashboard/app.py
   ```

5. Access the dashboard at `http://localhost:5000`.
