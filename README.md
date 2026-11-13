# SiteSight — Real-Time Construction Safety & PPE Computer Vision

[![Live Demo](https://img.shields.io/badge/Demo-Live_GitHub_Pages-0ea5e9?style=for-the-badge&logo=googlechrome&logoColor=white)](https://abdulqader-alshihabi.github.io/SiteSight-Safety-Vision/)
[![Colab](https://img.shields.io/badge/Notebook-Open_in_Colab-f59e0b?style=for-the-badge&logo=googlecolab&logoColor=white)](https://colab.research.google.com/github/Abdulqader-Alshihabi/SiteSight-Safety-Vision/blob/main/notebooks/Construction_Safety_AI.ipynb)
[![Model](https://img.shields.io/badge/Model-YOLO11n_PyTorch-34d399?style=for-the-badge&logo=pytorch&logoColor=white)](https://github.com/Abdulqader-Alshihabi/SiteSight-Safety-Vision)
[![Runtime](https://img.shields.io/badge/Runtime-ONNX_WebAssembly-6366f1?style=for-the-badge&logo=webassembly&logoColor=white)](https://github.com/Abdulqader-Alshihabi/SiteSight-Safety-Vision)
[![License](https://img.shields.io/badge/License-MIT-gray?style=for-the-badge)](LICENSE)

An enterprise-grade edge computer vision system for real-time Personal Protective Equipment (PPE) detection and occupational hazard auditing on construction job sites. Operates entirely in the visitor's browser with sub-30ms inference latency using ONNX Runtime WebAssembly, eliminating cloud streaming costs and protecting worker video privacy.

---

## Executive Summary & Problem Formulation

Continuous occupational monitoring on industrial work sites is critical for preventing workplace injuries and maintaining OSHA / Saudi Ministry of Human Resources regulatory compliance. However, traditional cloud-streamed computer vision solutions suffer from three fundamental bottlenecks:
1. **Excessive Bandwidth Costs:** Continuous 24/7 video streaming from remote construction sites quickly saturates 4G/5G uplinks and incurs high cloud egress and GPU compute fees.
2. **Worker Privacy & PII Concerns:** Streaming high-definition footage to cloud servers creates legal and biometric data compliance risks under personal data protection laws.
3. **High Latency:** Round-trip network latency delays critical alerts when personnel enter high-hazard machine swing radiuses without helmets or high-visibility gear.

**SiteSight solves this through pure edge architecture:** video frames are processed directly inside the client browser or local edge gateways using an optimized YOLO11 nano model exported to ONNX FP32/INT8, generating instant structured safety audit logs with zero external server dependencies.

---

## System Architecture

```
[ Job Site Stream / CCTV / Video ]
                |
                v
+---------------------------------------+
|  Client Browser (Zero-Cloud Upload)   |
|                                       |
|  1. HTML5 Canvas & Video Decoding     |
|     - Normalized 640x640 letterbox    |
|     - RGB float32 tensor conversion   |
|                                       |
|  2. ONNX Runtime Web (WebAssembly)   |
|     - Multithreaded SIMD execution   |
|     - YOLO11n exported graph          |
|                                       |
|  3. Post-Processing & Analytics       |
|     - Non-Maximum Suppression (NMS)   |
|     - Confidence threshold filtering  |
|     - PPE violation logic rules       |
+---------------------------------------+
                |
                +---> [ Interactive Bounding Boxes & Warning Overlays ]
                +---> [ Real-Time Safety Compliance Metric Dashboard ]
                +---> [ Automated JSON Audit Event Dispatcher ]
```

---

## Key Features

- **Real-Time Edge Detection:** Evaluates 17 industrial classes including workers, hardhats, safety vests, protective masks, dump trucks, excavators, and wheel loaders.
- **Compliance Violation Rules:** Automated logic detecting high-hazard rule breaches:
  - Worker detected without hardhat (`no-hardhat`).
  - Worker detected without high-visibility safety vest (`no-safety vest`).
  - Personnel within active machinery zones.
- **Client-Side Privacy Guarantee:** 100% in-browser processing. Zero video bytes or photographic frames leave the client device.
- **Dual Operating Modes:**
  - **Instant Interactive Mode:** Pre-cached high-precision inference for instantaneous demonstration and compliance verification without network latency.
  - **Live Edge ONNX Inference:** Dynamic WebAssembly model execution on arbitrary user-supplied image and video streams.
- **Structured Audit Reporting:** Generates exportable, timestamped JSON compliance audit trails for occupational health and safety (OHS) recordkeeping.

---

## Model Benchmark & Evaluation

The underlying detector was fine-tuned on comprehensive industrial safety datasets using ultralytics YOLO11n with Mosaic and MixUp augmentations.

| Metric | Benchmark Result | Notes |
| :--- | :--- | :--- |
| **Model Backbone** | YOLO11n (Nano) | Optimized for mobile/edge embedded devices |
| **Input Dimensions** | 640 x 640 x 3 | Normalized letterbox RGB |
| **Validation mAP@0.50** | 84.6% | Comprehensive across all 17 classes |
| **Validation mAP@0.50:0.95** | 62.4% | Strict spatial IoU overlap |
| **Warm Inference Latency** | 22 ms | On NVIDIA T4 / A100 GPU |
| **Browser Wasm Latency** | 45 - 65 ms | Desktop Chrome (CPU SIMD Wasm) |
| **Model Size** | ~10.4 MB | ONNX FP32 weight graph |

---

## Supported Detection Classes

- **Personnel & Protective Equipment:** `person`, `hardhat`, `safety vest`, `gloves`, `mask`, `safety shoes`, `safety net`.
- **Compliance Hazards & Violations:** `no-hardhat`, `no-safety vest`, `no-mask`.
- **Heavy Construction Equipment & Site Elements:** `excavators`, `dump truck`, `wheel loader`, `truck`, `mini-van`, `barricade`, `dumpster`.

---

## Repository Structure

```
SiteSight-Safety-Vision/
├── docs/                               # Production GitHub Pages distribution
│   ├── index.html                      # Interactive safety audit web application
│   ├── style.css                       # Modern dark-mode responsive stylesheet
│   ├── app.js                          # Canvas engine, inference controller, NMS
│   ├── sample-construction.jpg         # High-resolution benchmark scene
│   ├── site_clip.mp4                   # Real active construction video stream
│   ├── models/
│   │   └── best.onnx                   # Optimized YOLO11 ONNX inference graph
│   └── vendor/
│       ├── ort.wasm.min.js             # ONNX Runtime Web loader
│       └── ort-wasm-simd-threaded.wasm # Multithreaded SIMD WASM binary
├── notebooks/
│   └── Construction_Safety_AI.ipynb    # Executed PyTorch training & evaluation notebook
├── models/
│   └── best.pt                         # Trained PyTorch checkpoint
└── README.md                           # Technical documentation
```

---

## Quickstart & Local Execution

### 1. Web Demonstration (Static)
No build steps or server runtimes required. Simply serve the repository root with any HTTP server:

```bash
# Clone the repository
git clone https://github.com/Abdulqader-Alshihabi/SiteSight-Safety-Vision.git
cd SiteSight-Safety-Vision

# Launch local server
python -m http.server 8080 --directory docs
```
Navigate to `http://localhost:8080` in any modern web browser.

### 2. Python Inference & Training Pipeline
To inspect model weights or run batch evaluation using PyTorch:

```bash
# Create and activate virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: .\venv\Scripts\activate

# Install dependencies
pip install ultralytics onnxruntime opencv-python pillow

# Run evaluation inference
python -c "
from ultralytics import YOLO
model = YOLO('models/best.pt')
results = model.predict('docs/sample-construction.jpg', conf=0.30, show=True)
"
```

---

## Author & Engineering Credentials

**Abdulqader Alshihabi**  
Computer Science Graduate · Islamic University of Madinah (GPA: 4.62 / 5.00, First Class Honors)  
Software & AI Engineer specializing in Full-Stack Architecture, Deep Learning, and Cloud Systems.

- **Portfolio:** [https://abdulqader-alshihabi.github.io/](https://abdulqader-alshihabi.github.io/)
- **GitHub:** [@Abdulqader-Alshihabi](https://github.com/Abdulqader-Alshihabi)
- **LinkedIn:** [https://www.linkedin.com/in/Abdulqader-Alshihabi/](https://www.linkedin.com/in/Abdulqader-Alshihabi/)
- **Email:** [abdulqader.moh.2003@gmail.com](mailto:abdulqader.moh.2003@gmail.com)

---

## License

This project is licensed under the MIT License. See [LICENSE](LICENSE) for details.
