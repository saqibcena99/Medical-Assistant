# 🏥 Medical Assistant — Intelligent Healthcare RAG System

> A production-grade **Retrieval-Augmented Generation (RAG)** system for evidence-based medical question answering, built on the MIMIC-IV clinical dataset.

[![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)](https://python.org)
[![Streamlit](https://img.shields.io/badge/Streamlit-App-FF4B4B?logo=streamlit)](https://streamlit.io)
[![LangChain](https://img.shields.io/badge/LangChain-RAG-1C3C3C?logo=chainlink)](https://langchain.com)
[![FAISS](https://img.shields.io/badge/FAISS-Vector_Store-0866FF)](https://github.com/facebookresearch/faiss)
[![Groq](https://img.shields.io/badge/Groq-Llama_3.3_70B-F55036)](https://console.groq.com)
[![License](https://img.shields.io/badge/License-Educational-green)](LICENSE)

---

## 📋 Table of Contents

- [Overview](#-overview)
- [Live Demo](#-live-demo)
- [Features](#-features)
- [System Architecture](#-system-architecture)
- [Dataset](#-dataset)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Installation & Setup](#-installation--setup)
- [Deployment on Streamlit Cloud](#-deployment-on-streamlit-cloud)
- [How It Works](#-how-it-works)
- [Sample Results](#-sample-results)
- [Limitations](#-limitations)
- [Disclaimer](#-disclaimer)

---

## 🔍 Overview

Medical Assistant is a domain-specific question-answering system that combines **semantic information retrieval** with **large language model generation** to produce accurate, evidence-grounded answers to medical queries.

Unlike a standard chatbot, every answer is derived exclusively from retrieved clinical evidence — not model memory — dramatically reducing hallucination risk. The system covers **48 medical conditions** (cardiology, neurology, gastroenterology, pulmonology, endocrinology, and more) sourced from the MIMIC-IV Extended clinical dataset.

---

## 🚀 Live Demo

> Deploy your own instance in minutes — see [Deployment](#-deployment-on-streamlit-cloud) below.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔍 Semantic Search | Dense embedding retrieval using `all-MiniLM-L6-v2` via FAISS |
| 🧠 AI Generation | Llama 3.3 70B (Groq free tier) for context-grounded answers |
| 📚 Rich Knowledge Base | 48 medical conditions × knowledge graphs + 1,000+ patient cases |
| 📄 Source Citation | Every answer links back to retrieved clinical evidence chunks |
| 🌐 Web Interface | Polished Streamlit app with three tabs: Q&A, Browse, About |
| ⚡ Fast Inference | Groq delivers ~500 tokens/second — near-instant responses |
| 🔒 Hallucination Guard | Prompt-level constraint: model answers *only* from retrieved context |

---

## 🏗️ System Architecture

```
User Question
      │
      ▼
┌─────────────────┐
│  Streamlit UI   │  ← Web interface (3 tabs)
└────────┬────────┘
         │
         ▼
┌─────────────────────────────────────────────┐
│              RAG Pipeline                   │
│                                             │
│  ┌──────────────┐    ┌────────────────────┐ │
│  │   Embedder   │    │   FAISS Index      │ │
│  │ MiniLM-L6-v2 │───▶│  (384-dim vectors) │ │
│  └──────────────┘    └────────┬───────────┘ │
│                               │ top-k=5     │
│                               ▼             │
│                    ┌──────────────────────┐ │
│                    │  Retrieved Chunks    │ │
│                    │  (context window)    │ │
│                    └──────────┬───────────┘ │
│                               │             │
│                               ▼             │
│                    ┌──────────────────────┐ │
│                    │  Groq Llama 3.3 70B  │ │
│                    │  (answer generation) │ │
│                    └──────────────────────┘ │
└─────────────────────────────────────────────┘
         │
         ▼
  Grounded Answer + Source Citations
```

**Knowledge Base Construction (offline):**

```
MIMIC-IV RAR Archive
        │
        ├── diagnostic_kg/          ← 48 condition JSON files
        │   └── extract_knowledge_chunks()
        │       ├── Risk Factor chunks (~200–400)
        │       └── Symptom chunks (~200–400)
        │
        └── Finished/               ← 1,000+ patient case JSON files
            └── extract_case_chunks()
                ├── Narrative chunks (~1,000+)
                └── Reasoning chunks (~1,000+)
                        │
                        ▼
               FAISS Vector Index (~2,500–3,000 vectors)
```

---

## 📊 Dataset

This project uses the **MIMIC-IV Extended Direct (v1.0)** dataset.

| Property | Value |
|---|---|
| Source | Beth Israel Deaconess Medical Center (de-identified) |
| Format | JSON (Knowledge Graphs + Patient Cases) |
| Conditions Covered | 48 medical conditions |
| Patient Cases | 1,000+ de-identified clinical cases |
| Knowledge Structure | Diagnostic flowcharts with risk factors & symptoms |

**Medical Specialties Covered:**
- Cardiology (Heart Failure, ACS, Arrhythmia)
- Neurology (Migraine, Stroke, Multiple Sclerosis)
- Gastroenterology (Upper GI Bleeding, Pancreatitis, Hepatitis)
- Pulmonology (Pneumonia, COPD, Pulmonary Embolism)
- Endocrinology (Diabetes, Hypothyroidism, Adrenal Insufficiency)
- And more...

> **Note:** The dataset file (`mimic-iv-ext-direct-1.0.rar`) must be obtained separately and placed in the project root before running.

---

## 🛠️ Tech Stack

| Component | Technology |
|---|---|
| Language | Python 3.10 |
| Web Framework | Streamlit |
| RAG Orchestration | LangChain + LangChain-HuggingFace |
| Embedding Model | `sentence-transformers/all-MiniLM-L6-v2` |
| Vector Store | FAISS (Facebook AI Similarity Search) |
| LLM | Llama 3.3 70B via Groq API (free tier) |
| Archive Extraction | unrar-free (system package) |

---

## 📁 Project Structure

```
medical-assistant/
│
├── app.py                          # Main Streamlit application
├── requirements.txt                # Python dependencies
├── packages.txt                    # System packages (unrar-free)
├── setup.sh                        # Optional setup script
├── README.md                       # This file
│
└── mimic-iv-ext-direct-1.0.rar    # Dataset (place here before running)
```

After extraction, the dataset expands to:

```
mimic-iv-ext-direct-1.0/
└── mimic-iv-ext-direct-1.0.0/
    ├── diagnostic_kg/
    │   └── Diagnosis_flowchart/
    │       ├── migraine.json
    │       ├── heart_failure.json
    │       └── ... (48 files total)
    └── Finished/
        ├── migraine/
        │   ├── case_001.json
        │   └── ...
        └── ... (1,000+ cases across conditions)
```

---

## ⚙️ Installation & Setup

### Prerequisites

- Python 3.10+
- A free [Groq API key](https://console.groq.com) (takes 2 minutes to get)
- The MIMIC-IV dataset RAR file

### Local Setup

**1. Clone the repository**

```bash
git clone https://github.com/yourusername/medical-assistant.git
cd medical-assistant
```

**2. Install system dependency**

```bash
# Ubuntu/Debian
sudo apt-get install unrar-free

# macOS
brew install unar
```

**3. Install Python dependencies**

```bash
pip install -r requirements.txt
```

**4. Place the dataset**

Copy `mimic-iv-ext-direct-1.0.rar` into the project root directory.

**5. Set your Groq API key**

Create a `.streamlit/secrets.toml` file:

```toml
GROQ_API_KEY = "gsk_your_actual_key_here"
```

Or set as an environment variable:

```bash
export GROQ_API_KEY="gsk_your_actual_key_here"
```

**6. Run the app**

```bash
streamlit run app.py
```

The app will open at `http://localhost:8501`. On first run, it will extract the dataset and build the FAISS index — this takes 2–5 minutes. Subsequent runs load the cached index instantly.

---

## ☁️ Deployment on Streamlit Cloud

**1. Push all files to GitHub** (including `packages.txt` — this is critical):

```
app.py
requirements.txt
packages.txt          ← must contain "unrar-free"
setup.sh
mimic-iv-ext-direct-1.0.rar
README.md
```

**2. Connect to Streamlit Cloud**

- Go to [share.streamlit.io](https://share.streamlit.io)
- Click **New app** → select your GitHub repo
- Set **Main file path** to `app.py`

**3. Add your API key as a Secret**

- In the app settings, go to **Settings → Secrets**
- Add:

```toml
GROQ_API_KEY = "gsk_your_actual_key_here"
```

**4. Deploy**

Click **Deploy**. Streamlit Cloud will:
1. Install `unrar-free` from `packages.txt` (OS-level)
2. Install Python packages from `requirements.txt`
3. Launch the app

> ⚠️ **Important:** `packages.txt` must say `unrar-free` (not `unrar`) — the standard `unrar` package is not available in Debian's default repositories used by Streamlit Cloud.

---

## ⚡ How It Works

### Query Processing (Online)

```
1. User types question in Streamlit UI
2. Question → all-MiniLM-L6-v2 → 384-dim query vector
3. FAISS index searched → top-5 most similar chunks returned
4. Chunks concatenated → injected into prompt template
5. Prompt sent to Groq (Llama 3.3 70B)
6. Answer generated with temperature=0.3 (factual, low randomness)
7. Answer + source chunks displayed in UI
```

### Knowledge Base Construction (Offline, runs once)

```
1. RAR archive extracted automatically on first launch
2. 48 KG JSON files parsed → risk factor + symptom chunks created
3. 1,000+ case JSON files parsed → narrative + reasoning chunks created
4. All chunks → all-MiniLM-L6-v2 → 384-dim vectors
5. Vectors indexed in FAISS (exact search, IndexFlatIP)
6. Index cached with @st.cache_resource
```

### Prompt Design (Hallucination Guard)

```
SYSTEM: You are an expert medical AI assistant.

USER:   Answer using ONLY the provided medical context.
        If context is insufficient, clearly state what is missing.

        === MEDICAL CONTEXT ===
        [Top-5 retrieved chunks]

        === QUESTION ===
        [User's question]

        === ANSWER ===
        Provide a clear, structured, accurate answer.
```

---

## 📈 Sample Results

| Query | Result |
|---|---|
| "What are the symptoms of migraine?" | ✅ Detailed multi-system symptom breakdown (visual, neurological, GI, musculoskeletal) |
| "How is chest pain evaluated in emergency settings?" | ✅ ECG, troponins, risk stratification, differential diagnosis |
| "Risk factors for heart failure?" | ✅ Modifiable + non-modifiable factors with clinical depth |
| "How is diabetes diagnosed and managed?" | ✅ Diagnostic criteria (glucose/HbA1c thresholds) + treatment ladder |
| "Cause of heart attack in monkeys?" | ✅ Appropriate refusal — no monkey data in corpus, no hallucination |
| "How does cold weather affect body systems?" | ✅ Multi-condition synthesis across cardiology, pulmonology, endocrinology |

**Retrieval: Dense Embeddings vs TF-IDF**

Dense embedding retrieval outperforms TF-IDF on 7 of 8 test queries — particularly for paraphrased questions, semantically complex queries, and cases where clinical terminology differs from the query language.

---

## ⚠️ Limitations

- **Knowledge cutoff:** Answers reflect the MIMIC-IV dataset's time period. Recent drug approvals or updated guidelines are not included.
- **48 conditions only:** Rare diseases or conditions outside the dataset yield weaker answers.
- **No numerical reasoning:** Cannot compute drug dosages, GFR estimates, or other calculations.
- **General embedding model:** `all-MiniLM-L6-v2` is not biomedical-specific; domain-tuned models (BioLinkBERT) may improve precision.
- **English only:** The corpus and model are English-language; non-English queries are not supported.

---

## 🚨 Disclaimer

> **This system is for educational and research purposes only.**
>
> It does **not** provide medical advice, diagnosis, or treatment recommendations. Information generated by this system should **never** replace consultation with a qualified healthcare professional. Always seek the advice of your physician or other qualified health provider with any questions you may have regarding a medical condition.

---

## 📄 License

This project is submitted for academic evaluation. The MIMIC-IV dataset is subject to its own [PhysioNet Data Use Agreement](https://physionet.org/content/mimiciv/). The source code is for educational purposes only.

---

<p align="center">
Built with ❤️ using LangChain · FAISS · Groq · Streamlit · MIMIC-IV
</p>
