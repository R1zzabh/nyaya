# NyayaAI — AI-Powered Judicial Decision Support System for India

## Project Overview

You are the lead AI architect and full-stack developer for **NyayaAI**, an AI-powered judicial decision support system designed specifically for the Indian legal ecosystem. This system is NOT a replacement for human judicial decision-making — it is a **decision support tool** that helps lawyers, advocates, and judges prepare stronger arguments, recall relevant precedents, and receive comprehensive multi-perspective legal analysis.

The system has four core features, each detailed below. When building any component, always keep in mind that this is a legal-domain product — accuracy, transparency, explainability, and fairness are non-negotiable.

---

## Target Users

- **Advocates / Lawyers**: Primary users who need case analysis, precedent retrieval, and legal reasoning support
- **Judges**: Secondary users who may use the system for comprehensive case review and precedent lookup
- **Law Students / Legal Researchers**: Tertiary users for academic and research purposes

---

## Tech Stack & Infrastructure

### Core Stack
- **Backend**: Python (FastAPI or Django REST Framework)
- **Frontend**: React.js or Next.js with a clean, professional legal UI (think Westlaw/SCC Online aesthetics, not flashy startup)
- **Database**: PostgreSQL for structured case metadata + pgvector or a dedicated vector DB (Pinecone/Weaviate/Qdrant) for embeddings
- **Task Queue**: Celery + Redis for async processing (multi-model calls, batch embeddings)
- **Deployment**: Docker containers, deployable on AWS/GCP/Azure

### AI/ML Stack
- **LLM APIs**: OpenAI GPT-4/4o, Google Gemini Pro, Anthropic Claude, Mistral, and/or Llama (via API or self-hosted)
- **Embeddings**: Legal-domain fine-tuned embedding model (start with OpenAI text-embedding-3-large or BGE-large, fine-tune later)
- **Vector Search**: FAISS (local) or Pinecone/Weaviate (managed) for similarity search
- **ML Models**: scikit-learn, XGBoost for classification tasks
- **Fine-tuning**: Hugging Face Transformers + PEFT/LoRA for lightweight LLM fine-tuning

---

## Data Foundation (CRITICAL — This Powers Everything)

### The Case Document Dataset
We have a dataset of previous Indian court case documents. This dataset is **unclean** and requires significant preprocessing before it can power any feature. This is the single most important engineering task — every feature depends on clean, structured data.

### Data Cleaning Pipeline

Build a robust, reproducible data cleaning pipeline that:

1. **Extracts structured metadata** from each raw case document:
   - `case_id`: Unique identifier
   - `case_title`: e.g., "State of Maharashtra v. XYZ"
   - `court`: Supreme Court / High Court (which one) / District Court / Tribunal
   - `bench`: Names of judges on the bench
   - `date_of_judgment`: Standardized date format
   - `case_type`: Criminal / Civil / Constitutional / Family / Labour / Tax / etc.
   - `petitioner`: Name(s) of petitioner(s)
   - `respondent`: Name(s) of respondent(s)
   - `statutes_cited`: List of all statutes/acts referenced (e.g., IPC Section 302, CrPC Section 164)
   - `sections_invoked`: Specific sections of law invoked
   - `charges`: What the accused was charged with (if criminal)
   - `verdict`: Convicted / Acquitted / Dismissed / Allowed / Remanded / Settlement
   - `sentence`: If convicted, what sentence was given
   - `key_arguments_petitioner`: Summarized arguments from petitioner side
   - `key_arguments_respondent`: Summarized arguments from respondent side
   - `court_reasoning`: Summarized reasoning of the court
   - `precedents_cited`: List of previous cases cited in this judgment
   - `headnotes`: Key legal principles established or applied
   - `full_text`: Clean full text of the judgment

2. **Use an LLM to assist with extraction** — many of these fields cannot be extracted with regex alone. Build a pipeline that:
   - Feeds each raw document to an LLM with a structured extraction prompt
   - Outputs JSON conforming to the schema above
   - Includes confidence scores for each extracted field
   - Flags low-confidence extractions for human review

3. **Validation layer**:
   - Cross-reference extracted statute citations against a known list of Indian statutes
   - Validate date formats and court names against known courts
   - Flag duplicate cases
   - Flag cases with missing critical fields

4. **Store the cleaned data** in PostgreSQL with proper indexing on all filterable fields.

### Legal Knowledge Base

In addition to the case dataset, the system needs access to:
- The **Constitution of India** (all articles, schedules, amendments up to the latest)
- **Indian Penal Code (IPC)** / **Bharatiya Nyaya Sanhita (BNS)** — the system should know both old and new codes since cases may reference either
- **Code of Criminal Procedure (CrPC)** / **Bharatiya Nagarik Suraksha Sanhita (BNSS)**
- **Code of Civil Procedure (CPC)**
- **Indian Evidence Act** / **Bharatiya Sakshya Adhiniyam (BSA)**
- **Other major statutes**: Companies Act, IT Act, POCSO, NDPS Act, etc.
- All of the above should be chunked, embedded, and stored in the vector database for RAG retrieval.

---

## Feature 1: Simulated Courtroom (Multi-Model Deliberation Engine)

### Concept
Multiple AI models analyze the same case document from different assigned perspectives, then evaluate each other's analyses through structured rubrics, simulating a multi-perspective courtroom deliberation.

### Architecture

#### Phase 1: Role Assignment & Initial Analysis
Each participating model is assigned a distinct **legal role**:

| Role | Model (configurable) | Responsibility |
|------|---------------------|----------------|
| **Prosecution Counsel** | Model A | Build the strongest case for the prosecution/petitioner |
| **Defense Counsel** | Model B | Build the strongest case for the defense/respondent |
| **Constitutional Expert** | Model C | Analyze constitutional implications, fundamental rights, validity of laws applied |
| **Legal Analyst** | Model D | Neutral analysis of legal merits, procedural correctness, evidentiary strength |
| **Presiding Judge (Synthesizer)** | Model E | Final synthesis — does NOT participate in scoring, only synthesizes after deliberation |

Each model receives:
- The full case document (uploaded by user)
- Its assigned role and detailed instructions for that role
- Relevant legal context retrieved via RAG (constitutional articles, relevant statutes, similar precedents)
- A structured output format to follow

#### Phase 2: Cross-Evaluation (Anonymous Scoring)
- Each model's output (from Phase 1) is anonymized — stripped of any identifiers
- Each model (except the Presiding Judge) evaluates the OTHER models' outputs on a **structured rubric**:

| Criteria | Weight | Description |
|----------|--------|-------------|
| **Legal Accuracy** | 25% | Are cited laws, sections, and precedents correct and applicable? |
| **Constitutional Alignment** | 20% | Does the analysis respect constitutional provisions and fundamental rights? |
| **Logical Coherence** | 20% | Is the argument logically structured with sound reasoning? |
| **Precedent Usage** | 15% | Are cited precedents relevant and correctly applied? |
| **Fairness & Balance** | 10% | Does the analysis acknowledge counterarguments? |
| **Practical Applicability** | 10% | Is the proposed outcome realistic and enforceable? |

Each criterion is scored 1-100 with mandatory justification for the score.

#### Phase 3: Rebuttal Round (Optional, User-Configurable)
- After scoring, each model (except the Judge) receives anonymized critiques from the evaluation round
- Each model can revise, strengthen, or rebut — producing an updated analysis
- A second round of scoring occurs on the updated analyses

#### Phase 4: Synthesis & Presentation
- The **Presiding Judge model** receives ALL analyses (original + revised), ALL scores, and ALL justifications
- It produces a **consolidated judgment recommendation** that:
  - Synthesizes the strongest arguments from all perspectives
  - Identifies points of consensus and disagreement
  - Provides a recommended outcome with confidence level
  - Lists key legal principles applied
  - Highlights any unresolved issues or areas needing human judgment

#### Output to User
Present to the user:
1. **Executive Summary**: The synthesized recommendation (2-3 paragraphs)
2. **Detailed Deliberation Report**:
   - Each role's analysis (with role labels, not model names)
   - Scoring breakdown with justifications
   - Rebuttal round changes (if applicable)
3. **Transparency Dashboard**:
   - Scoring heatmap (which roles scored which roles how)
   - Consensus meter (how much agreement across models)
   - Confidence score for the final recommendation
4. **Caveats**: Always include a disclaimer that this is a decision support tool, not a legal opinion

#### Implementation Notes
- All model calls should be **async and parallel** where possible to reduce latency
- Implement **caching** — if the same case document is analyzed again, offer to show previous results
- Allow users to choose between:
  - **Quick Analysis**: 2-3 models, single round, ~2-3 minutes
  - **Full Deliberation**: All 5 models, with rebuttal round, ~8-12 minutes
- Track and display **estimated API costs** per analysis (transparency for the business)
- Implement **retry logic and fallbacks** — if one model API fails, gracefully degrade (e.g., 4 models instead of 5)

---

## Feature 2: Legal Research Assistant (RAG-Powered, Fine-Tuned for Indian Law)

### Concept
A conversational AI assistant that has deep knowledge of Indian law and can reason about the current case document in context. It acts as a knowledgeable legal research partner.

### Architecture

#### Hybrid Approach: RAG + Fine-Tuned Reasoning

**RAG Layer (for factual recall — updatable without retraining):**
- Constitution of India, IPC/BNS, CrPC/BNSS, CPC, Evidence Act/BSA, and other major statutes — all chunked and embedded
- The cleaned case document dataset — embedded for semantic retrieval
- The current user-uploaded case document — dynamically chunked and embedded at upload time

**Fine-Tuned Layer (for legal reasoning style):**
- Fine-tune a lightweight model (Mistral 7B, Llama 3 8B, or Phi-3) using LoRA/QLoRA on:
  - Indian court judgment reasoning patterns (how judges structure their analysis)
  - Legal argumentation styles specific to Indian courts
  - Correct usage of Indian legal terminology and citation formats
- The fine-tuning teaches the model HOW to reason about Indian law, not WHAT the law says (that comes from RAG)

#### Capabilities
The assistant should be able to:
1. **Answer questions about the current case**: "What sections of IPC are applicable here?", "What are the weaknesses in the prosecution's argument?"
2. **Recall and explain relevant law**: "Explain Section 302 IPC and how it differs from Section 304", "What does Article 21 guarantee?"
3. **Identify gaps**: "The case document doesn't mention whether the confession was made before a magistrate — this is relevant under Section 164 CrPC"
4. **Suggest strategy**: "Based on the facts, a defense argument under the right to self-defense (Section 96-106 IPC) may be applicable"
5. **Cross-reference**: "This case cites Bachan Singh v. State of Punjab — here's what that precedent established and how it applies here"

#### Conversation Design
- Maintain conversation history within a session
- Allow the user to upload a case document which becomes the "active case context"
- Every response should include citations — which statute, which section, which precedent
- If the assistant is unsure, it should say so explicitly rather than hallucinate
- Include a "confidence indicator" on responses (based on RAG retrieval score + model confidence)

#### Technical Implementation
- Use a **retrieval-augmented generation** pipeline:
  1. User query → Embed query → Retrieve top-k relevant chunks from vector DB
  2. Construct prompt with: system instructions + retrieved context + conversation history + user query
  3. Generate response from fine-tuned model (or API model with strong system prompt if fine-tuning isn't ready)
- Implement **source attribution** — every claim should be traceable to a specific statute section or case
- Build a **feedback loop** — users can flag incorrect responses, which feeds into a correction dataset for future fine-tuning

---

## Feature 3: Similar Case Retrieval (Semantic Search Engine)

### Concept
A user uploads a case document, and the system retrieves the top-N most similar historical cases from the dataset, enabling advocates and judges to reference relevant precedents.

### Architecture

#### Embedding-Based Semantic Search (NOT naive KNN on raw text)

1. **Document Embedding Pipeline**:
   - Each cleaned case document is embedded using a legal-domain embedding model
   - Embeddings capture semantic meaning, not just keyword overlap
   - Store embeddings in a vector database with associated metadata

2. **Multi-Level Similarity**:
   - **Factual Similarity**: Similarity based on the facts of the case (what happened)
   - **Legal Similarity**: Similarity based on the laws/sections applied
   - **Outcome Similarity**: Cases with similar verdicts/outcomes
   - Allow users to weight these dimensions (e.g., "I care more about factual similarity")

3. **Metadata Filtering** (applied BEFORE or alongside vector search):
   - Court level: Supreme Court, specific High Court, District Court
   - Case type: Criminal, Civil, Constitutional, etc.
   - Relevant statutes/sections
   - Date range
   - Verdict type
   - Jurisdiction

4. **Retrieval Process**:
   ```
   User uploads case doc
   → Extract text and metadata
   → Generate embedding
   → Query vector DB with embedding + metadata filters
   → Retrieve top-N candidates
   → Re-rank using a cross-encoder for precision
   → Return ranked results with similarity scores and explanations
   ```

5. **Result Presentation**:
   For each retrieved case, show:
   - Case title, court, date, verdict
   - **Similarity score** (overall + per dimension)
   - **Why it's similar**: Highlighted overlapping legal sections, similar fact patterns, analogous charges
   - **Key takeaway**: What the precedent established and how it might apply
   - Link to full case document

#### Technical Implementation
- **Embedding Model**: Start with a general-purpose model (OpenAI text-embedding-3-large, BGE-large-en-v1.5), then fine-tune on Indian legal text pairs for better domain performance
- **Vector Database**: FAISS for local/prototype, Pinecone or Weaviate for production
- **Re-ranking**: Use a cross-encoder (e.g., ms-marco-MiniLM) fine-tuned on legal document pairs for re-ranking top candidates
- **Chunking Strategy**: For long judgments, use hierarchical embeddings — embed the full document AND key sections (facts, arguments, reasoning, verdict) separately
- **Index the cleaned dataset fields**: Enable hybrid search (vector similarity + metadata filters) for maximum precision

---

## Feature 4: Case Outcome Tendency Analyzer (Statistical Insights)

### Concept
Given a case document, the system provides **statistical insights** about how similar cases have historically been resolved. This is NOT a "verdict predictor" — it is a statistical analysis tool that shows historical patterns and tendencies.

### CRITICAL FRAMING
- **NEVER** present this as "predicting" a verdict
- **ALWAYS** frame as "historical pattern analysis" or "outcome tendency based on similar cases"
- **ALWAYS** include confidence intervals and sample sizes
- **ALWAYS** include a disclaimer about the limitations of statistical analysis in legal contexts

### Architecture

#### Feature Engineering
From the cleaned dataset, extract features for each case:
- **Statutory features**: Which sections of law were invoked (one-hot or multi-hot encoded)
- **Case type**: Criminal / Civil / Constitutional / etc.
- **Court level**: SC / HC / District
- **Charge severity**: Based on the maximum punishment prescribed for the charges
- **Number of charges**: Single vs. multiple charges
- **Number of accused**: Individual vs. multiple
- **Type of evidence mentioned**: Documentary, forensic, eyewitness, confession, circumstantial
- **Legal representation**: Whether specific types of counsel are mentioned
- **Procedural features**: Whether specific procedural steps were followed (bail history, number of hearings, etc.)
- **Precedent features**: Which landmark cases were cited
- **Text-based features**: TF-IDF or embedding-based features from case summary text

#### Model Architecture
- **Primary Model**: XGBoost or LightGBM classifier (interpretable, handles mixed feature types well)
- **Benchmark Models**: Logistic Regression, Random Forest, Passive Aggressive Classifier — compare performance
- **Output**: Multi-class classification (Convicted / Acquitted / Dismissed / Partially Allowed / Remanded / Settled)
- **Calibrate probabilities** using Platt scaling or isotonic regression for meaningful confidence scores

#### Output to User
Present as a **statistical insights dashboard**:
1. **Outcome Distribution**: "In 1,247 similar cases (matching case type, court level, and primary statute): 68% resulted in conviction, 24% in acquittal, 8% other"
2. **Sentence Distribution** (if applicable): "Average sentence: 7.2 years. Range: 3-14 years. Median: 6 years"
3. **Key Factors**: "The most influential factors in similar cases were: [SHAP values / feature importance] — type of evidence (32%), specific sections invoked (24%), court level (18%)"
4. **Trend Analysis**: "Conviction rates for this type of case have [increased/decreased] from X% to Y% over the past 10 years"
5. **Confidence & Caveats**:
   - Sample size used for analysis
   - Model confidence interval
   - Clear disclaimer: "This is a statistical summary of historical outcomes. Every case has unique facts and circumstances. This analysis should not be used as a basis for legal decisions."

---

## Cross-Cutting Concerns

### Security & Privacy
- Case documents may contain sensitive personal information — implement encryption at rest and in transit
- User uploads should be processed and stored securely, with options for auto-deletion after analysis
- Implement role-based access control (RBAC) — different features for different user tiers
- Audit logging for all analyses performed

### API Design
- RESTful API with clear versioning (e.g., `/api/v1/`)
- Endpoints for each feature:
  - `POST /api/v1/courtroom/analyze` — Simulated courtroom
  - `POST /api/v1/assistant/query` — Legal assistant
  - `POST /api/v1/cases/similar` — Similar case retrieval
  - `POST /api/v1/cases/tendency` — Outcome tendency
  - `POST /api/v1/documents/upload` — Document upload and processing
- WebSocket support for streaming long responses (especially Feature 1)
- Rate limiting and usage tracking

### Document Processing
- Support common Indian legal document formats: PDF (most common), DOCX, scanned documents (OCR needed)
- Build a **document ingestion pipeline**:
  1. Upload → Format detection → Text extraction (PyPDF2/pdfplumber for PDFs, Tesseract for scanned docs)
  2. Text cleaning → Section identification → Metadata extraction
  3. Embedding generation → Storage

### Monitoring & Observability
- Track model performance metrics (latency, accuracy of retrievals, user satisfaction)
- Log all LLM calls with prompts and responses for debugging
- Implement A/B testing infrastructure for model improvements
- Dashboard for system health and usage analytics

### Ethical Considerations
- The system must NEVER claim to replace judicial discretion
- All outputs must include appropriate disclaimers
- Implement bias detection — regularly audit outputs for patterns of bias (caste, religion, gender, economic status)
- Provide an easy feedback mechanism for users to report inaccurate or biased outputs
- Consider making bias audit reports publicly available for transparency

---

## Development Phases

### Phase 1: Data Foundation (Weeks 1-4)
- Build and run the data cleaning pipeline
- Set up PostgreSQL + vector database
- Chunk and embed the legal knowledge base (Constitution, IPC/BNS, etc.)
- Achieve a clean, structured dataset of at least 70% of total cases

### Phase 2: Similar Case Retrieval — Feature 3 (Weeks 5-8)
- Implement embedding pipeline for case documents
- Build vector search with metadata filtering
- Implement cross-encoder re-ranking
- Build a simple UI for upload and retrieval
- **Ship this first** — it's the most immediately useful and least controversial feature

### Phase 3: Legal Research Assistant — Feature 2 (Weeks 9-14)
- Build the RAG pipeline with legal knowledge base
- Implement the conversational assistant with source attribution
- Fine-tune a lightweight model on Indian legal reasoning (or start with strong prompting on API models)
- Build the conversational UI

### Phase 4: Simulated Courtroom — Feature 1 (Weeks 15-22)
- Implement multi-model orchestration layer
- Build role assignment and prompt engineering for each legal role
- Implement cross-evaluation and scoring pipeline
- Build rebuttal round logic
- Implement the synthesis phase
- Build the transparency dashboard UI

### Phase 5: Outcome Tendency Analyzer — Feature 4 (Weeks 23-28)
- Feature engineering from cleaned dataset
- Train and benchmark multiple classifiers
- Calibrate probabilities
- Build the statistical insights dashboard
- Extensive bias auditing

### Phase 6: Integration, Polish & Launch (Weeks 29-34)
- Integrate all features into a unified platform
- End-to-end testing with real legal professionals
- Performance optimization
- Security audit
- Beta launch with select law firms / legal aid organizations

---

## Coding Guidelines

When writing code for this project:
1. **Type hints everywhere** — this is a legal product, type safety matters
2. **Comprehensive error handling** — graceful degradation, never crash on bad input
3. **Logging** — structured logging (JSON format) for every significant operation
4. **Testing** — unit tests for all utility functions, integration tests for pipelines, at minimum 80% coverage for critical paths
5. **Documentation** — docstrings on all functions, README for each module, API documentation via OpenAPI/Swagger
6. **Config management** — use environment variables and config files, never hardcode API keys or model names
7. **Modular architecture** — each feature should be an independent module that can be developed, tested, and deployed separately
8. **Indian legal domain awareness** — use correct legal terminology (e.g., "petitioner" not "plaintiff" for writ petitions, "FIR" not "police report")

---

## Key Reminders

- **Data cleaning is the foundation** — don't rush past it. Every downstream feature's quality depends on it.
- **Start with Feature 3** (Similar Case Retrieval) — it's the quickest win and validates your data pipeline.
- **The Simulated Courtroom is the flagship feature** — invest the most prompt engineering effort here. The quality of role-specific prompts determines everything.
- **Always prioritize accuracy over speed** — in legal contexts, a wrong answer is worse than a slow answer.
- **Transparency is non-negotiable** — users should always be able to understand WHY the system produced a particular output.
- **This is a decision SUPPORT tool** — never position it as a decision-making tool.
