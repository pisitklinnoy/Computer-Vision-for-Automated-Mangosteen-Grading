# 🍈 Mangosteen Detection and Grading System

An AI-powered computer vision project for mangosteen detection, object tracking, and quality grading. The system integrates YOLO-based deep learning models with a C++ application through a Python AI server, enabling visual detection and grading results to be processed by the application.

## Overview

This project combines object detection, multi-object tracking, and image classification to support an automated mangosteen inspection workflow.

The AI pipeline uses two separate models:

- **Mangosteen Detection:** A YOLO26s model identifies mangosteen objects in image frames.
- **Mangosteen Grading:** A separate YOLO-based classification model predicts the grade of each detected mangosteen.

The Python AI server processes incoming image data, tracks detected objects across frames, assigns grading predictions, and returns structured JSON results to the C++ application.

## Key Features

- **Object Detection:** Detects mangosteen objects using a YOLO-based model.
- **Object Tracking:** Uses ByteTrack to maintain object IDs across video frames.
- **AI-Based Grading:** Classifies detected mangosteens using a separate grading model.
- **Grade Memory:** Caches grading predictions by tracking ID to reduce repeated classification inference.
- **C++ and Python Integration:** Connects the application and AI inference pipeline through ZeroMQ.
- **JSON Communication:** Returns object IDs, bounding-box coordinates, and grading predictions in structured JSON format.
- **Real-Time Processing Architecture:** Designed to process successive image frames for continuous detection and tracking.

## System Architecture

```text
┌──────────────────────────────┐
│       C++ Application        │
│       FruitProject.cpp       │
└──────────────┬───────────────┘
               │
               │ Image bytes
               ▼
┌──────────────────────────────┐
│       ZeroMQ Messaging       │
│       TCP Port 5555          │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│        Python AI Server      │
│          ai_server.py        │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│     YOLO26s Detector         │
│     + ByteTrack              │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│   Mangosteen Grading Model   │
│   Classification Inference   │
└──────────────┬───────────────┘
               │
               ▼
┌──────────────────────────────┐
│ JSON Detection and Grade Data│
└──────────────┬───────────────┘
               │
               ▼
        C++ Application
```

## AI Workflow

1. The C++ application sends an image frame to the Python AI server.
2. The Python server decodes the image using OpenCV and NumPy.
3. The YOLO26s detector identifies mangosteens and ByteTrack assigns tracking IDs.
4. Each newly tracked object is cropped from the original image.
5. The grading model predicts the grade of the cropped object.
6. The predicted grade is stored against the tracking ID to avoid unnecessary repeated classification.
7. The server serializes the detection results into JSON.
8. The results are sent back to the C++ application through ZeroMQ.

## Technology Stack

| Category | Technologies |
|---|---|
| Programming Languages | Python, C++ |
| Computer Vision | OpenCV |
| Deep Learning | YOLO26s, Ultralytics |
| Object Tracking | ByteTrack |
| Image Processing | NumPy |
| Inter-Process Communication | ZeroMQ |
| Data Serialization | JSON |
| Development Environment | Visual Studio / Visual Studio C++ project |

## Project Structure

```text
FruitProject/
├── FruitProject.cpp
├── FruitProject.slnx
├── FruitProject.vcxproj
├── ai_server.py
├── best_mangosteen_yolo26s.pt
└── grade_mangosteen_best.pt
```

- `FruitProject.cpp` — C++ application entry point.
- `ai_server.py` — Python inference server, detection, tracking, grading, and message handling.
- `best_mangosteen_yolo26s.pt` — Mangosteen detection model weights.
- `grade_mangosteen_best.pt` — Grading model weights.
- `FruitProject.slnx` and `FruitProject.vcxproj` — Visual Studio project configuration.

## Installation

### Prerequisites

- Python 3.10 or another Python version compatible with the installed dependencies.
- Visual Studio with the required C++ development tools.
- A compatible PyTorch and Ultralytics environment.
- Git.

### 1. Clone the Repository

```bash
git clone https://github.com/pisitklinnoy/FruitProject.git
cd FruitProject
```

### 2. Create a Python Virtual Environment

```bash
python -m venv .venv
```

Activate it on Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

### 3. Install Python Dependencies

```bash
python -m pip install --upgrade pip
pip install ultralytics opencv-python numpy pyzmq
```

Ensure that PyTorch is installed with a configuration compatible with your hardware and the selected Ultralytics version.

## Running the AI Server

Start the Python AI server:

```bash
python ai_server.py
```

The server loads both trained models and listens for incoming image data over ZeroMQ on TCP port `5555`.

Keep the server running while using the client application.

**Note:** The current repository's C++ entry point is a basic starter program. The complete client-side image capture and ZeroMQ communication workflow must be implemented or integrated before the full end-to-end system can run.

## Example Output

The AI server returns JSON objects containing the tracking ID, bounding-box coordinates, and predicted grade.

```json
[
  {
    "id": 1,
    "box": {
      "x1": 120,
      "y1": 80,
      "x2": 260,
      "y2": 220
    },
    "grade": "example_grade"
  }
]
```

The values above are illustrative; actual grades depend on the trained model's class names and predictions.

## Potential Applications

- Automated mangosteen sorting and inspection.
- Computer vision for agricultural products.
- AI-assisted fruit quality assessment.
- Integration with automated sorting or robotic handling systems.

## Future Improvements

- Implement and document the complete C++ client communication pipeline.
- Add a reproducible dataset preparation and model training workflow.
- Evaluate detection and grading performance using appropriate metrics.
- Add confidence scores and configurable grading thresholds.
- Improve error handling, logging, and communication reliability.
- Benchmark inference latency and throughput on target hardware.
- Integrate with a conveyor belt, camera system, or robotic sorting mechanism.

## License

Please refer to the repository's licensing terms before redistributing the source code or trained model weights.

## Author

**Pisit Klinnoy**

GitHub: [@pisitklinnoy](https://github.com/pisitklinnoy)
