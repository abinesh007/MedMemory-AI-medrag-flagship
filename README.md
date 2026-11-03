# Medical RAG Assistant — Open-source, Privacy-first Mobile Application



Proposed architecture

Offline-first

Open-source



The application should function as a personal medical knowledge assistant and medical-document interpreter, designed for patients, caregivers and people without a medical background.

Its primary purpose is to help someone understand their medical history, prescriptions, laboratory reports, diagnoses and treatment-related information through simple, evidence-grounded explanations.

The important distinction is that this should not be another generic medical chatbot. It should combine three capabilities:

1. Personal Medical Memory: Understand and remember the user's uploaded medical documents and previous conversations.
2. Medical Knowledge Retrieval: Retrieve relevant information from credible medical education websites, clinical guidelines and peer-reviewed research.
3. Grounded Medical Explanation: Combine personal medical context with credible external evidence and explain the result in accessible language, with references and appropriate uncertainty.

## 1. Product vision and core requirements

# Your Personal Medical Knowledge Assistant

[Content Scheduler](https://images.openai.com/static-rsc-4/Bck__65N__QCAAMaPh8T4frpQ-Tha16F_3qkNXcYdZPLhZLMZzzX7P-Xi9VrpJPPlIQlfffko_tjlpIkrxUZVVtQWiPZ8zHYH2UUCoZI4JoYljegfKiVkF8fmkhUv98oqW_PSJc1T9E6XbKY7ypfKw76NRjRYNdwvdl5pGZ6xtw?purpose=inline)

[mediai.info](https://mediai.info/?utm_source=chatgpt.com)

Document intelligence

Scans PDFs, prescriptions, lab reports and handwritten notes.

Personal memory

Retains medical history and conversation context.

Evidence retrieval

Uses credible medical resources and research.

Privacy-first

Local storage, local inference and no mandatory cloud account.

### Requirements and how the architecture will satisfy them

| Your requirement            | Proposed solution                                                    |
| --------------------------- | -------------------------------------------------------------------- |
| Free to build               | Open-source frameworks and free public medical resources             |
| Open source                 | Publish source code, model configurations and documentation          |
| Private information         | Local-first processing and encrypted device storage                  |
| Medical history             | Structured patient-specific medical database                         |
| Handwritten prescriptions   | OCR plus handwriting recognition and human verification              |
| Internet research           | Curated medical-source retrieval pipeline                            |
| Grounded answers            | Hybrid RAG with source citations                                     |
| Layman-friendly             | Dedicated explanation and simplification layer                       |
| Remember previous chats     | Persistent conversation memory and summaries                         |
| Lightweight mobile app      | Quantized small language model and efficient local database          |
| Internet-independent access | Offline document search and local AI; online research when connected |

One important engineering decision: Build it as an offline-first application, not an exclusively offline application.

The application should work without internet for personal-document retrieval, historical conversations and basic explanations. Internet connectivity should be required only for fresh external medical research and source updates.

Also, free/open-source software does not mean every service is free: hosting, mobile hardware, electricity and optional external APIs can introduce costs. The core product should not depend on paid APIs.

## 2. Recommended technology stack

I recommend building the first version for Android, using native Kotlin. This gives you better control over local storage, camera access, document handling, background processing and on-device inference.

Your existing Python, PyTorch, Flask, RAG and medical AI experience can be reused for prototyping and testing, but the final mobile architecture should avoid depending on a continuously running Python server.

| Component          | Recommended technology              | Purpose                                   |
| ------------------ | ----------------------------------- | ----------------------------------------- |
| Mobile frontend    | Kotlin + Jetpack Compose            | Android application                       |
| Camera scanning    | CameraX                             | Capture prescriptions and documents       |
| PDF processing     | PDFBox / Android PDF APIs           | Extract and render PDFs                   |
| Printed OCR        | Google ML Kit Text Recognition      | Extract printed text locally              |
| Handwriting OCR    | PaddleOCR / specialized HTR model   | Recognize handwritten prescriptions       |
| Image enhancement  | OpenCV                              | Deskewing, denoising, contrast correction |
| Local database     | Room + SQLite                       | Store patient records                     |
| Database security  | Android Keystore + AES-GCM          | Protect local information                 |
| Vector retrieval   | sqlite-vec or local embedding index | Semantic search                           |
| Keyword retrieval  | SQLite FTS5                         | Exact text and medication searches        |
| Embedding model    | multilingual-e5-small or BGE-small  | Convert documents into vectors            |
| Local LLM          | llama.cpp / LiteRT-LM               | Generate answers locally                  |
| Model format       | GGUF, quantized models              | Reduce memory requirements                |
| Backend            | None for core functionality         | Avoid centralized patient-data storage    |
| Medical research   | PubMed / Europe PMC APIs            | Find scientific literature                |
| Medical references | WHO, MedlinePlus, NHS, NICE, ICMR   | Trusted medical information               |

Important technology choices:

- ML Kit is useful for printed OCR and supports on-device processing.&#x20;

  [image](https://www.google.com/s2/favicons?domain=https://developers.google.com\&sz=32)

  Google for Developers+1

- For handwriting, ML Kit's Digital Ink Recognition is not the solution for photographed prescriptions. It recognizes digital pen strokes, not handwriting captured by a camera. You need a separate image-based handwriting recognition model.&#x20;

  [image](https://www.google.com/s2/favicons?domain=https://developers.google.com\&sz=32)

  Google for Developers+1

- llama.cpp has an Android implementation for running GGUF models locally.&#x20;

  [image](https://www.google.com/s2/favicons?domain=https://github.com\&sz=32)

  GitHub

- Google's current Android AI documentation recommends LiteRT-LM for new on-device LLM development rather than its older MediaPipe LLM Inference API.&#x20;

  [image](https://www.google.com/s2/favicons?domain=https://developers.google.com\&sz=32)

  Google for Developers+1



I would initially choose llama.cpp because it provides a flexible route for experimenting with different open-weight models.

# 3. System architecture

The entire application can be divided into six major subsystems.

## Overall system architecture

Android application

Kotlin · Jetpack Compose · CameraX

Document intelligence

OCR · HTR · PDF extraction · Image processing

Medical memory

Patient profile · Records · Conversations

Retrieval engine

Vector search · Keyword search · Reranking

Medical knowledge

PubMed · Guidelines · Trusted websites

Local AI orchestration and LLM

Context assembly · Evidence evaluation · Answer generation

Grounded patient-facing response

Simple explanation · Medical citations · Uncertainty · Safety guidance

All personal-data processing and local inference take place on the device. External medical research is an optional internet-connected operation.

### How the system works

Imagine a patient uploads:

- A handwritten prescription from a doctor.
- A blood test report from a diagnostic laboratory.
- A discharge summary from a hospital.
- A previous consultation note.

The application processes these documents and creates a structured medical history.

Later, the user asks:

"Why did my doctor prescribe this medicine, and what should I know about it?"

The application:

1. Retrieves the relevant prescription.
2. Identifies the medicine and checks whether the OCR extraction was verified.
3. Retrieves relevant medical information from approved sources.
4. Searches the patient's existing records for relevant context.
5. Combines the retrieved evidence.
6. Generates a simple explanation.
7. Shows exactly which personal documents and external references support the explanation.

The application should never assume that a medicine's name or dosage is correct simply because the OCR model extracted it.

# 4. Detailed execution plan by phases

I recommend 10 development phases. Build each phase independently, test it, and then integrate it into the next one.

## Phase 0 — Project foundation and requirements



Foundation

&#x20;Estimated: 3–4 days



Objective: Establish the architecture and define the boundaries of the application.

Tasks:

1. Create the GitHub repository.
2. Define the application architecture.
3. Establish the supported Android version and minimum hardware requirements.
4. Define the patient-data schema.
5. Decide how documents, conversations and medical knowledge will be stored.
6. Define the security and privacy requirements.
7. Create an initial dataset of synthetic medical records and sample prescriptions for testing.

Suggested repository structure:

```
Personal-Medical-RAG/
│
├── android-app/
│   ├── presentation/
│   ├── camera/
│   ├── document/
│   ├── ocr/
│   ├── medical_records/
│   ├── conversation/
│   ├── retrieval/
│   ├── llm/
│   ├── security/
│   └── settings/
│
├── ai-engine/
│   ├── embeddings/
│   ├── reranking/
│   ├── handwriting/
│   └── evaluation/
│
├── medical-knowledge/
│   ├── source_registry/
│   ├── retrieval/
│   └── metadata/
│
├── tests/
├── documentation/
└── README.md
```

Deliverable: A working Android project with defined modules, database schema and privacy architecture.

## Phase 1 — Medical document ingestion



Document intelligence

&#x20;Estimated: 1–2 weeks



Objective: Allow users to upload, scan, classify and organize medical documents.

### Supported document formats

| Format        | Processing                        |
| ------------- | --------------------------------- |
| PDF           | Text extraction or page rendering |
| JPG/JPEG      | Image processing + OCR            |
| PNG           | Image processing + OCR            |
| Camera images | Capture + enhancement + OCR       |
| DOCX/TXT      | Text extraction                   |
| Scanned PDF   | Page-by-page OCR                  |

### Component 1: Document scanner

Use CameraX.

Functions:

- Capture documents.
- Detect document boundaries.
- Crop document edges.
- Correct perspective.
- Adjust brightness and contrast.
- Remove background noise.
- Generate a high-quality scanned image.
- Save the original and processed versions locally.

### Component 2: Document classification

Develop a lightweight classifier that identifies the document type.

Initial categories:

- Prescription.
- Laboratory report.
- Discharge summary.
- Consultation note.
- Radiology report.
- Medical history.
- Other.

Initially, use rule-based classification and keyword detection. Introduce an ML classifier later if necessary.

### Component 3: PDF extraction

Extract text directly from digitally generated PDFs.

If the PDF is image-based, render individual pages and pass them through OCR.

### Component 4: Document metadata extraction

For every document, capture:

- Document name.
- Document type.
- Date of document.
- Hospital or laboratory.
- Doctor's name, if available.
- Patient identifier, if available.
- Extraction status.
- Original file location.

The user should be able to edit these details.

Deliverable: A document manager that can ingest and organize a patient's medical documents without uploading them to a server.

## Phase 2 — OCR and handwritten prescription recognition



Most challenging component

&#x20;Estimated: 2–4 weeks



This deserves a separate development phase because handwritten prescriptions are considerably harder to interpret than printed medical documents.

### Component 1: Printed OCR

Use Google ML Kit Text Recognition initially.

Extract:

- Patient name.
- Test names.
- Laboratory values.
- Dates.
- Medicine names.
- Dosage information.
- Instructions.
- Doctor's notes.

### Component 2: Handwritten prescription recognition

Use a separate handwriting recognition pipeline.

Suggested experiments:

- PaddleOCR for detection and text recognition.
- TrOCR-based handwritten text recognition.
- Specialized handwritten text recognition models trained on appropriate datasets.

These are candidates to benchmark, not guaranteed prescription-reading solutions.

The pipeline:

Camera image of handwritten prescription

Image preprocessing

Deskew · Denoise · Binarization · Contrast enhancement

Text and handwriting region detection

Handwriting recognition model

Medical entity extraction

Medicine · Strength · Frequency · Duration · Route

Human verification

Patient reviews and confirms every uncertain medicine and dosage.

Verified medical document

### Component 3: Medical entity extraction

Once text is recognized, convert it into structured information.

Example:

```
{
  "document_type": "prescription",
  "medications": [
    {
      "name": "Medicine name",
      "strength": "500 mg",
      "frequency": "Twice daily",
      "duration": "5 days",
      "route": "Oral",
      "confidence": 0.87,
      "verification_status": "pending"
    }
  ]
}
```

The values above are illustrative, not an actual prescription.

### Component 4: Verification interface

Create a screen that displays:

- Original prescription image.
- Recognized text.
- Extracted medicine details.
- Confidence level.
- Edit and confirm buttons.

Critical rule: Low-confidence or ambiguous handwriting must be marked as unreadable or unverified. The model must never guess a medicine based on what it thinks the doctor probably intended.

For medication names, strength, dosage and frequency, require explicit user confirmation before treating the extraction as verified.

Deliverable: A document scanning and OCR system with a human-in-the-loop prescription verification process.

## Phase 3 — Personal medical memory and database



Persistent memory

&#x20;Estimated: 1–2 weeks



Objective: Build a persistent medical history that the application can retrieve across conversations.

This is one of the most important components.

Instead of storing all medical information as plain text, create a structured medical record system.

### Component 1: Patient profile

Store:

- Basic profile information.
- Known allergies.
- Existing medical conditions.
- Previous diagnoses recorded in documents.
- Medication history.
- Relevant surgeries.
- Relevant family medical history, if provided.
- Emergency contact information, if the user chooses.

Keep extracted information distinct from information explicitly confirmed by the patient.

### Component 2: Medical history database

Use Room and SQLite.

Suggested entities:

| Database table  | Information                      |
| --------------- | -------------------------------- |
| Patients        | Patient profile                  |
| Documents       | Original medical documents       |
| ExtractedText   | OCR output                       |
| Diagnoses       | Documented diagnoses             |
| Medications     | Medication records               |
| LabResults      | Laboratory measurements          |
| Allergies       | Confirmed and reported allergies |
| Encounters      | Consultation history             |
| Conversations   | Previous conversations           |
| Messages        | Individual messages              |
| MemorySummaries | Conversation summaries           |
| DocumentChunks  | Searchable document sections     |
| Embeddings      | Vector representations           |

### Component 3: Medical timeline

The application should construct a chronological medical timeline.

Example:

## Example patient medical timeline

1. January 2025

   Uploaded blood test report
2. March 2025

   Consultation note recorded
3. June 2025

   Prescription uploaded and verified
4. September 2025

   Follow-up laboratory report

### Component 4: Conversation memory

Use three layers:

A. Short-term memory

Recent messages in the current conversation.

B. Long-term conversation memory

Summaries of older conversations, important questions and previously discussed topics.

C. Medical memory

Structured information extracted from uploaded documents and confirmed by the user.

For example, if the user asks:

"What was the medicine I took during my previous treatment?"

The system searches the verified medication history and retrieves the relevant prescription.

It should distinguish between:

- Medicine prescribed.
- Medicine confirmed as taken.
- Medicine reported by the patient.
- Medicine that is no longer current.

Do not assume a historical prescription is a current medication.

Deliverable: A persistent, searchable personal medical history and conversation memory.

## Phase 4 — Local semantic search and RAG engine



Core RAG engine

&#x20;Estimated: 2 weeks



Objective: Enable natural-language searching across personal medical documents.

### Component 1: Document chunking

Break documents into smaller meaningful sections.

Example:

A laboratory report might contain:

- Patient details.
- Complete blood count.
- Lipid profile.
- Liver function.
- Kidney function.
- Reference ranges.

Keep these sections separate rather than combining the entire report into one embedding.

### Component 2: Embeddings

Use a small embedding model to convert each document chunk into a numerical vector.

Suggested initial candidates:

- multilingual-e5-small.
- BGE-small.

The embeddings should be generated locally.

### Component 3: Hybrid retrieval

Use both semantic and keyword search.

| Retrieval method | Example                                       |
| ---------------- | --------------------------------------------- |
| Semantic search  | "What did my kidney test indicate?"           |
| Keyword search   | "Creatinine"                                  |
| Date filtering   | "Show my last blood test"                     |
| Entity search    | "Find prescriptions containing this medicine" |

Combine the results using a ranking method such as Reciprocal Rank Fusion.

### Component 4: Context assembly

Before sending information to the LLM, retrieve:

1. Relevant personal medical documents.
2. Relevant verified medical facts.
3. Relevant previous conversations.
4. Relevant external medical references, when available.

Use metadata filters to prevent unrelated patient records from entering the context.

Deliverable: A completely local personal-document RAG engine.

## Phase 5 — External medical knowledge retrieval



Evidence retrieval

&#x20;Estimated: 2–3 weeks



Objective: Enable the application to search credible medical knowledge beyond the user's personal documents.

This is different from ordinary web search. You need a curated retrieval system.

### Component 1: Trusted-source registry

Start with a controlled list of approved sources.

[World Health Organization Logo & Brand Assets (SVG, PNG and vector) - Brandfetch](https://images.openai.com/static-rsc-4/IGQtCm_IwWeP_rlr3xrakRLvlKA59KFqfP171Zl_BtwAZkaq51jxgTFDyXbYAi5Ot9ZCncxrYc1gVAIohA7cvG7Lpc9R41TRI21zGGg0I-XRQTvD1yfUrXikki8JssVE-Dw_oSw77S6YQyfDTYQTqJvwg7DmjDEmZzmMOiI9-1E?purpose=inline)

World Health Organization (WHO)

Public health information, disease information and global health guidance.

[Official website](https://www.who.int/?utm_source=chatgpt.com)

[MedlinePlus | East Brunswick Public Library](https://images.openai.com/static-rsc-4/Y2oajhcFFhrEP_-6M-QAFIwzxd4bJhUH-_HvnJdFCKYd2gRy_Hq2T00oZfbFS_Mz7baUPG-TUAvbkTy-wJZX_srBmPRBl5TicQ7WA0FmTAranMCLzi3XjSr43kcSe2t_klQPOMTi8z7PTWyRaokd6QQFZw1oG70tbdFzNr3PP1c?purpose=inline)

MedlinePlus

Patient-friendly medical explanations, conditions and medicines.

[Official website](https://medlineplus.gov/?utm_source=chatgpt.com)

[NHS Saltaire | The Office Interiors Group](https://images.openai.com/static-rsc-4/ykB5Z7T5GXFJYaiwiSI8OzgZFz564bEcmk1_qhmzGXAsRxjsJYeipKBWB2NXocq_v1Bou7UX8-62EXI1XUzNABanDJQRoALHu39kNoSRjmCz4GEbH97lVCo9v-gyRaqsOJFTaNyvjrKyK7nKYG9HtPSzDBVf2AWtYZCDsB-lw8s?purpose=inline)

NHS

Accessible explanations of diseases, symptoms and treatments.

[Official website](https://www.nhs.uk/?utm_source=chatgpt.com)

[NIH T1D Spending Overview : 2020 Reports : Archives Reports  : Report Library  : Publications : Juvenile Diabetes Cure Alliance (JDCA)](https://images.openai.com/static-rsc-4/vKoYgmiC4GShAsJ9uQloF5nCDRKNSjrFckQsewKwsoK9AqtTq7MirLCm5RcSHAanqdAWIKBa0vlZZx0ET0yEHespWav1rKqkTLsfXp-gpR59JQcYuIoTRGq-NFk6KFqLNmJWjREF8WCIf_4QwvKckDVSzx_4RHVukxFfiIQCTwQ?purpose=inline)

NIH

Research, medical evidence and scientific resources.

[Official website](https://www.nih.gov/?utm_source=chatgpt.com)

[US NLM PubMed Logo Vector Download | Logowik](https://images.openai.com/static-rsc-4/KhBCkK_cLi10Uz21C3F7iJuXH9OzxWZOTANj6g4mhOE8Swb65oMto6dvkGJNzRbnV8lVBm0Y2iu3_-e1m80ZxUAEIgHLbMO8yjOSuzQJm4U-Iww5QrCBKj7M7x2TgE16ALH6i85bH5ry-llbWwdlof82O12Uxr1dEd7tkxHM3Mw?purpose=inline)

PubMed

Peer-reviewed biomedical literature and research article metadata.

[Search PubMed](https://pubmed.ncbi.nlm.nih.gov/?utm_source=chatgpt.com)

[Europe PMC (@EuropePMC_news) on X](https://images.openai.com/static-rsc-4/xFodAK2BnAwTSA4cdfgBgVGHbUWNGo1ImPXP0ANIJpF_fm0ZqyqqiphMEe4ENQj9Pg3EByaJeloGasGdsf3Mxxp90_DBC2WbxjV20sQ5-zPNxK-HkN28-s9eC3slpNT5nnVNPkW-pQduyyOEW3oh72hhae3iACoMl5GrFJp1Cxg?purpose=inline)

Europe PMC

Biomedical literature, abstracts and accessible full-text research.

[Search Europe PMC](https://europepmc.org/?utm_source=chatgpt.com)

[National Institute for Health and Care Excellence Logo PNG Vector (AI) Free Download](https://images.openai.com/static-rsc-4/frS-jYPlT7Zs589q6wwCksGv4Im2U39Fdlqsm2I_61HI54G7a7sb4C2mGtycI8dm5lTURTIvdjkT18dhQrkcA5mAhRFpuJ_PykkPkF1K5xTaABggwP6K6OMmWWTN9IXcnRxOrRA-MbXJPsSlphWw6Bc25OKMUa_radc0HkkytnQ?purpose=inline)

NICE

Clinical guidelines and evidence-based recommendations.

[Official website](https://www.nice.org.uk/)

[ICMR-NIV (@icmr_niv) on X](https://images.openai.com/static-rsc-4/ZtsP7I1-m-kTXexP-zaUxaJHtdqt_S9ipHov2oF8_eUbEQaUqsjg3y0u8DIuSxlWOKrWNl3hIETiNyEDQoY17foVsHctfwadpNC9U5GfDd0HyXnH6M7Mi-vghwnGtemNsjxQkISEXCS347IZbBMSR6ySEewcUIj5Fx_RdhUmT4I?purpose=inline)

ICMR

Indian medical research and national health guidance.

[Official website](https://www.icmr.gov.in/?utm_source=chatgpt.com)

### Component 2: Medical search connector

For the initial version, integrate:

- PubMed E-utilities API.
- Europe PMC REST API.
- Selected official medical websites.

PubMed provides programmatic access to biomedical literature, while Europe PMC provides APIs for publication metadata and accessible research content.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.ncbi.nlm.nih.gov\&sz=32)

NCBI+2



Avoid indiscriminate web scraping. Respect robots policies, API usage limits, copyright and article licensing. Store article metadata and short relevant excerpts where permitted, rather than copying entire copyrighted articles into your database.

### Component 3: Source ranking

Not all medical information has the same evidentiary value.

Use a ranking hierarchy:

| Priority | Evidence type                                |
| -------- | -------------------------------------------- |
| 1        | Current official clinical guidelines         |
| 2        | Systematic reviews and meta-analyses         |
| 3        | High-quality peer-reviewed research          |
| 4        | Government and national medical institutions |
| 5        | Established patient education resources      |

Also evaluate publication date, population studied, study design, applicability and conflicts between sources.

### Component 4: Search query privacy

This is particularly important.

Suppose the user's uploaded document contains their name, hospital, diagnosis and medication.

Do not transmit those details to external search engines.

Instead, convert the question into a generic medical query, such as:

"What is the commonly documented purpose and safety information of [verified medicine]?"

Remove personal identifiers and unrelated clinical details.

The external search should never receive the original medical document.

Deliverable: A medical knowledge retrieval system that can search external sources without uploading personal records.

## Phase 6 — Local LLM and grounded answer generation



AI reasoning layer

&#x20;Estimated: 2–3 weeks



Objective: Build the intelligence layer that converts retrieved medical information into simple, understandable answers.

### Component 1: Select a lightweight LLM

Do not begin with a large 7B–14B model. Your application needs to run on ordinary Android devices.

Start benchmarking these open-weight model families:

| Model candidate       | Intended experiment                       |
| --------------------- | ----------------------------------------- |
| Qwen2.5-1.5B-Instruct | Small local instruction-following model   |
| Qwen3-1.7B            | Newer compact reasoning/instruction model |
| Gemma 3 1B            | Lightweight instruction-following         |
| Llama 3.2 1B Instruct | Small conversational model                |

Use an appropriate GGUF quantization, initially Q4_K_M, and benchmark the models on actual Android hardware.

These are experimental candidates, not medically validated models.

My starting recommendation is Qwen2.5-1.5B-Instruct Q4_K_M because it gives you a manageable initial deployment target. Compare it against other candidates using the same medical question benchmark.

### Component 2: Model runtime

Use:

- llama.cpp Android integration.
- GGUF model files.
- CPU inference initially.
- Optional Android GPU acceleration after benchmarking.
- Context window initially around 2,048–4,096 tokens.

The LLM should not be responsible for searching the internet or accessing the database directly.

Instead, your application should retrieve the relevant evidence and pass it to the LLM.

This is a more controlled architecture.

### Component 3: Grounded generation pipeline

## Answer generation workflow

User question

"What does my latest blood report mean?"

Intent classification

Identify request type, medical entities and required context.

Retrieve personal context

Relevant blood report, previous reports, documented history and conversation context.

Retrieve medical evidence

Guidelines, patient education and research references.

Evidence validation

Check dates, source quality, contradictions and missing information.

Local LLM generation

Generate an explanation using only the supplied context. Abstain when evidence is insufficient.

Answer + citations + safety information

### Component 4: Prompt engineering

The model should receive a structured instruction.

For example:

```
SYSTEM ROLE:
You are a medical information assistant.

OBJECTIVE:
Explain medical information to patients and caregivers
using simple, accessible language.

RULES:
1. Use only the supplied evidence.
2. Never invent medical facts.
3. Distinguish documented facts from assumptions.
4. Never interpret uncertain OCR as confirmed data.
5. Explain medical terminology in simple language.
6. Cite every important medical claim.
7. Identify conflicting evidence.
8. State when evidence is insufficient.
9. Never prescribe or modify medication.
10. Encourage consultation with qualified clinicians
    for diagnosis and treatment decisions.

OUTPUT:
- Simple explanation
- What the documents actually say
- What medical references explain
- What remains uncertain
- Questions to ask the doctor
- Sources
```

### Component 5: Answer structure

Do not generate a long unstructured paragraph.

Use a consistent output format:

Example AI response

Illustrative

## Understanding your blood report

1\. What your report says

Your report contains a measurement of haemoglobin, which is a protein in red blood cells that helps carry oxygen.

2\. What this means

The value should be interpreted using the laboratory's reference range and your clinical context. A single measurement does not establish a diagnosis.

3\. What medical references explain

Relevant medical information is retrieved from the approved sources and linked below.

4\. What you should clarify with your doctor

- Does this result need to be repeated?
- Does it need to be considered alongside other blood parameters?

Evidence used

- Personal document: Blood report, uploaded 12 October.
- Medical source: Relevant official reference.

  A real answer must populate the actual report values and verified source links. This example intentionally contains no invented patient measurements.

### Component 6: Evidence validation

This is essential because a small LLM may produce incorrect answers even with RAG.

Implement:

- Citation-to-source matching.
- Evidence coverage checks.
- Missing-context detection.
- Contradiction detection.
- OCR verification checks.
- Explicit uncertainty statements.
- A fallback answer when retrieval is insufficient.

A citation must point to the actual source supporting the claim, not merely an article retrieved for a related topic.

Deliverable: A locally running medical RAG assistant capable of answering questions with traceable evidence.

## Phase 7 — Mobile application interface and user experience



Application experience

&#x20;Estimated: 1–2 weeks



The application should be simple enough for someone with limited medical knowledge or technical experience.

### Recommended screens

[Work](https://images.openai.com/static-rsc-4/CTJmN2U2BTs_jmMbmt7szqfgH75L5YMR0Co476wPb7YcNLBJxGiMBKTwcsiKzS4ngrCR3X1jfLsht7YAsMBpz8Y8aYdfUoMjr7zuZNRKKUjOprheHMie8QLFVigrh5EIaF4tc6k_QRejtHwLIkgNt0JG42V7c7PhRd2mJiyemyY?purpose=inline)

1\. Home dashboard

Patient profile, recent records, quick actions and medical timeline.

[nightingale v2: Explore E-Pharmacy/Medication Product UI by strangehelix on Dribbble](https://images.openai.com/static-rsc-4/qSpBasCNIIsSiKeime8Y_oYRcAFPLP1pAu3oGMm1il-Krbjf4nADW3GqifDGE97EnWGL1oXlANl0hQkOhcnoSyoeNZvR3vdf2Uea7m4ljYVEmm6PXLTB--5-F6kWtbJg1EZEiDSsB4ypOOFYAb-dQkWczMIQKU9g5ZbLwSSPgEI?purpose=inline)

2\. Document scanner

Scan, upload and classify medical documents.

[Your Electronic Health Records](https://images.openai.com/static-rsc-4/eli-sLgPXN_hE6K3B3_ZtP3Qxr4EFf6waABCU6tCkkZiLzIJgXPl61WFoDKyyFkdJXaNFF9RNX9E3ENM_kQBEtBA9BuBXe03TNOgPquGjOoZA0Eb9Ncx8sxGSMpe2Rhu8EiUqBLkyP4h3CwH4oSpjmJAS7egB6ljiwAMQqlbC38?purpose=inline)

3\. Medical history

Documents, medicines, laboratory results and chronological history.

[Doctorina Health | Your Empathetic AI Health Assistant](https://images.openai.com/static-rsc-4/dSsY3NdTjpIWunsnAXWiVB8eWpMct0-jgCsG-2bglsAWfMfCazUc6JYxrS_IH3QHjXvT37HeSRGq8HWr7yA6p9ku-l4dxJyTUby5SGuBm-mkExqv_dm5YHlI_QTP-L4V9HTd7jFeSMyO4VzSI3f-WSrxq6CGVu_PJSi_3KuVB-E?purpose=inline)

4\. AI assistant

Ask questions, see answers and inspect evidence.

### Suggested navigation

Use five bottom-navigation items:

1. Home
2. Records
3. Scan
4. Ask AI
5. Settings

### Additional features

- Voice-to-text questions.
- Text-to-speech answers.
- English and Tamil interface.
- Simple medical terminology explanations.
- Search previous conversations.
- Downloadable medical history summary.
- Export selected records as PDF.
- Doctor appointment preparation.
- Questions-to-ask-your-doctor generator.

The Tamil interface can be particularly useful for people who are more comfortable understanding medical information in Tamil than English. However, medical translation should preserve the original terminology and be reviewed for accuracy.

## Phase 8 — Privacy, encryption and offline operation



Security-critical

&#x20;Estimated: 1–2 weeks



Your privacy requirement should be an architectural decision, not simply a privacy statement.

### Component 1: Local-only data architecture

The following should remain on the user's device:

- Original medical documents.
- OCR output.
- Patient profile.
- Medical timeline.
- Conversation history.
- Medical memory.
- Embeddings.
- Local LLM inference.
- Generated answers.

Do not create a central patient database.

### Component 2: Encryption

Implement:

- AES-256-GCM encryption for sensitive files.
- Android Keystore for protecting encryption keys.
- Encrypted database storage.
- Secure temporary files.
- Automatic deletion of temporary OCR images.
- Optional biometric authentication.
- App-lock functionality.

Do not store encryption keys directly in the application source code or ordinary preferences.

### Component 3: Network restrictions

The application should have a controlled network boundary.

| Operation                           | Internet required? | Personal data transmitted?       |
| ----------------------------------- | ------------------ | -------------------------------- |
| Scan documents                      | No                 | No                               |
| OCR                                 | No                 | No                               |
| Search medical history              | No                 | No                               |
| Search old conversations            | No                 | No                               |
| Local LLM generation                | No                 | No                               |
| Search PubMed                       | Yes                | No, only sanitized generic query |
| Retrieve public medical information | Yes                | No personal documents            |
| Download model updates              | Yes                | No medical records               |

You can even introduce a privacy mode that disables all external network access.

### Component 4: Offline mode

The application should have two operating modes.

Offline mode

Fully local

- Upload and scan medical documents.
- Search personal records.
- Access previous conversations.
- Ask questions about previously stored information.
- Generate answers with locally available evidence.

Connected mode

Optional internet

- Search trusted medical sources.
- Retrieve new research publications.
- Check updated clinical guidelines.
- Refresh external knowledge indexes.
- Generate answers combining personal and external evidence.

One limitation must be made clear: an offline application cannot guarantee that its medical knowledge is current. Display the date of the latest external knowledge update.

### Component 5: Open-source privacy verification

Before release:

- Audit Android permissions.
- Disable analytics and advertising SDKs.
- Avoid third-party crash reporting that captures patient information.
- Inspect outbound network traffic.
- Check for accidental document uploads.
- Test deletion of all user data.
- Publish a clear data-flow diagram.

Deliverable: A privacy-audited application with no mandatory external storage, inference or patient-data transmission.

## Phase 9 — Medical safety and testing



Before public release

&#x20;Estimated: 2–3 weeks



This application operates in a high-stakes information domain. Its reliability is more important than its conversational ability.

### Component 1: Medical safety layer

The application must not:

- Diagnose diseases independently.
- Prescribe medicines.
- Change medication dosages.
- Recommend stopping prescribed treatment.
- Treat an OCR interpretation as a confirmed prescription.
- Present research findings as universally applicable.
- Give false reassurance about potentially serious symptoms.

It should instead:

- Explain existing medical documents.
- Describe medical terminology.
- Summarize previously recorded information.
- Explain what published evidence says.
- Identify missing information.
- Recommend professional consultation when appropriate.
- Direct users to emergency medical services when symptoms suggest an emergency.

### Component 2: Medical evidence evaluation

Create a test dataset containing:

- Printed prescriptions.
- Handwritten prescriptions.
- Laboratory reports.
- Discharge summaries.
- Medical history documents.
- Questions requiring multiple documents.
- Questions requiring external medical evidence.
- Questions for which the correct answer is that evidence is insufficient.

Use synthetic and appropriately licensed or consented data. Avoid putting real patient records into public test datasets.

### Component 3: Evaluation metrics

| Component           | Evaluation                        |
| ------------------- | --------------------------------- |
| Printed OCR         | Character and word error rate     |
| Handwriting OCR     | Character and word error rate     |
| Medicine extraction | Exact entity and field accuracy   |
| RAG retrieval       | Recall\@K, Precision\@K           |
| Answer generation   | Groundedness and factual accuracy |
| Citations           | Citation correctness              |
| Memory              | Context retrieval accuracy        |
| Mobile inference    | Latency and RAM usage             |
| Security            | Data leakage and encryption tests |

Pay particular attention to medication extraction accuracy. A good overall OCR score does not guarantee that the medicine name or dosage is correct.

### Component 4: Clinical validation

Before marketing it as a dependable medical information tool, have qualified healthcare professionals review:

- The answer-generation templates.
- The evidence ranking methodology.
- Medication extraction.
- High-risk answer handling.
- Translation quality.
- Medical safety messages.

Also assess applicable medical software regulations in the countries where you intend to distribute the application.

The FDA's software guidance specifically distinguishes general medical-information functions from functions that provide patient-specific diagnostic or treatment directives. Because your product targets patients and caregivers, its intended use and actual functionality need careful regulatory assessment.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.fda.gov\&sz=32)

FDA+2



Deliverable: An evaluated application with documented limitations, safety controls and a repeatable testing framework.

## Phase 10 — Open-source publication and deployment



Release

&#x20;Estimated: 1 week



Publish the project with:

- Android application source code.
- AI engine source code.
- Model integration documentation.
- Database architecture.
- Installation instructions.
- Model download instructions.
- Privacy policy.
- Security documentation.
- Medical safety limitations.
- Evaluation results.
- Sample synthetic medical records.

### Suggested open-source licensing

| Component                  | License consideration               |
| -------------------------- | ----------------------------------- |
| Your application source    | Apache 2.0                          |
| Your Python/Kotlin AI code | Apache 2.0 or MIT                   |
| Model weights              | Check individual model license      |
| Medical source content     | Follow source-specific licenses     |
| Test datasets              | Use appropriately licensed datasets |

Do not assume that a model is open-source merely because its weights are downloadable. Review its redistribution, commercial-use and attribution conditions.

# 5. How the application remembers previous conversations

This deserves special attention because ordinary RAG applications often lose conversational context when a new chat starts.

Your application should implement a dedicated memory management system.

### Example

Conversation 1:

User: "What does my latest blood report indicate?"

AI: Explains the report.

Conversation 2, several days later:

User: "Can you compare this with my previous report?"

The application should:

1. Retrieve the current report.
2. Retrieve the previous report from the database.
3. Identify corresponding laboratory measurements.
4. Compare values and dates.
5. Explain differences.
6. Retrieve relevant reference information.
7. Present a comparison with citations to both documents.

### Recommended memory architecture

| Memory type         | Storage              | Retrieval           |
| ------------------- | -------------------- | ------------------- |
| Recent messages     | SQLite               | Recent conversation |
| Older conversations | SQLite summaries     | Semantic search     |
| Medical facts       | Structured tables    | Entity-based lookup |
| Medical documents   | Local document store | Hybrid RAG          |
| User preferences    | Local profile        | Profile retrieval   |

One important rule: memory must be traceable.

For example, if the application remembers that a user has a documented allergy, it should be able to identify which source established that fact and whether it was confirmed by the patient.

Do not allow an AI-generated summary to silently turn an uncertain statement into a confirmed medical fact.

# 6. Lightweight mobile deployment strategy

Your requirement that the application run on relatively ordinary smartphones is important.

Do not load every AI model into memory simultaneously.

Use a modular loading strategy.

### Suggested memory budget

These are engineering targets for benchmarking, not guaranteed hardware requirements.

| Component              | Initial target           |
| ---------------------- | ------------------------ |
| Printed OCR            | 50–150 MB working memory |
| Image processing       | 30–100 MB                |
| Embedding model        | 50–150 MB                |
| Small LLM              | 1–2 GB working memory    |
| Database and retrieval | 50–150 MB                |
| Remaining application  | 100–200 MB               |

Actual usage depends on model architecture, quantization, context length, image resolution and Android device.

### Model-loading strategy

1. Load OCR only when scanning.
2. Unload OCR after document processing.
3. Load embedding model when indexing or searching.
4. Load the LLM only when the user asks a question.
5. Limit conversation context to relevant summaries and retrieved documents.
6. Unload the LLM when the user leaves the AI assistant.

### Recommended model configuration

| Model component         | Initial configuration         |
| ----------------------- | ----------------------------- |
| OCR                     | ML Kit                        |
| Handwriting recognition | Specialized compact HTR model |
| Embeddings              | E5-small / BGE-small          |
| LLM                     | 1B–1.7B parameter model       |
| Quantization            | 4-bit                         |
| Context window          | 2K–4K                         |
| Vector database         | SQLite-based local index      |
| Runtime                 | llama.cpp                     |

For older phones, introduce a fallback mode that uses document search, structured medical facts and prewritten explanations without loading an LLM.

That is preferable to making the application unusable on lower-memory devices.

# 7. End-to-end example: How the finished application functions

Consider a hypothetical patient who uploads three documents.

## Patient journey

1. Upload medical documents

   [Handwritten Medical Prescriptions Collection | Kaggle](https://images.openai.com/static-rsc-4/wi805QfJroVVgpawpCaDEk6NFVRkuEHhtoVywYQRtut45h1gFDvtmgs-5k7vNPjQoY0aqzTzl31wArAb5-uayeYxXBL5bcdUHedk41tET41XnXWhi_TLSTHRFlCTjDfOCbT0vct8hZ_GYh69nUCDKAnMW0GS2GHzhYusz028tEo?purpose=inline)

   [APTT-KCCT Blood Test In London - Order Online - Attend Clinic – Blood Tests London](https://images.openai.com/static-rsc-4/lwEPbdt1ZadcCoeI4URakYcPOmtWo5ZpaFxVGP3nOF-4zY565L_FAulXKD5kRg_9g_XG-mew8cVul4-Wp_QltYFVvdnmZLT6yIFCBrieesFYb2U-QhIE9yGn0LlHXtPgqDghZtKaaWJmv_XRzpKTE8-SOzwzrEWt8tbgVq5_GYs?purpose=inline)

   [30+ Sample Discharge Summary Templates (PDF) » American Templates](https://images.openai.com/static-rsc-4/CWaVCICgPLlHi5H0FKm7OeNSX4Aebp6TZUpWrfde1Oe1pju9QLZCpH7g_oyRKa05WH325-C56ZBRCQQcXgvFQK47XXMdMZwHNUrXdhc44y9ucptwPnxTGqBlGxkyZczDYhPQSV7yI8megI7M4Mtfk6U8Az5Tdbyce2LaDKWZlLg?purpose=inline)

   The application scans and stores the documents locally.
2. Extract information

   OCR extracts printed information. A handwriting model processes the prescription. The user verifies uncertain medicine names and dosages.
3. Build medical memory

   The system creates a medical timeline and indexes all documents for future retrieval.
4. Ask a question

   "Can you explain my medical history in simple language?"
5. Retrieve personal context

   The system searches relevant reports, prescriptions, discharge summaries and previous conversations.
6. Research external evidence

   It searches relevant medical references using a sanitized query without sharing the patient's documents.
7. Generate an explanation

   The local LLM combines the retrieved evidence, identifies uncertainties and generates an accessible response.
8. Save the conversation

   The conversation and a structured summary become part of the user's local medical memory.

The next time the user opens the application, the relevant history is still available.

# 8. Development timeline and prioritization

I would not attempt to build every feature simultaneously.

Here is a realistic solo-developer roadmap.

| Phase                         | Estimated duration |
| ----------------------------- | ------------------ |
| 0. Foundation                 | 3–4 days           |
| 1. Document ingestion         | 1–2 weeks          |
| 2. OCR and handwriting        | 2–4 weeks          |
| 3. Medical memory             | 1–2 weeks          |
| 4. Local RAG                  | 2 weeks            |
| 5. External medical research  | 2–3 weeks          |
| 6. LLM integration            | 2–3 weeks          |
| 7. Mobile UI                  | 1–2 weeks          |
| 8. Privacy and security       | 1–2 weeks          |
| 9. Medical safety and testing | 2–3 weeks          |
| 10. Open-source release       | 1 week             |

Estimated total: 14–20 weeks

Some phases can overlap, so this is a planning estimate rather than a fixed schedule.

### Three development milestones

Milestone 1

Personal Medical Document Assistant

Weeks 1–5

- Scan documents.
- Extract printed text.
- Recognize and verify handwritten prescriptions.
- Store medical records locally.
- Search personal history.

Milestone 2

Offline Medical RAG Assistant

Weeks 6–10

- Local embeddings.
- Hybrid retrieval.
- Local LLM.
- Persistent conversation memory.
- Grounded explanations.

Milestone 3

Evidence-grounded Medical AI Assistant

Weeks 11–18+

- PubMed and Europe PMC integration.
- Trusted-source ranking.
- Citations.
- Security and privacy audits.
- Evaluation and public release.

# 9. What I would build first: the actual MVP

To avoid scope creep, I would define the first publicly demonstrable version as follows.

| Feature                        | MVP priority                        |
| ------------------------------ | ----------------------------------- |
| Android application            | Essential                           |
| PDF and image uploads          | Essential                           |
| Printed OCR                    | Essential                           |
| Handwritten prescription OCR   | Essential, with verification        |
| Personal medical history       | Essential                           |
| Local SQLite database          | Essential                           |
| Local embeddings and retrieval | Essential                           |
| Small local LLM                | Essential                           |
| Persistent conversations       | Essential                           |
| PubMed integration             | Essential                           |
| External source citations      | Essential                           |
| AES encryption                 | Essential                           |
| Tamil language interface       | Next iteration                      |
| Voice interaction              | Next iteration                      |
| Drug interaction checker       | Later, after independent validation |
| Medical image interpretation   | Out of initial scope                |
| Automatic diagnosis            | Excluded                            |

I would also postpone complex agentic workflows. A deterministic retrieval pipeline with clearly defined stages is easier to test, debug and secure than an autonomous medical agent.

# 10. How to reuse your existing MedRAG project

You already have experience building a medical RAG system using Flask, Next.js, Ollama, ChromaDB and PyMuPDF. You can use that experience rather than starting from zero.

However, I would separate the existing prototype from the production mobile architecture.

| Existing project experience | New application                       |
| --------------------------- | ------------------------------------- |
| PyMuPDF                     | Android PDF extraction                |
| Flask RAG pipeline          | Native Kotlin retrieval orchestration |
| ChromaDB                    | SQLite-based local vector retrieval   |
| Ollama                      | llama.cpp Android runtime             |
| Local Llama model           | Quantized mobile LLM                  |
| Next.js UI                  | Jetpack Compose                       |
| Medical document ingestion  | CameraX + PDF ingestion               |
| RAG conversational history  | Structured persistent medical memory  |

Your existing MedRAG project can serve as the experimental environment for:

- Testing retrieval strategies.
- Evaluating prompt templates.
- Comparing embedding models.
- Developing medical entity extraction.
- Building evaluation datasets.
- Testing citation generation.

Then migrate the stable components into Android.

This avoids spending time building a backend that you eventually do not need.

# 11. Final recommended architecture and technology decisions

# My recommended final stack

No mandatory cloud infrastructure

Application

Kotlin + Jetpack Compose + CameraX

Document intelligence

ML Kit + PaddleOCR/HTR + OpenCV + PDF extraction

Local data and memory

Room + SQLite + Android Keystore + encrypted document storage

RAG engine

Local embeddings + SQLite FTS5 + vector similarity + reranking

LLM inference

llama.cpp + GGUF + compact quantized instruction model

External knowledge

PubMed E-utilities + Europe PMC + curated official medical sources

Security

AES-GCM + local-only processing + network restrictions

Deployment

Android APK + public GitHub repository + downloadable model files subject to their licenses

## 12. Five non-negotiable engineering principles

1\. Patient data must remain local. Never upload original documents, medical history, OCR output or conversation history to external AI providers.

2\. OCR must not become medical truth automatically. Especially for handwritten prescriptions, require verification of medication names and dosage.

3\. Every medical explanation must be traceable. Personal documents and external research must have separate citations.

4\. The LLM must be allowed to say "I don't know." An answer unsupported by reliable evidence is worse than no answer.

5\. The application must not become a substitute for a doctor. Its role is to help patients understand and organize information, not to make independent diagnosis or treatment decisions.

## 13. Suggested project identity

A few possible names:

- MedMemory AI
- MedSage
- MediVault
- MedCompanion
- Arogya RAG
- MyMedContext

My preferred direction would be MedMemory AI — Your Private Medical Knowledge Assistant.

The positioning is clear: a personal medical knowledge repository with an intelligent, evidence-grounded assistant, rather than a generic AI doctor.

## My suggested immediate execution order

Do not start by downloading five LLMs and experimenting with prompts. Start with the data and document pipeline.

Your first ten development tasks should be:

1. Create the Android Kotlin project.
2. Implement CameraX document scanning.
3. Implement PDF and image ingestion.
4. Integrate on-device printed OCR.
5. Develop the prescription handwriting recognition benchmark.
6. Implement prescription verification UI.
7. Create the Room database and encrypted document storage.
8. Build the medical history timeline.
9. Implement local embeddings and hybrid retrieval.
10. Integrate llama.cpp and produce the first grounded answer.

Only after these tasks work should you integrate PubMed and external medical research.

The most important MVP success criterion: A user should be able to upload a prescription and a laboratory report, close the application, reopen it without an internet connection, ask a question about those documents and receive an understandable answer grounded in the stored information.

That would demonstrate the central value of the entire product.

For your project, I would treat medical-document accuracy, privacy and retrieval reliability as the primary engineering achievements—not the size or sophistication of the LLM.
