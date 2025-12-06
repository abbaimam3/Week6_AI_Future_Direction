# Part 2, Task 2: AI-Driven IoT Concept

## Scenario: Smart Agriculture Simulation System

### 1. System Requirements

**Sensors Needed:**
1.  **Soil Moisture Sensor:** To measure water content in the soil at various depths.
2.  **DHT11/DHT22 (Temp & Humidity):** To monitor ambient air temperature and humidity.
3.  **NPK Sensor:** To measure soil nutrient levels (Nitrogen, Phosphorus, Potassium).
4.  **Light Intensity Sensor (LDR):** To track sunlight exposure hours.

### 2. AI Model Proposal

**Goal:** Predict crop yields and optimize irrigation.

**Model Choice:** **Recurrent Neural Network (RNN) - specifically LSTM (Long Short-Term Memory).**

**Justification:**
*   Crop growth is a time-series process. The state of the plant today depends on the conditions over the past weeks/months.
*   LSTMs are excellent at capturing long-term dependencies in time-series data (e.g., "A dry spell 2 weeks ago combined with high nitrogen today impacts yield X").
*   **Input:** Daily sequence of [Moisture, Temp, Humidity, NPK, Sunlight].
*   **Output:** Predicted Yield (kg/hectare) or Irrigation Recommendation (Liters).

### 3. Data Flow Diagram

```mermaid
graph TD
    subgraph Field [Farm Field]
        S1[Soil Moisture Sensor]
        S2[Temp/Humidity Sensor]
        S3[NPK Sensor]
    end

    subgraph Edge [Edge Gateway / Raspberry Pi]
        DA[Data Aggregation]
        PP[Pre-processing (Noise Filtering)]
        LiteModel[TFLite Model (Irrigation Control)]
        Actuator[Water Pump / Sprinkler]
    end

    subgraph Cloud [Cloud Platform]
        DB[(Time-Series Database)]
        Train[Model Training (LSTM)]
        Dash[User Dashboard]
    end

    %% Data Flow
    S1 --> DA
    S2 --> DA
    S3 --> DA
    DA --> PP
    PP --> LiteModel
    LiteModel -- "Turn On/Off" --> Actuator
    PP -- "Batch Upload" --> DB
    DB --> Train
    Train -- "Update Weights" --> LiteModel
    DB --> Dash
```

### 4. Workflow Explanation
1.  **Sensing:** Sensors collect environmental data every 15 minutes.
2.  **Edge Processing:** The Edge Gateway (e.g., Raspberry Pi) aggregates the data. A lightweight TFLite model makes immediate decisions (e.g., "Soil is critically dry -> Turn on Pump") to ensure real-time response without internet reliance.
3.  **Cloud Sync:** Aggregated data is uploaded to the cloud periodically.
4.  **Training:** The heavy LSTM model in the cloud analyzes historical trends to predict future yields and retrains the edge model with improved parameters.
5.  **Visualization:** Farmers view insights and yield predictions on a dashboard.
