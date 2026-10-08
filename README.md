# AXEL NODE — Smart Cart Ecosystem

[![Flutter](https://img.shields.io/badge/Flutter-02569B?style=for-the-badge&logo=flutter&logoColor=white)](https://flutter.dev)
[![Raspberry Pi 5](https://img.shields.io/badge/Raspberry%20Pi%205-C51A4A?style=for-the-badge&logo=Raspberry-Pi&logoColor=white)](https://www.raspberrypi.com/)
[![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://www.python.org/)
[![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white)](https://opencv.org/)

**AXEL NODE** is a comprehensive, AI-powered Smart Cart ecosystem designed for Raspberry Pi 5 and cross-platform Flutter client applications. It integrates real-time video streaming, OpenCV and ML Kit neural vision processing, smart agricultural analytics, load sensing, and remote system management over SSH into a unified cockpit experience.

---

## 🌟 Key Features

- 🏎️ **Drive Cockpit & Low-Latency Live Stream**
  - High-frame-rate MJPEG video streaming (up to 100 FPS @ 1080p) from USB cameras connected to Raspberry Pi 5.
  - Multi-orientation cockpit support with adaptive desktop Navigation Rail and mobile bottom navigation bar.

- 🧠 **Cart AI & Proximity Hazard Detection**
  - Dual-layer computer vision: Real-time client-side ML Kit entity tracking combined with server-side OpenCV detection.
  - Automatic proximity hazard analysis and real-time audio/haptic alert system (triggers onboard hardware speaker/buzzer).
  - Persistent AI event history logging and inspection.

- 🌾 **Crop Vision AI**
  - Agricultural crop health diagnosis tool with camera capture and gallery image analysis.
  - Seasonal intelligence algorithm providing tailored recommendations for irrigation, nitrogen management, and disease prevention.

- ⚖️ **Smart Agri-Tools & Load Telemetry**
  - Real-time payload monitor interfacing with load sensor endpoints.
  - Live value calculations based on payload weight and crop commodity metrics.

- 💻 **Integrated Remote SSH Terminal**
  - Fully functional embedded terminal powered by `xterm` and `dartssh2`.
  - Built-in command macros for instant system stats (`vcgencmd`), process monitoring (`top`), log inspection, and reboot controls.

- 🖥️ **Onboard Pi Touchscreen GUI**
  - Native Pygame touchscreen app (`axelnode_pi_gui.py`) running directly on the Raspberry Pi 5 (1024x600 reference display).
  - Matches the Flutter app dark-mode design language with standalone offline controls and camera streaming server.

---

## 🏗️ System Architecture

```text
 ┌──────────────────────────────────────┐        ┌──────────────────────────────────────┐
 │         Flutter Application          │        │           Raspberry Pi 5             │
 │   (Android / iOS / macOS / Linux)    │        │         (Server & Local GUI)         │
 ├──────────────────────────────────────┤        ├──────────────────────────────────────┤
 │  • Control Cockpit UI                │  HTTP  │  • Flask Streaming Server (:5000)    │
 │  • ML Kit Live Inference             │ ◄────► │  • Pygame Onboard Touch GUI          │
 │  • Crop Vision Diagnostics           │  SSH   │  • OpenCV Frame Processing & Capture │
 │  • Embedded SSH Terminal             │ ◄────► │  • Hardware Audio Alert / Speakers    │
 └──────────────────────────────────────┘        └──────────────────────────────────────┘
```

---

## 📂 Project Structure

```text
axelnode/
├── lib/                        # Flutter Application Source
│   ├── main.dart               # App entrypoint & primary navigation scaffold
│   ├── pages/                  # Screen views
│   │   ├── home_page.dart          # Main dashboard & ecosystem splash screen
│   │   ├── live_stream_page.dart   # Dedicated Drive Cockpit video feed
│   │   ├── ai_analysis_page.dart   # Cart AI real-time ML Kit inference & logs
│   │   ├── crop_analysis_page.dart # Crop Vision AI seasonal advisory tool
│   │   └── terminal_page.dart      # Embedded SSH interactive shell
│   ├── services/               # Utilities & persistent logging
│   │   └── ai_logger.dart          # Local file logger for detection history
│   └── widgets/                # Reusable UI components
│       ├── object_painter.dart     # Canvas bounding box overlay painter
│       └── sheets.dart             # Settings and Smart Agri-Tools bottom sheets
├── rpi/                        # Raspberry Pi 5 Codebase
│   ├── server/                 # Lightweight Flask server
│   │   ├── camera_server.py        # MJPEG video feed, snapshot & telemetry endpoints
│   │   └── requirements.txt        # Python package dependencies
│   ├── gui/                    # Standalone Onboard Pygame Interface
│   │   └── axelnode_pi_gui.py      # Fullscreen Pi touchscreen GUI with built-in server
│   └── scripts/                # Startup & management shell scripts
│       ├── start_all.sh            # Main autostart runner
│       ├── start_gui.sh            # GUI standalone launcher
│       └── start_camera.sh         # Dedicated camera server runner
├── assets/                     # App static assets
└── pubspec.yaml                # Flutter project configuration & dependencies
```

---

## 🚀 Raspberry Pi 5 Setup

### 1. Hardware Requirements
- Raspberry Pi 5 (Raspberry Pi OS 64-bit recommended)
- USB Camera (V4L2 compatible)
- Official Raspberry Pi Touchscreen or HDMI Display (Optional for Pi GUI)
- Audio Speaker / Buzzer (for alert sounds)

### 2. Software Installation

1. Clone or transfer the repository to your Raspberry Pi:
   ```bash
   cd /home/rpi
   git clone <repository-url> axelnode_rpi
   cd axelnode_rpi/rpi
   ```

2. Install Python dependencies:
   ```bash
   pip install -r server/requirements.txt
   ```
   *(Ensure `opencv-python`, `flask`, `pygame`, and `numpy` are installed).*

3. Test the standalone Camera Server:
   ```bash
   python3 server/camera_server.py
   ```
   The server will start on `http://<your-pi-ip>:5000`.

4. (Optional) Run the Integrated Onboard Pygame GUI:
   ```bash
   bash scripts/start_all.sh
   ```

---

## 📱 Flutter App Setup

### Prerequisites
- [Flutter SDK](https://docs.flutter.dev/get-started/install) (v3.12.0 or higher)
- Android Studio / VS Code with Flutter extension
- Connected mobile device, emulator, or desktop runner

### Installation & Execution

1. Get dependencies:
   ```bash
   flutter pub get
   ```

2. Run the app:
   ```bash
   flutter run
   ```

3. **Connect to Raspberry Pi**:
   - Open the App Settings (gear icon in top right).
   - Enter your Raspberry Pi's local IP address (e.g., `192.168.0.112`).
   - Tap **Save Configuration**.

---

## 🌐 Server API Endpoints

The Raspberry Pi 5 server exposes the following REST/HTTP endpoints on port `5000`:

| Endpoint | Method | Description |
| :--- | :--- | :--- |
| `/` | `GET` | Server health check |
| `/video_feed` | `GET` | MJPEG video streaming feed |
| `/snapshot` | `GET` | Returns single high-resolution JPEG frame |
| `/status` | `GET` | Returns camera resolution, FPS, and status JSON |
| `/weight` | `GET` | Returns current payload load sensor data |
| `/alert` | `POST` | Triggers hardware audio alarm on the Pi speaker |

---

## 🛠️ Built With

- **Flutter** & **Dart** - Cross-Platform Application Framework
- **Google ML Kit** - Real-time Object & Face Detection
- **dartssh2** & **xterm** - Native SSH Remote Terminal
- **OpenCV** & **Pygame** - Video Stream Processing & Onboard GUI
- **Flask** - Lightweight Python HTTP & Streaming Server
