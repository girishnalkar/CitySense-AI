# 🏙️ CitySense AI 

An AI-powered, hybrid Edge-to-Cloud smart city infrastructure monitoring system. CitySense AI uses Edge AI cameras (YOLOv8 + OpenCV) to automatically detect road hazards (potholes) and sanitary issues (overflowing garbage), clusters them intelligently to avoid duplication, and uses Google Gemini to generate proactive maintenance reports.

## 🌐 Live Deployments

*   **Live Web App (Frontend):** [city-pulse-ivory-kappa.vercel.app](https://city-pulse-ivory-kappa.vercel.app/)
*   **Live API Server (Backend):** [citypulse-1-bjum.onrender.com](https://citypulse-1-bjum.onrender.com)
*   **Database:** MongoDB Atlas (Cloud NoSQL)
*   **AI Engine:** Google Gemini 2.5 Flash

---

## 🚀 Key Features

*   **📷 Real-Time Edge AI Detection:** Simulates dashcams and static CCTV cameras using YOLOv8 models ([pothole_best.pt] and [garbage_best.pt] to detect road irregularities and waste build-up.
*   **📍 Intelligent Clustering (10m Radius):** Group nearby reported incidents automatically in the database, avoiding redundant report cluttering while dynamically escalating severity levels as more sightings are reported.
*   **🧠 Proactive Maintenance (Gemini AI):** An autonomous reporting pipeline fetches active clusters, prepares structured prompts, and queries Gemini 2.5 Flash to synthesize urgent bullet-point work plans.
*   **📟 Silent Failure Detection (Anomaly Engine):** Monitors device heartbeats (e.g. CCTV feeds, smart streetlights) and flags anomalies using an exponential failure probability model when heartbeats are missed.
*   **🗺️ Citizen Reporting Portal:** Enables residents to report localized complaints. Citizen entries default to `Pending_Verification` until approved by city administrators.

---

## 🧰 Tech Stack

| Frontend | Backend | Machine Learning & Edge |
| :--- | :--- | :--- |
| React (Vite) | Node.js (Express) | Python |
| Leaflet Maps | MongoDB (Mongoose) | YOLOv8 (Ultralytics) |
| Tailwind CSS | Google Gemini AI SDK | OpenCV (cv2) |
| Recharts (Analytics) | | Flask (MJPEG Streaming) |

---

## 🔑 Setup & Installation

### Step 1: Install Dependencies
Run the installation script in the root directory to set up both Node.js services:

```bash
# Install backend dependencies
npm install

# Install frontend dependencies
cd frontend
npm install
cd ..
```

### Step 2: Set Up Python ML Environment
Create a virtual environment and install the required vision dependencies:

```bash
# Create and activate virtual environment
python -m venv venv
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate

# Install requirements
pip install -r requirements.txt
```

### Step 3: Configure Environment Variables
Create a `.env` file in the root folder:

```env
PORT=5000
GEMINI_API_KEY=your_google_gemini_api_key
MONGO_URI=your_mongodb_connection_string
```

---

## 📡 Core API Reference

### 1. Detections Ingestion & Retrieval

*   **`GET /api/detections`**
    *   **Description:** Fetches current incidents. Filters out `Pending_Verification` items by default.
    *   **Query Params:** `status` (Open/Resolved/Pending_Verification), `include_pending` (true/false).
*   **`POST /api/detections`**
    *   **Description:** Ingests telemetry reports. Automates clustering for detections within 10 meters.
    *   **Request Body:**
        ```json
        {
          "type": "Pothole",
          "severity": "Medium",
          "lat": 19.1136,
          "lng": 72.8697,
          "confidence": 0.88,
          "source": "python_streamer"
        }
        ```

### 2. Anomaly Engine (Predictive AI)

*   **`GET /api/infrastructure/anomalies`**
    *   **Description:** Scans city device heartbeats and calculates failure risk levels using missed interval mathematics.

### 3. AI Advisory Report Generation

*   **`POST /api/generate-report`**
    *   **Description:** Builds a context payload of active incidents and requests a maintenance summary from Gemini.
    *   **Response:**
        ```json
        {
          "report": "### ENGINEERING ADVISORY\n* Urgent action required near coordinate cluster..."
        }
        ```

---

## 💻 Local Development Workflow

To boot up the complete environment, open three terminals:

1.  **Terminal 1 (Backend Server):**
    ```bash
    node server.js
    ```
    *(Note: The server will automatically attempt to spawn the Python Multi-Cam Streamer in the background if it detects the local `venv` folder.)*

2.  **Terminal 2 (Frontend Client):**
    ```bash
    cd frontend
    npm run dev
    ```

3.  **Terminal 3 (Manual Python Camera Server - optional override):**
    ```bash
    # Activate virtual environment
    venv\Scripts\activate
    python stream_camera.py
    ```

---

## ⚙️ Development Notes
*   **Google Maps Warning:** The "For Development Purposes Only" watermark on the Leaflet map is expected and will not block key map functionalities.
*   **Free-tier Cold Starts:** The live Render backend sleeps after 15 minutes of inactivity. Please allow up to 50 seconds for the initial API response when starting the live app.
