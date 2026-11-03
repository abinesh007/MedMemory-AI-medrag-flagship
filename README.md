# MedMemory AI - Your Private Medical Knowledge Assistant

**An open-source, privacy-first, offline-capable medical RAG application that helps patients and caregivers understand personal medical records, handwritten prescriptions, laboratory reports, and evidence-based medical information through AI-powered retrieval and natural-language explanations.**

<p align="center">
  <strong>Private by Design · Open Source · Local AI · Evidence-Grounded RAG</strong>
</p>

---

## Overview

MedMemory AI is a personal medical knowledge assistant designed to make healthcare information more accessible and understandable for patients, caregivers, and individuals without formal medical knowledge.

Medical information is often scattered across hospital discharge summaries, laboratory reports, handwritten prescriptions, consultation notes, and other clinical documents. Understanding these documents and connecting information across different medical encounters can be challenging.

MedMemory AI aims to address this problem by combining **Optical Character Recognition (OCR), Handwritten Text Recognition (HTR), Retrieval-Augmented Generation (RAG), local Large Language Models (LLMs), and medical knowledge retrieval** into a single mobile application.

Users can scan or upload their medical documents, build a searchable personal medical history, and ask questions in natural language. The application retrieves relevant information from personal records and credible medical knowledge sources, then generates simplified, evidence-grounded explanations with references.

Unlike conventional cloud-based AI assistants, MedMemory AI is designed around a local-first architecture. Personal medical documents, conversation history, embeddings, and AI inference are intended to remain on the user's device.

External internet connectivity is optional and primarily used to retrieve updated medical literature, clinical guidelines, and trusted health information.

## Vision

To build a lightweight, open-source and privacy-preserving personal medical knowledge system that helps people understand their healthcare journey without requiring advanced medical knowledge or sharing sensitive medical information with third-party AI services.

## Core Features

### 1. Intelligent Medical Document Processing

Upload, scan, and organize different types of medical documents.

Supported document types include:

- Handwritten medical prescriptions.
- Digitally generated and scanned PDFs.
- Laboratory and diagnostic reports.
- Hospital discharge summaries.
- Doctor consultation notes.
- Medical history documents.
- Other healthcare-related text documents.

Document processing combines OCR, image enhancement, text extraction, and document classification to convert unstructured medical information into searchable content.

### 2. Handwritten Prescription Recognition

Handwritten prescriptions are processed using specialized image-based handwriting recognition models.

The processing pipeline includes:

- Image preprocessing and enhancement.
- Handwriting region detection.
- Text recognition.
- Medical entity extraction.
- Medication name and strength extraction.
- Dosage, frequency, duration, and route extraction.
- Confidence estimation and human verification.

Recognized information is presented alongside the original prescription image for user review.

**Safety principle:** Uncertain medication names, strengths, and dosage instructions must never be silently interpreted as verified medical information. User confirmation is required before such information enters verified medication records.

### 3. Personal Medical Memory

MedMemory AI builds a persistent, searchable medical history from uploaded documents and previous conversations.

It organizes information into structured categories:

- Medical conditions and documented diagnoses.
- Medication history.
- Allergies.
- Laboratory results.
- Previous consultations.
- Hospital visits and discharge summaries.
- Relevant medical observations.
- Conversation summaries.

A chronological medical timeline helps users understand how their medical records evolve over time.

The application distinguishes between information extracted from documents, information confirmed by users, and information discussed in conversations.

### 4. Local Retrieval-Augmented Generation (RAG)

A local RAG engine enables users to search their medical history through natural-language questions.

The retrieval pipeline combines:

- Document parsing and chunking.
- Local text embeddings.
- Semantic similarity search.
- Keyword-based retrieval.
- Metadata filtering.
- Context ranking and retrieval.
- Evidence-aware answer generation.

Example questions:

- "What did my previous blood report indicate?"
- "Which medicines were prescribed during my last consultation?"
- "Compare my latest laboratory results with the previous report."
- "What does this medical terminology mean?"
- "Summarize my medical history in simple language."

### 5. Evidence-Based Medical Knowledge Retrieval

MedMemory AI aims to retrieve relevant information from credible medical education platforms, government health institutions, clinical guidelines, and biomedical research publications.

Planned knowledge sources include:

| Source | Purpose |
|---|---|
| WHO | Global health information and guidance |
| MedlinePlus | Patient-friendly medical explanations |
| NHS | Medical conditions, symptoms, and treatment information |
| NIH | Biomedical research and health information |
| PubMed | Peer-reviewed biomedical literature |
| Europe PMC | Biomedical publications and accessible research |
| NICE | Evidence-based clinical guidelines |
| ICMR | Indian medical research and health guidance |

External knowledge retrieval is designed to use official APIs and approved sources wherever possible.

Source quality, publication date, relevance, and evidence strength are considered during retrieval.

Personal medical documents are never intended to be uploaded to external search providers. Search queries should be sanitized to avoid exposing personally identifiable information.

### 6. Grounded, Patient-Friendly AI Explanations

The local LLM generates explanations using retrieved personal records and external medical evidence.

The response generation pipeline is designed to:

1. Understand the user's question.
2. Retrieve relevant personal medical information.
3. Retrieve relevant external medical evidence when connected.
4. Evaluate the relevance and quality of retrieved sources.
5. Identify conflicting or insufficient information.
6. Generate an accessible explanation.
7. Provide supporting references.
8. Communicate uncertainty and appropriate safety guidance.

Answers are structured around:

- What the medical document says.
- What the information means in simple language.
- What credible medical references explain.
- What remains uncertain.
- Questions the patient can discuss with their healthcare professional.
- Supporting references and source links.

The assistant is intended to explain and organize medical information, not independently diagnose conditions or prescribe treatment.

### 7. Persistent Conversation Memory

The application maintains conversation context across sessions through locally stored memory.

Three memory layers are planned:

- **Short-term memory:** Recent messages within the current conversation.
- **Long-term conversational memory:** Summaries and relevant information from previous conversations.
- **Personal medical memory:** Structured information retrieved from the user's medical documents and verified records.

This enables contextual follow-up questions without requiring users to repeatedly explain their medical history.

### 8. Privacy-First and Offline-Capable Architecture

Privacy is a fundamental design requirement.

The application is intended to operate without mandatory cloud infrastructure, centralized patient databases, or paid AI APIs.

Privacy features include:

- On-device document processing.
- Local OCR and handwriting recognition.
- Local embedding generation.
- Local LLM inference.
- Encrypted medical document storage.
- Encrypted local database.
- Android Keystore-based encryption key protection.
- No mandatory account registration.
- No advertising or third-party tracking.
- Optional internet connectivity.
- User-controlled medical data deletion.

Offline functionality is designed to support document scanning, personal history retrieval, previous conversations, and local AI generation using previously available information.

Fresh external medical research requires an internet connection.

---

## System Architecture

MedMemory AI follows a modular, local-first architecture.

```text
              ANDROID APPLICATION
             Kotlin + Jetpack Compose
                       |
          +------------+------------+
          |                         |
   DOCUMENT PROCESSING        MEDICAL MEMORY
          |                         |
   CameraX / PDF             Room / SQLite
          |                         |
   OpenCV / OCR              Structured Records
          |                         |
   Handwriting HTR           Conversation Memory
          |                         |
          +------------+------------+
                       |
                LOCAL RAG ENGINE
                       |
          +------------+------------+
          |                         |
    SEMANTIC SEARCH           KEYWORD SEARCH
          |                         |
    Local Embeddings          SQLite FTS5
          |                         |
          +------------+------------+
                       |
             EXTERNAL KNOWLEDGE
                       |
          +------------+------------+
          |                         |
       PubMed                  Europe PMC
          |                         |
     WHO / NHS / NICE / MedlinePlus / ICMR
                       |
              EVIDENCE RETRIEVAL
                       |
               LOCAL LLM ENGINE
                       |
              llama.cpp / GGUF
                       |
             GROUNDED GENERATION
                       |
          PATIENT-FRIENDLY RESPONSE
                       |
             CITATIONS + SOURCES
```

## Technology Stack

| Component | Technology |
|---|---|
| Mobile application | Kotlin |
| UI framework | Jetpack Compose |
| Camera integration | CameraX |
| Image processing | OpenCV |
| Printed OCR | Google ML Kit |
| Handwriting recognition | PaddleOCR / TrOCR-based HTR |
| PDF processing | Android PDF APIs / PDFBox |
| Local database | Room + SQLite |
| Keyword retrieval | SQLite FTS5 |
| Vector retrieval | SQLite-based vector indexing |
| Embedding models | multilingual-e5-small / BGE-small |
| Local LLM inference | llama.cpp |
| Model format | GGUF |
| Model quantization | 4-bit quantization |
| Medical literature | PubMed E-utilities / Europe PMC APIs |
| Encryption | AES-GCM + Android Keystore |
| AI experimentation | Python + PyTorch |

Technology selections may evolve as mobile performance, compatibility, and model evaluation progress.

## Privacy Architecture

MedMemory AI follows a local-first data architecture.

| Operation | Processing location |
|---|---|
| Document scanning | Device |
| OCR and text extraction | Device |
| Handwriting recognition | Device |
| Medical record storage | Device |
| Embedding generation | Device |
| Personal document retrieval | Device |
| Conversation memory | Device |
| LLM inference | Device |
| External medical research | Internet-connected retrieval |
| Patient data synchronization | Not required |

External medical searches should use generic, sanitized queries rather than transmitting original prescriptions, laboratory reports, patient names, or complete medical histories.

## Development Roadmap

The project is planned in the following phases:

- [ ] **Phase 0:** Project foundation and architecture.
- [ ] **Phase 1:** Medical document ingestion and classification.
- [ ] **Phase 2:** Printed OCR and handwritten prescription recognition.
- [ ] **Phase 3:** Personal medical memory and structured database.
- [ ] **Phase 4:** Local embeddings and hybrid RAG retrieval.
- [ ] **Phase 5:** External medical knowledge retrieval.
- [ ] **Phase 6:** Local LLM integration and grounded generation.
- [ ] **Phase 7:** Android application interface and user experience.
- [ ] **Phase 8:** Encryption, privacy, and offline functionality.
- [ ] **Phase 9:** Medical safety evaluation and testing.
- [ ] **Phase 10:** Open-source release.

*Development status: Initial project planning and architecture definition.*

## Design Principles

1. **Privacy by Design:** Personal medical information should remain under the user's control.
2. **Local-First AI:** Prefer on-device inference and processing over cloud-dependent services.
3. **Evidence Over Hallucination:** Medical explanations must be grounded in identifiable sources.
4. **Human Verification:** Critical information extracted from handwritten prescriptions must be reviewed.
5. **Accessible Healthcare Information:** Explain complex medical terminology in simple language.
6. **Persistent Context:** Maintain useful medical and conversational history across sessions.
7. **Lightweight Deployment:** Optimize model size, memory usage, inference speed, and battery consumption.
8. **Open Source:** Build with transparent, reusable, and appropriately licensed technologies.

## Intended Audience

- Patients managing long-term medical records.
- Caregivers assisting family members.
- Individuals managing multiple prescriptions and laboratory reports.
- People who find medical terminology difficult to understand.
- Individuals who want to organize and understand their medical history.
- Developers and researchers interested in privacy-preserving medical AI and on-device RAG.

## Medical Disclaimer

MedMemory AI is intended to support medical information understanding, document organization, and patient education.

It is not a substitute for professional medical advice, diagnosis, or treatment. AI-generated explanations may contain errors, and OCR may misinterpret medical handwriting.

Users should verify medication information with a qualified healthcare professional and should not start, stop, or modify medication based solely on AI-generated responses.

The application must not be used for emergency medical decision-making.

## Open-Source and Contributions

MedMemory AI is intended to be developed as an open-source project.

Contributions related to mobile development, OCR, handwriting recognition, local AI inference, retrieval systems, medical document processing, privacy, and evaluation are welcome.

Before contributing, review the licensing requirements of individual AI models, datasets, and external medical resources.

## License

Planned application license: **Apache License 2.0**.

Individual model weights, datasets, and third-party dependencies remain subject to their respective licenses.

---

**MedMemory AI — Making personal medical knowledge accessible, understandable, and private.**
