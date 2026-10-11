# Face Recognition App

A real-time face recognition desktop application built with Python. It detects faces from a live webcam feed or uploaded images, overlays a 468-point facial landmark mesh, and recognizes people you've saved by comparing 128-dimensional face encodings.

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![OpenCV](https://img.shields.io/badge/OpenCV-4.8%2B-green)
![PySide6](https://img.shields.io/badge/GUI-PySide6-41cd52)

<!-- Add a screenshot or GIF of the app here -->
<!-- ![App screenshot](docs/screenshot.png) -->

## Features

- **Live webcam recognition** – real-time face detection at ~30 FPS with bounding boxes and facial landmark overlays
- **Image upload** – detect and recognize faces in static images (PNG, JPG, JPEG, BMP)
- **Save known faces** – capture a face and store it with a name in a local database
- **Face matching** – identifies saved people using Euclidean distance between face encodings, with the match name and distance shown in the status bar
- **Face management** – browse saved faces with thumbnails, rename them, and search by name in real time
- **Desktop GUI** – clean Qt-based interface built with PySide6

## Tech Stack

| Library | Purpose |
|---|---|
| [OpenCV](https://opencv.org/) | Camera capture and image processing |
| [MediaPipe](https://developers.google.com/mediapipe) | 468-point facial landmark detection (Face Mesh) |
| [face_recognition](https://github.com/ageitgey/face_recognition) | Face detection and 128D encoding generation (built on dlib) |
| [PySide6](https://doc.qt.io/qtforpython/) | Desktop GUI framework |
| [NumPy](https://numpy.org/) | Numerical operations and embedding storage |

## Project Structure

```
Face-Recognition-App/
├── run.py                 # Entry point
├── requirements.txt       # Dependencies
├── data/                  # Face database (created automatically)
│   ├── index.json         # Metadata index for saved faces
│   └── faces/
│       └── [person_name]/
│           ├── face.jpg       # Cropped face image
│           └── embedding.npy  # 128D face encoding
└── src/
    ├── camera_handler.py  # Webcam access via OpenCV
    ├── face_detector.py   # Detection, landmarks, encodings, drawing
    ├── face_database.py   # Saving, loading, and matching faces
    └── main.py            # PySide6 GUI application
```

## Installation

### Prerequisites

- Python 3.9 or newer
- A webcam (for live mode)
- **CMake** and a C++ compiler, which `dlib` (required by `face_recognition`) needs to build:
  - **Windows:** install [Visual Studio Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/) with the "Desktop development with C++" workload
  - **macOS:** run `xcode-select --install`
  - **Linux:** install `cmake` and `build-essential` via your package manager

### Setup

```bash
# Clone the repository
git clone https://github.com/joelrajah08/Face-Recognition-App.git
cd Face-Recognition-App

# Create and activate a virtual environment
python -m venv venv
# Windows:
venv\Scripts\activate
# macOS/Linux:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt
```

> Installing `face-recognition` compiles dlib from source and can take several minutes.

## Usage

```bash
python run.py
```

1. **Start the live feed** – click **📹 Live Feed** to turn on your webcam. Detected faces get a green box and a landmark mesh.
2. **Save a face** – when a face is detected, click **💾 Save Face** and enter the person's name. They'll appear in the sidebar.
3. **Recognize faces** – when a saved person appears on camera, the status bar shows `Match: [Name] (distance: X.XXX)`.
4. **Upload an image** – click **📁 Upload Image** to run detection and recognition on a photo.
5. **Manage faces** – use the search box to filter saved faces, or click ✏️ to rename one.

## How It Works

1. **Detection** – `face_recognition` (HOG model) finds face bounding boxes, while MediaPipe Face Mesh maps 468 facial landmarks for the visual overlay.
2. **Encoding** – each detected face is converted into a 128-dimensional vector by a deep neural network. Photos of the same person produce similar vectors.
3. **Matching** – the app computes the Euclidean distance between the new encoding and every saved one. The closest match is accepted if its distance is at or below the threshold (default `0.6`). Same-person distances are typically under 0.4.
4. **Storage** – each saved face's cropped image and encoding are stored in `data/faces/`, with a JSON index tracking names and file paths.

## Troubleshooting

**Camera won't open** – close other apps using the camera, or change `camera_index` to `1` or `2` in `src/camera_handler.py`.

**No faces detected** – improve lighting, face the camera directly, and adjust your distance from it.

**Wrong or missed matches** – adjust `threshold` in `find_closest_match()` in `src/face_database.py`. A lower value makes matching stricter.

**Slow performance** – make sure `face_detector.py` uses `model="hog"` rather than `"cnn"`, lower `max_num_faces`, or reduce the camera resolution.

## Roadmap

- [ ] Delete saved faces from the GUI
- [ ] Store multiple photos per person for better accuracy
- [ ] Color-coded match confidence
- [ ] Webcam selection
- [ ] FPS counter
- [ ] Database export/import
- [ ] Batch image processing

## Privacy Note

All face data is stored locally in the `data/` folder and never leaves your machine. The `data/` folder should be listed in `.gitignore` so saved faces aren't committed to the repository.

## Acknowledgments

Built as part of the Trailblazers Software Projects program (Project 2 – AI Face Detection).

## Author

**Joel Rajah** – [@joelrajah08](https://github.com/joelrajah08)
