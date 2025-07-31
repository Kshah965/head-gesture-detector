# 🧠 Fatigue Detection Helmet

A wearable system that detects different head gestures using motion data from an IMU sensor. Originally developed to study cycling fatigue, the project evolved into a real-time gesture recognition system using a microcontroller, data collection tools, and machine learning classification.

---

## 💡 Motivation

The original goal was to collect and analyze motion data from cyclists to detect signs of fatigue. We aimed to:

- Compare normal cycling motion with fatigued cycling (e.g., simulated by riding with eyes closed on a stationary bike)
- Use sensor data (accelerometer + gyroscope) to classify different motion patterns
- Perform real-time fatigue detection using a wireless microcontroller
- Test and validate our model on multiple users

Due to hardware and time constraints, we shifted focus to classifying head gestures (e.g., nodding or stillness) as a foundational step toward fatigue detection.

---

## ⚙️ Hardware and Setup

- **Microcontroller**: ESP32-S3-DevKitC-1-N32R8
- **Sensor**: MPU6050 (IMU with accelerometer and gyroscope)
- **Firmware**: CircuitPython

### 🛠️ Setup Instructions

1. **Wiring**
   - Connect MPU6050 to ESP32 as follows:
     - `VCC → 3V`
     - `GND → G`
     - `SCL → 0`
     - `SDA → 1`

2. **Install CircuitPython**
   - Visit: [CircuitPython for ESP32-S3](https://circuitpython.org/board/espressif_esp32s3_devkitc_1_n32r8/)
   - Follow on-screen steps. You may need to press the **BOOT** button twice to detect the board.

3. **Install Libraries**
   - Download the [CircuitPython libraries bundle](https://circuitpython.org/libraries)
   - Copy the `lib/` folder to your device

4. **Deploy the Code**
   - Paste the contents of `code.py` from this repo into a new file on the device
   - Optional: Use [PuTTY](https://www.putty.org/) to verify serial data transmission
     - Set connection to **Serial**, use the correct COM port (e.g. COM17)
     - View live IMU output (acceleration + gyroscope)

---

## 🧪 Data Collection & Preprocessing

### Scripts

- **`data_collection.py`**  
  Collects sensor data via serial port and logs it to numbered `.txt` files (10-second samples).

- **`truncate.py`**  
  Truncates all text files to a uniform length of 600 lines to enable fair ML training.

- **`rename_file.py`**  
  Renames files for consistency across the dataset.

---

## 🤖 Machine Learning Pipeline

We trained two classifiers: **Support Vector Machine (SVM)** and **K-Nearest Neighbors (KNN)**.

### Gesture Classes

| Label | Gesture                            |
|-------|------------------------------------|
| 0     | sitting_still                      |
| 1     | sitting_nodding_up_down            |
| 2     | sitting_nodding_side_to_side       |
| 3     | sitting_nodding_diagonal           |

### Feature Extraction

Each `.txt` file (10s recording) is transformed into a **12-dimensional feature vector**:

- Mean and standard deviation for each axis of:
  - Accelerometer: `ax`, `ay`, `az`
  - Gyroscope: `gx`, `gy`, `gz`

### Scripts

- **`svm.py`**
  - Trains SVM with RBF kernel
  - Displays classification report + confusion matrix

- **`knn.py`**
  - Trains KNN using Euclidean distance, weighted voting
  - Includes feature normalization (StandardScaler)
  - Optionally searches for optimal `k` using accuracy plots

- **`real_time.py`**
  - Streams live IMU data
  - Predicts gesture every 0.5s using a trained SVM model
  - Outputs predictions in real time

---

## 🧪 Results

### SVM Classifier

- High accuracy on structured data
- Confusion between similar gestures (e.g., stillness vs side-to-side)
- Accuracy improved when using randomized training/test splits

### KNN Classifier

- More robust performance, especially for imbalanced or noisy classes
- Achieved **~100% accuracy** using random 70/30 data split
- Scales well with added gesture types

---

## 🧭 Challenges Faced

- ESP32 Bluetooth was unreliable using CircuitPython
- C programming with Espressif SDK had a steep learning curve
- Time constraints required scope reduction to head gestures
- Sensor readings were noisy without filtering

---

## 🚀 Future Work

1. **True Fatigue Classification**  
   - Collect motion data while cycling normally vs. eyes closed  
   - Train SVM and KNN to detect signs of fatigue

2. **Bluetooth Integration**  
   - Explore BLE and switch to C/Arduino if necessary

3. **Energy Efficiency**  
   - Integrate solar or other renewable sources

4. **Mobile App**  
   - Send push alerts when fatigue is detected

5. **3D Printed Mount**  
   - Design a universal helmet sensor holder

6. **User Testing**  
   - Test model accuracy across multiple users and conditions

---


