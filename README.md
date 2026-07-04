<div align="center">

#  NETRA : Next-Gen Emergency Traffic Response Architecture
### Network-Enabled Traffic Routing & Analysis

**A smart traffic perception system that sees the road, thinks in graphs, and moves emergencies through in real time.**

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![YOLOv8](https://img.shields.io/badge/YOLOv8-Ultralytics-00FFFF?style=flat)](https://github.com/ultralytics/ultralytics)
[![OpenCV](https://img.shields.io/badge/OpenCV-Computer%20Vision-5C3EE8?style=flat&logo=opencv&logoColor=white)](https://opencv.org/)
[![NetworkX](https://img.shields.io/badge/NetworkX-Graph%20Routing-orange?style=flat)](https://networkx.org/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

</div>

---

## What is NETRA?

Most traffic lights run on a timer. They don't know if a lane is empty or gridlocked, and they definitely don't know an ambulance is stuck behind a wall of cars.

**NETRA fixes that.**

It's an end-to-end pipeline that watches live traffic camera feeds, counts vehicles per lane with computer vision, detects emergency vehicles by *sight and sound*, and feeds all of that into a live road-network graph — so routing and signal timing can react to what's actually happening on the street, not a fixed 60-second clock.

Think of it as giving a city intersection a pair of eyes, a pair of ears, and a decent sense of judgment.

---

## Core Capabilities

- 🚗 **Vehicle Detection & Counting** — Custom-trained YOLOv8 model detects cars, bikes, buses, trucks, and autos per frame, per lane, per direction.
- 🚨 **Emergency Vehicle Detection (Multi-Modal)** — A CNN classifier scans frames for ambulances, fire trucks, and police vehicles, while a separate **audio model (MFCC features + Random Forest)** listens for sirens. If either fires, the system flags it — no single point of failure.
- 📊 **Live Traffic Density Scoring** — Converts raw vehicle counts into LOW / MEDIUM / HIGH traffic levels per direction, in real time.
- 🗺️ **Road Network as a Graph** — Pulls real-world road topology for a given city (built and tested on Dehradun) using OSMnx + NetworkX, turning intersections into nodes and roads into weighted edges.
- ⚡ **Dynamic Edge Re-Weighting** — Traffic density and emergency flags directly reshape the graph's edge weights, so routing algorithms running on top of it always see the *current* state of the road, not a stale map.
- 🚦 **Adaptive Signal Logic** — Green-light duration scales with real vehicle density instead of a fixed timer, and emergency detection can force a priority override.
- 🌐 **Interactive Visualizations** — Standalone HTML demos (`netra-astar.html`, `netra-astar-live.html`, `netra-dynamic.html`) that visualize A\* pathfinding and live traffic-aware routing directly in the browser — no backend required.
- 🗄️ **Persistent Traffic State** — SQLite-backed storage for node-level traffic data, so the system has memory instead of amnesia between frames.

---

## How It Works

```
 ┌─────────────────┐     ┌──────────────────┐     ┌───────────────────┐
 │  Traffic Camera  │ ──▶ │   YOLOv8 Vehicle  │ ──▶ │  Density Scoring   │
 │  Feed (per lane) │     │     Detection     │     │  (LOW/MED/HIGH)    │
 └─────────────────┘     └──────────────────┘     └─────────┬──────────┘
                                                              │
 ┌─────────────────┐     ┌──────────────────┐               │
 │   Audio Stream   │ ──▶ │  Siren Detection  │──┐            │
 │  (extracted via  │     │ (MFCC + RandomFor)│  │            │
 │     ffmpeg)      │     └──────────────────┘  │            ▼
 └─────────────────┘                            │   ┌───────────────────┐
                                                 ├──▶│  Emergency + Load  │
 ┌─────────────────┐     ┌──────────────────┐   │   │   Fusion Layer     │
 │   Video Frame    │ ──▶ │  CNN Emergency    │──┘   └─────────┬──────────┘
 │                  │     │  Vehicle Classifier│               │
 └─────────────────┘     └──────────────────┘                 ▼
                                                    ┌────────────────────────┐
 ┌──────────────────────┐                          │  Road Graph (NetworkX)  │
 │  OSMnx Road Network   │ ────────────────────────▶│  Dynamic Edge Weights   │
 │   (city topology)     │                          │  + SQLite State Store  │
 └──────────────────────┘                          └───────────┬─────────────┘
                                                                 ▼
                                                    ┌────────────────────────┐
                                                    │  A* Routing / Adaptive  │
                                                    │  Signal Control         │
                                                    └────────────────────────┘
```

---

## Tech Stack

| Layer | Tools |
|---|---|
| **Object Detection** | YOLOv8 (Ultralytics), OpenCV |
| **Emergency Vision** | TensorFlow / Keras CNN |
| **Emergency Audio** | Librosa (MFCC extraction), Scikit-learn (Random Forest) |
| **Road Network Modeling** | OSMnx, NetworkX |
| **Data Persistence** | SQLite |
| **Routing / Visualization** | A\* search, custom HTML/JS interactive demos |
| **Experimentation** | Google Colab, Jupyter Notebook |

---

## Repo Structure

```
NETRA/
├── netra (1) copy.ipynb        # Full pipeline: data prep, YOLO training, detection,
│                                # emergency classification, graph modeling
├── netra-astar.html            # A* pathfinding visualization (static demo)
├── netra-astar-live.html       # A* routing with live traffic updates
├── netra-dyanmic.html          # Dynamic graph weight visualization
├── netra-dyanmicupdated.html   # Updated dynamic routing demo
├── netra3.html                 # Iteration on the core visualization
├── netrav6.html                # Latest visualization build
└── README.md
```

---

## Getting Started

### 1. Clone the repo
```bash
git clone https://github.com/DakshMehrotra/NETRA.git
cd NETRA
```

### 2. Run the notebook
Open `netra (1) copy.ipynb` in **Google Colab** (recommended — it's built for it) or Jupyter locally.

Install the core dependencies:
```bash
pip install ultralytics opencv-python osmnx networkx librosa tensorflow scikit-learn
```

### 3. Try the live demos
No setup needed — just open any of the `.html` files directly in a browser:
```bash
open netra-astar-live.html
```

---

## Roadmap

- [ ] Package the detection pipeline as a deployable service (FastAPI/Flask)
- [ ] Replace simulated 4-direction video feeds with real multi-camera sync
- [ ] Extend emergency detection to a wider vehicle/siren dataset
- [ ] Integrate live routing output directly with the HTML visualizations
- [ ] Add REST API for external traffic-management systems to pull live graph state

---

## Author

**Daksh Mehrotra**
B.Tech CS, UPES Dehradun

<div align="center">

*Built on the idea that a traffic light should know what's happening on the road before it decides what to do about it.*

</div>
