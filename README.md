<p align="center">
  <img src="assets/brain_neural.png" alt="DroneBrain Neural Network" width="100%"/>
</p>

<h1 align="center">DroneBrain</h1>
<p align="center">Real-time aerial intelligence — vision, autonomy, and gesture control in one pipeline</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/YOLOv8-Ultralytics-00CFFF?style=flat"/>
  <img src="https://img.shields.io/badge/PyTorch-2.x-EE4C2C?style=flat&logo=pytorch&logoColor=white"/>
  <img src="https://img.shields.io/badge/MediaPipe-0.10-00BCD4?style=flat"/>
  <img src="https://img.shields.io/badge/Simulator-AirSim-00A8A8?style=flat"/>
  <img src="https://img.shields.io/badge/Status-In%20Development-D4AF37?style=flat"/>
  <img src="https://img.shields.io/badge/License-MIT-lightgrey?style=flat"/>
</p>

---

## What This Is

DroneBrain is a personal R&D project that turns a drone into an autonomous aerial observer. The goal is to build a system where the drone can detect and track objects in its environment, respond to hand gestures and eye gaze, and autonomously plan a patrol route while locking onto high-value targets.

Everything is tested in AirSim simulation before touching any physical hardware. The project is being built in phases — right now the focus is getting the vision pipeline solid.

---

## Hardware Overview

<p align="center">
  <img src="assets/drone_parts.png" alt="Drone hardware components" width="72%"/>
</p>

The platform uses a standard quadcopter layout — four brushless motors driven by individual ESCs, a central flight controller, power distribution board, FPV camera, and an FPV transmitter for live video downlink. Every component is off-the-shelf and widely available.

---

## Core Modules

**DroneDXY Control Engine**
Takes MediaPipe landmark data from a webcam — hand poses and face mesh gaze vectors — and converts them into discrete flight commands: `FORWARD`, `BACKWARD`, `LEFT`, `RIGHT`, `UP`, `DOWN`, `ROTATE_LEFT`, `ROTATE_RIGHT`, and `LAND`.

**Vision Intelligence**
YOLOv8 (nano/small) fine-tuned on the VisDrone2019-DET dataset. Runs inference on each camera frame and returns bounding boxes, class labels, and confidence scores across 10 aerial object categories (person, car, truck, bus, train, bike, motor, etc.).

**Autonomous Decision Engine**
Consumes detection results and drone state to plan the next move. Priority order: hold a locked target if one exists, avoid any obstacle within threshold distance, otherwise continue the patrol path. Outputs the next flight command every frame.

**AI Content Engine**
Selects the highest-confidence frames from each flight segment and sends them to GPT-4 Vision with a structured prompt. Gets back JSON-formatted scene captions that are attached to each frame in the patrol report.

---

## Eye Tracking & Face Mesh Control

The gaze control system uses MediaPipe Face Mesh to track 468 facial landmarks in real time. From those landmarks it extracts the gaze vector — where the user's eyes are pointing — and maps it to camera pan/tilt commands. Blink detection enables mode switching and emergency stop.

<p align="center">
  <img src="assets/face_mesh_tracking.png" alt="MediaPipe face mesh and eye tracking demo" width="60%"/>
</p>

---

## Features

- Hand gesture recognition mapped to flight commands via MediaPipe hand landmarks
- Eye tracking navigation — gaze direction controls camera rotation and heading
- Aerial object detection with YOLOv8 trained on drone-altitude footage
- Rule-based autonomous path planning with target lock and obstacle logic
- Per-frame AI scene captioning through GPT-4 Vision
- Patrol report export as JSON and CSV with annotated frames
- Live dashboard showing object counts, top frames, captions, and system status
- Full AirSim integration — no drone hardware needed to run the pipeline

---

## Tech Stack

| Layer | Tools |
|---|---|
| Object detection | YOLOv8 · Ultralytics |
| Gesture + gaze control | MediaPipe |
| Vision processing | OpenCV, Pillow |
| Deep learning | PyTorch 2.x |
| AI captioning | OpenAI GPT-4 Vision |
| Simulation | AirSim, Gazebo |
| Language | Python 3.10+ |

---

## Datasets

| Dataset | Use | Link |
|---|---|---|
| VisDrone2019-DET | Primary training — drone-altitude footage, 10 object categories | [aiskyeye.com](http://aiskyeye.com/) |
| COCO | Pre-training and transfer learning for general objects | [cocodataset.org](https://cocodataset.org/) |
| Open Images v7 | Supplementary training for edge-case object categories | [openimages](https://storage.googleapis.com/openimages/web/index.html) |

---

## Installation

```bash
git clone https://github.com/yourusername/DroneBrain.git
cd DroneBrain

python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate

pip install -r requirements.txt
```

For simulation, download the [AirSim binary](https://github.com/microsoft/AirSim/releases) for your OS and place it outside the project directory. The pipeline connects over the default localhost socket by default.

---

## Usage

```bash
# Full pipeline in simulation
python src/main.py --mode sim

# Webcam only — gesture and eye tracking demo
python src/main.py --mode webcam

# Detection on a pre-recorded video
python src/main.py --mode video --input data/sample_flight.mp4

# Launch the live dashboard
python src/dashboard.py
```

Output goes to `output/` — annotated frames, `patrol_report.json`, and `patrol_report.csv`.

---

## Project Structure

```
DroneBrain/
├── src/
│   ├── main.py               # Pipeline entry point
│   ├── control/              # DroneDXY gesture + gaze engine
│   ├── vision/               # YOLOv8 detection module
│   ├── decision/             # Autonomous path planner
│   └── content/              # GPT-4 Vision captioning engine
├── dashboard.py              # Live dashboard
├── data/                     # Sample footage and test inputs
├── output/                   # Annotated frames and patrol reports
├── assets/                   # Diagrams and images
└── requirements.txt
```

---

## Roadmap

**Phase 1 — Vision foundation** ← current
- [ ] VisDrone dataset setup and YOLOv8 fine-tuning
- [ ] AirSim simulation environment
- [ ] Basic detection pipeline with annotated output

**Phase 2 — Control layer**
- [ ] Hand gesture recognition and command mapping
- [ ] Eye tracking navigation
- [ ] DroneDXY command dispatcher

**Phase 3 — Autonomy and output**
- [ ] Autonomous decision engine (patrol + target lock)
- [ ] GPT-4 Vision scene captioning
- [ ] Patrol report generation and dashboard

**Phase 4 — Validation**
- [ ] Benchmark detection mAP on VisDrone test split
- [ ] Latency profiling across the inference and control loop
- [ ] Hardware integration testing

---

## Future Enhancements

- Multi-drone coordination with shared detection state
- On-device inference with quantized YOLO (ONNX / TensorRT)
- Reinforcement learning flight policy to replace the rule engine
- LiDAR fusion for depth-aware obstacle avoidance
- Live video stream with overlay annotations

---

## References

- [YOLOv8 Documentation](https://docs.ultralytics.com/)
- [MediaPipe Documentation](https://developers.google.com/mediapipe)
- [OpenCV Documentation](https://docs.opencv.org/)
- [PyTorch Documentation](https://pytorch.org/docs/stable/index.html)
- [AirSim Simulator](https://microsoft.github.io/AirSim/)
- [Gazebo Simulator](https://gazebosim.org/)
- [VisDrone Dataset](http://aiskyeye.com/)
- [COCO Dataset](https://cocodataset.org/)
- [Open Images v7](https://storage.googleapis.com/openimages/web/index.html)
- [OpenAI GPT-4 Vision](https://platform.openai.com/docs/guides/vision)

---

## License

MIT — see [LICENSE](LICENSE) for details.

---

<p align="center">
  Built by <a href="https://github.com/yourusername">Dharnish</a> &nbsp;·&nbsp; BSc AI/ML &nbsp;·&nbsp; Kristu Jayanti University
</p>
