# 🏥 Medical Assistant — Intelligent Healthcare RAG System

> A production-grade **Retrieval-Augmented Generation (RAG)** system for evidence-based medical question answering, built on the MIMIC-IV clinical dataset.

[![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)](https://python.org)
[![Gradio](https://img.shields.io/badge/Gradio-Interface-F97316?logo=gradio)](https://gradio.app)
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
- [Sharing the Gradio App](#-sharing-the-gradio-app)
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

🟢 **Live App:** [https://485a585f59f31a9ffd.gradio.live/](https://485a585f59f31a9ffd.gradio.live/)

> Note: Gradio share links expire after 72 hours. To run permanently, deploy on Hugging Face Spaces or run locally with `share=True`.

---

## ✨ Features

| Feature | Description |
|---|---|
| 🔍 Semantic Search | Dense embedding retrieval using `all-MiniLM-L6-v2` via FAISS |
| 🧠 AI Generation | Llama 3.3 70B (Groq free tier) for context-grounded answers |
| 📚 Rich Knowledge Base | 48 medical conditions × knowledge graphs + 1,000+ patient cases |
| 📄 Source Citation | Every answer links back to retrieved clinical evidence chunks |
| 🌐 Web Interface | Gradio app with chat interface, source display, and shareable public link |
| ⚡ Fast Inference | Groq delivers ~500 tokens/second — near-instant responses |
| 🔒 Hallucination Guard | Prompt-level constraint: model answers *only* from retrieved context |

---

## 🏗️ System Architecture

```
User Question
      │
      ▼
┌─────────────────┐
│   Gradio UI     │  ← Chat interface with public share link
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
| Web Framework | Gradio |
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
├── RAG_v2_clean.ipynb              # Main Jupyter Notebook (Colab)
├── requirements.txt                # Python dependencies
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

- A Google account (for Google Colab)
- A free [Groq API key](https://console.groq.com) — signup takes 2 minutes
- The MIMIC-IV dataset RAR file

### Option 1: Run on Google Colab (Recommended)

This is the primary way to run the project — no local setup required.

**1. Open the notebook in Colab**

Upload `RAG_v2_clean.ipynb` to [Google Colab](https://colab.research.google.com) or open it directly from GitHub.

**2. Upload the dataset**

In the Colab file panel, upload `mimic-iv-ext-direct-1.0.rar` to `/content/`.

**3. Add your Groq API key**

In the notebook, find Cell 6 and replace the placeholder:

```python
GROQ_API_KEY = "gsk_your_actual_key_here"
```

Or use Colab Secrets (recommended):
- Click the 🔑 **Secrets** tab in the left panel
- Add a secret named `GROQ_API_KEY` with your key

**4. Run all cells**

Click **Runtime → Run all**. The notebook will:
1. Install `unrar` and extract the dataset (~30 seconds)
2. Install all Python dependencies (~1 minute)
3. Build the FAISS index (~2–3 minutes on GPU, ~5 minutes on CPU)
4. Launch the Gradio interface with a public share link

**5. Access the app**

Once Cell 8 (Gradio interface) runs, you'll see a public URL like:

```
Running on public URL: https://xxxxxxxxxxxxxxxx.gradio.live
```

Share this link with anyone — it stays live as long as your Colab session is running.

---

### Option 2: Run Locally

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
pip install gradio langchain langchain-community langchain-huggingface \
            sentence-transformers faiss-cpu groq
```

**4. Place the dataset**

Copy `mimic-iv-ext-direct-1.0.rar` into the project root.

**5. Set your Groq API key**

```bash
export GROQ_API_KEY="gsk_your_actual_key_here"
```

**6. Run the notebook or launch Gradio directly**

```bash
jupyter notebook RAG_v2_clean.ipynb
```

---

## 🌐 Sharing the Gradio App

Gradio automatically creates a shareable public link when `share=True` is set:

```python
demo.launch(share=True)
```

| Method | Link Duration | Notes |
|---|---|---|
| Gradio share link | 72 hours | Auto-generated, no account needed |
| Hugging Face Spaces | Permanent | Free hosting, requires HF account |
| Google Colab | Session lifetime | Easiest for demos |

---

## ⚡ How It Works

### Query Processing (Online)

```
1. User types question in Gradio chat interface
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
Built with ❤️ using LangChain · FAISS · Groq · Gradio · MIMIC-IV
</p>
