# Face Recognition on Raspberry Pi

A real-time face recognition system built with **Python and OpenCV** designed to run on a **Raspberry Pi**. The project uses Haar Cascade detection and the **LBPH (Local Binary Pattern Histogram)** face recognizer to capture face data, train a model, and perform live recognition from a webcam stream.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Setup & Running](#setup--running)
- [Workflow](#workflow)

---

## Overview

This project implements a three-stage face recognition pipeline:

1. **Capture** — Collect 30 face images for a named person using a webcam.
2. **Train** — Train an LBPH recognizer on the captured face images.
3. **Recognize** — Run real-time detection + recognition via the webcam feed.

Designed and tested for **Raspberry Pi** hardware with a camera module or USB webcam, but works on any device with a webcam running OpenCV.

---

## Features

- Automated face image capture (saves 30 grayscale face crops per person)
- LBPH face recognizer training from local images
- Real-time face recognition with name overlay
- Confidence threshold to display "Unknown" for unrecognized faces
- Pickle-based label dictionary persistence

---

## Tech Stack

| Component | Technology |
|---|---|
| Language | Python 3 |
| Computer Vision | OpenCV (`cv2`) |
| Face Detection | Haar Cascade (`haarcascade_frontalface_default.xml`) |
| Face Recognition | OpenCV LBPH (`cv2.face.LBPHFaceRecognizer`) |
| Data Storage | Pickle (labels), YAML (trainer model) |
| Hardware Target | Raspberry Pi + webcam |

---

## Project Structure

```
Face-Recognition-RaspberryPI/
├── capture.py          # Stage 1: Capture 30 face images for a named person
├── train2.py           # Stage 2: Train LBPH recognizer on captured images
├── recog3.py           # Stage 3: Real-time face recognition from webcam
├── abstract.txt        # Project abstract
├── steps to use.txt    # Quick-start guide
└── README.md
```

---

## How It Works

### Stage 1 — `capture.py`

- Prompts for a person's name.
- Creates `faces/<name>/` directory.
- Captures 30 grayscale face crops from the webcam using Haar Cascade detection.
- Saves them as `faces/<name>/0.jpg`, `1.jpg`, ..., `29.jpg`.

### Stage 2 — `train2.py`

- Loads all images from `faces/<person_name>/` directories.
- Encodes each person's name as a numeric label.
- Trains the LBPH face recognizer.
- Saves the model to `trainer.yml` and label mappings to `labels.pickle`.

### Stage 3 — `recog3.py`

- Loads the trained `trainer.yml` and `labels.pickle`.
- Opens live webcam feed.
- For each frame: detects faces → resizes to 200×200 → runs LBPH recognizer.
- If confidence `< 70` (lower = better match): displays the person's name.
- Otherwise: displays "Unknown".
- Press `q` to quit.

---

## Setup & Running

### Prerequisites (Raspberry Pi or any Linux/Windows/Mac with webcam)

```bash
pip install opencv-python opencv-contrib-python numpy
```

> `opencv-contrib-python` is required for the LBPH face recognizer.

### Step 1: Capture faces

```bash
python capture.py
# Enter the person's name when prompted
# The script captures 30 images and stops automatically
```

Repeat for each person you want to recognize.

### Step 2: Train the model

```bash
python train2.py
# Creates trainer.yml and labels.pickle
```

### Step 3: Run live recognition

```bash
python recog3.py
# Opens webcam — recognized faces are labeled with their name
# Press 'q' to quit
```

---

## Confidence Threshold

In `recog3.py`, the threshold is set to `70`:
```python
if conf < 70:   # Lower confidence = better match
    name = label_dict.get(id_, "Unknown")
else:
    name = "Unknown"
```
Lower values = stricter matching. Adjust based on your lighting and camera quality.
