# SIH-26188
AI-powered border &amp; immigration screening using OCR, document validation, tampering detection, face verification, and risk scoring.
# 🛡️ AI-Based Fake Identity & Document Screening System

[![Smart India Hackathon](https://img.shields.io/badge/SIH_2026-Problem_Statement_26188-blue.svg)](https://sih.gov.in)
[![Organization](https://img.shields.io/badge/Client-Sashastra_Seema_Bal_(SSB)-green.svg)]()
[![Theme](https://img.shields.io/badge/Theme-Blockchain_&_Cybersecurity-orange.svg)]()
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

An enterprise-grade, edge-deployable AI platform designed for the **Ministry of Home Affairs (MHA)** and **Sashastra Seema Bal (SSB)**. This system automates identity verification at land border checkpoints, reducing document screening time from several minutes to **under 2 seconds** while detecting sophisticated digital and physical forgeries.

---

## 📌 The Challenge (SIH 2026 - PS 26188)
Border checkpoints process thousands of identity documents daily (passports, visas, national IDs, permits). Current manual verification is:
* **Time-consuming:** Causing heavy passenger delays.
* **Prone to Human Error:** Vulnerable to fatigue and subjectivity.
* **Ineffective against Deep Forgeries:** Unable to catch high-tech photo splicing, altered dates, or forged stamps.

**Objective:** Develop an AI-powered document screening platform that automatically analyzes travel documents, detects tampering, validates rules, and generates a risk score to assist border personnel.

---

## 💡 Our Solution
We built a highly scalable, 4-module computer vision and deep learning pipeline. The system extracts data, validates international checksum rules, performs deep visual forensics, and matches traveler biometrics in real-time. 

Instead of a black-box AI, the system feeds all metrics into an **XGBoost Risk Engine** to generate a precise **0–100 Risk Score** (Green/Yellow/Red). All officer decisions are logged to an immutable **Blockchain ledger** for a tamper-proof audit trail.

---

## ⚙️ Core Modules (The AI Pipeline)

### 📄 Module 1: OCR Extraction
Automatically extracts Visual Inspection Zone (VIZ) text and parses the Machine Readable Zone (MRZ).
* **Extracted Fields:** Name, Passport/Visa Number, Nationality, DOB, Expiry Date, Gender, Visa Type.
* **Models Used:** LayoutLMv3, PaddleOCR, TrOCR.

### 🛡️ Module 2: Document Validation
Verifies if extracted information follows official document standards.
* **Checks:** ICAO Doc 9303 modulo-10 checksum validation.
* **Cross-matching:** Verifies VIZ text against MRZ text strings.
* **Database Lookups:** Ultra-low latency Redis lookups for Interpol/national watchlists.

### 🔍 Module 3: Tampering Detection (Core AI Innovation)
Detects digitally or physically altered documents using deep visual forensics.
* **Photo Replacement:** Uses Error Level Analysis (ELA) to catch compression anomalies and splicing.
* **Text/Ink Manipulation:** Applies Spatial Rich Model (SRM) high-pass filters to detect font mismatches and altered digits.
* **Stamp Forgery:** HSV color segmentation + Siamese Neural Networks.
* **Explainability (XAI):** Generates **Grad-CAM heatmaps** to show officers exactly *where* the document was manipulated.

### 👤 Module 4: Face Verification
Ensures the document owner matches the presented individual.
* **Pipeline:** Extracts the printed face $\rightarrow$ aligns it using SCRFD $\rightarrow$ matches against live webcam feed.
* **Embedding Model:** ArcFace (ResNet-100) via InsightFace.
* **Security:** MiniFASNet anti-spoofing liveness detection prevents screen replays and 3D mask attacks.

---

## 🚀 Technology Stack

| Component | Technologies Used |
| :--- | :--- |
| **Frontend Dashboard** | React.js (Vite), Tailwind CSS, Lucide React, Zustand |
| **Backend API Gateway** | FastAPI (Python), Uvicorn |
| **AI / Computer Vision** | PyTorch, OpenCV, TensorRT, InsightFace, Scikit-Image |
| **Database & Cache** | PostgreSQL (Relational), Redis (Watchlists), Milvus (Vector DB) |
| **Security & Audit** | Hyperledger Fabric (Blockchain), SHA-256 Hashing |

---

## 📁 Project Structure

```text
├── frontend/                # React.js Officer Dashboard
│   ├── src/
│   │   ├── components/      # UI Cards, Layouts, Modals
│   │   ├── hooks/           # Custom state & API hooks
│   │   └── App.jsx          # Main Dashboard View
├── backend/                 # FastAPI Python Server
│   ├── main.py              # API Gateway entry point
│   ├── routers/             # API routes (ocr, biometrics, audit)
│   └── core/                # Config, logging, security
├── ai_engine/               # Core Machine Learning Models
│   ├── module1_ocr/         # PaddleOCR / LayoutLMv3 scripts
│   ├── module2_rules/       # ICAO Checksum logic
│   ├── module3_tampering/   # ELA, SRM, Grad-CAM generation
│   └── module4_face/        # ArcFace & Liveness models
└── blockchain/              # Hyperledger smart contracts & node config                                                              --  ###📊 The Risk Engine Logic
The dashboard synthesizes a composite Risk Score (0-100):

🟢 0-30 (Low Risk): Valid MRZ, untouched photo/text, >90% face match. Auto-cleared.

🟡 31-65 (Medium Risk): Minor anomalies (e.g., poor scan quality, near expiry). Requires secondary inspection.

🔴 66-100 (High Risk): Tampered photo box, Checksum failure, Face mismatch, or Blacklist hit. Detain & escalate.                            Who Can Use This
Border checkpoint / immigration officers — primary users, via the review dashboard.
Airport and seaport security personnel — document screening at entry/exit points.
Police & law enforcement (SSB, Police II Division) — investigation and intelligence use via the audit trail.
Visa/immigration processing centers — pre-screening before manual review.
Government agencies (Ministry of Home Affairs) — policy-level analytics on forgery trends.
Hotels, banks, or KYC-heavy businesses (future extension) — document verification for onboarding                                           Expected Impact
Verification time reduced from several minutes to a few seconds per document.
Improved detection of forged and tampered documents over manual inspection.
Standardized, consistent screening decisions across checkpoints.
Data-driven risk assessment instead of purely manual judgment.
Digital audit trail for investigations and intelligence analysis.                                                                            Feasibility & Scalability

Feasibility

OCR (Tesseract/PaddleOCR) and face-matching (ArcFace/FaceNet) are mature, pretrained, open-source — no need to train from scratch, only fine-tune on document samples.
Tampering detection is achievable with ELA + a CNN classifier trained on genuine vs. edited-document pairs; metadata/EXIF checks add near-zero compute cost.
Modular microservice design lets each module be built, tested, and improved independently.
Can integrate with existing checkpoint kiosks/e-gates via REST APIs without replacing hardware.

Scalability

Stateless services behind a job queue (upload → queue → worker) scale horizontally to handle passenger surges.
GPU inference can be batched and load-balanced across multiple workers.
Database and image storage scale independently (PostgreSQL read replicas, object storage for images).
Designed to be deployed per-checkpoint or centrally, depending on network/latency requirements.

Constraints to plan for

Needs labeled forged-document datasets — limited public availability, may require synthetic data generation.
Requires access to government databases for blacklist/expiry validation.
Face verification accuracy depends on image quality at capture (lighting, camera angle).
