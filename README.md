# ProofStack (PerfProof) — Autonomous Verification & Forensic Audit Engine

> **Zero-Compromise Performance & Security Verification Gauntlet with Cryptographic Evidence Ledger**  
> *Engineered for High-Assurance AI Patch Evaluation, DAST Remediation, and Deterministic Acceptance.*

[![Python 3.11+](https://img.shields.io/badge/python-3.11+-blue.svg)](https://www.python.org/downloads/)
[![FastAPI](https://img.shields.io/badge/backend-FastAPI_0.115+-009688.svg)](https://fastapi.tiangolo.com)
[![Next.js 14](https://img.shields.io/badge/frontend-Next.js_14-black.svg)](https://nextjs.org/)
[![Tests](https://img.shields.io/badge/pytest-76%20passed%2C%201%20skipped-success.svg)](file:///tests)
[![Ledger](https://img.shields.io/badge/ledger-SHA--256%20Tamper--Evident-green.svg)](file:///proofstack/ledger)
[![AI Integration](https://img.shields.io/badge/model-NVIDIA%20Nemotron%20%2F%20Nebius-76B900.svg)](file:///backend/model_service.py)

---

## 1. What is ProofStack in Simple Terms?

Modern AI coding models (like Cursor, Claude, or Copilot) are fantastic at proposing optimizations that make code **10x to 40,000x faster**. However, they also frequently introduce **silent, catastrophic security flaws**—like removing tenant checks, introducing IDOR vulnerabilities, or bypassing authentication to achieve speed.

Human engineers cannot manually audit thousands of lines of AI diffs under deadline pressure. 

**ProofStack solves this by enforcing one non-negotiable rule:**

$$\Large\textbf{The AI Proposes; Deterministic Code Decides.}$$

1. **AI is an Assistant, Not the Judge**: An LLM (NVIDIA Nemotron via Nebius Token Factory, or a local deterministic rule engine) analyzes profiling data and proposes patches.
2. **The Zero-Compromise Interlock**: The proposed patch is placed in an isolated sandbox and slammed with benchmark runs and dynamic security exploits (DAST). Even if a patch is **42,000x faster**, if it fails a single mandatory security gate, **the patch is instantly REJECTED**.
3. **Cryptographic Proof Ledger**: Every measurement, gate result, and verdict is committed to an append-only, SHA-256 cryptographic hash-chain. Nobody—not even an admin—can alter the benchmark or security results without breaking the chain.

---

## 2. End-to-End System Architecture

```mermaid
flowchart TD
    subgraph Intake ["1. Task Intake & Profiling"]
        A[Developer / CI Submits Code] --> B[Intake Validation & Schema Check]
        B --> C[Reproducible Baseline Benchmark & Profiling]
    end

    subgraph Security ["2. Automated DAST Audit"]
        C --> D[PSEC Dynamic Security Probes]
        D -->|Detects Vulnerabilities e.g. IDOR, Leaks| E[Verified Findings List]
    end

    subgraph AI ["3. Evidence-Grounded AI Engineering"]
        E --> F[Prompt Fencing & Untrusted Input Sanitization]
        F --> G{Provider Configured?}
        G -->|Yes: NEBIUS_API_KEY| H[NVIDIA Nemotron-4-340B via Nebius Token Factory]
        G -->|No / Provider Outage| I[Deterministic Local Rule Engine Fallback]
        H --> J[Strict JSON Schema Validation]
        I --> J
        J --> K[Safe Memory-Only Patch Synthesis]
    end

    subgraph Gauntlet ["4. Verification Gauntlet (Gates G0 - G7)"]
        K --> L[Isolated Sandbox Execution]
        L --> M[G0-G2: Syntax, Secrets & Supply Chain]
        L --> N[G3: Functional Correctness Tests]
        L --> O[G4: Security Exploit Oracles]
        L --> P[G5-G6: Concurrency & Scaling Invariants]
        L --> Q[G7: Hardware Benchmark & Noise Floor]
    end

    subgraph Interlock ["5. Zero-Compromise Security Interlock"]
        M & N & O & P & Q --> R{Did ALL Mandatory Security Gates Pass?}
        R -->|NO: Security Breached| S[VERDICT: STRICT REJECTION ❌]
        R -->|YES: Security Preserved & Speedup Confirmed| T[VERDICT: ACCEPTED ✅]
    end

    subgraph Ledger ["6. Cryptographic Evidence Ledger"]
        S & T --> U[Append State to SHA-256 Hash Chain]
        U --> V[Tamper-Evident Forensic Audit Report]
        U --> W[Real-Time Next.js Cockpit Dashboard]
    end

    style S fill:#ff4d4f,stroke:#d9363e,stroke-width:2px,color:#fff
    style T fill:#52c41a,stroke:#389e0d,stroke-width:2px,color:#fff
    style R fill:#faad14,stroke:#d48806,stroke-width:2px,color:#000
    style H fill:#76b900,stroke:#5c8f00,stroke-width:2px,color:#fff
    style I fill:#1890ff,stroke:#096dd9,stroke-width:2px,color:#fff
```

---

## 3. The AI / Model Subsystem

ProofStack connects directly to the **Nebius Token Factory** using **NVIDIA Nemotron** models (`nvidia/nemotron-4-340b-instruct`), with an automatic, honest **Deterministic Local Fallback** when credentials are absent.

```mermaid
sequenceDiagram
    autonumber
    actor User as Engineer / UI
    participant Server as FastAPI Server
    participant ModelService as Model Service
    participant Nebius as Nebius Token Factory (NVIDIA Nemotron)
    participant Fallback as Local Deterministic Rule Engine
    participant Ledger as SHA-256 Evidence Ledger

    User->>Server: POST /api/runs/{run_id}/analyze
    Server->>ModelService: Request structured hypothesis analysis
    ModelService->>ModelService: Delimit untrusted task code (fence)
    
    alt NEBIUS_API_KEY configured & active
        ModelService->>Nebius: Live Inference (Prompt with PSEC evidence)
        Nebius-->>ModelService: Raw JSON completions
        ModelService->>ModelService: Validate against hypothesis-set.v1.json
        Note over ModelService: Provenance: LIVE AI INFERENCE
    else Key absent or provider error (5xx / timeout)
        ModelService->>Fallback: Grounded heuristic synthesis from run findings
        Fallback-->>ModelService: Structured hypothesis payload
        Note over ModelService: Provenance: LOCAL RULE ENGINE (FALLBACK)
    end

    ModelService->>Ledger: Commit MODEL_ANALYSIS event with SHA-256 hash
    ModelService-->>Server: ModelAnalyzeResponse (provenance, hypotheses, latency)
    Server-->>User: Return response to Cockpit with visible provenance badge
```

### Safety Guarantees for AI Patches:
- **Prompt Fencing**: User task descriptions and source files are wrapped with strict boundaries (`proofstack.llm.render.fence`) to prevent prompt injection from overriding verification rules.
- **Strict Schema Enforcement**: Outputs must strictly conform to `schemas/hypothesis-set.v1.json`. Malformed responses trigger an error rather than silent corruption.
- **Path Traversal Defense**: Patches attempting directory traversal (`../`), absolute paths, or touching protected namespaces (`.git/`, `proofstack.db`, security configs) are blocked before sandboxing.
- **Honest Provenance**: The Cockpit displays whether an analysis originated from **Live AI Inference (NVIDIA Nemotron)** or **Deterministic Rule Engine (Local Fallback)**. No AI hallucinations are ever masqueraded as live tokens.

---

## 4. The Zero-Compromise Security Interlock

Why is ProofStack trustworthy? Because **speedups cannot bribe the security gate**.

```mermaid
flowchart LR
    subgraph Inputs ["Evaluations"]
        Perf["Empirical Speedup (G7)<br/>e.g. +42,000x Faster!"]
        Sec["Security Oracles (G4)<br/>e.g. IDOR Probe Detected"]
        Corr["Correctness Tests (G3)<br/>e.g. Regression Check"]
    end

    subgraph DecisionMatrix ["The Interlock Logic"]
        Interlock{"Zero-Compromise Interlock<br/>(All mandatory gates must be GREEN)"}
    end

    subgraph Outcome ["Final Verdict"]
        Reject["REJECTED ❌<br/>(Security Bolt Retracted)"]
        Accept["ACCEPTED ✅<br/>(Patch Applied to Main)"]
    end

    Perf -->|Green: Ultra Fast| Interlock
    Sec -->|Red: Tenant Leak!| Interlock
    Corr -->|Green: Logic Passes| Interlock

    Interlock -->|Any Red Gate| Reject
    Interlock -->|All Green Gates| Accept

    style Perf fill:#52c41a,color:#fff
    style Sec fill:#ff4d4f,color:#fff
    style Corr fill:#52c41a,color:#fff
    style Reject fill:#ff4d4f,stroke:#b71c1c,stroke-width:3px,color:#fff
    style Accept fill:#52c41a,stroke:#1b5e20,stroke-width:3px,color:#fff
```

### The 8 Verification Gates:
| Gate | Category | Description | Failure Behavior |
|---|---|---|---|
| **G0** | Integrity | Diff line budget & syntax validity | Hard Rejection |
| **G1** | Supply Chain | Secret leakage & dependency tampering | Hard Rejection |
| **G2** | Static Analysis | Pre-gate security scans & linters | Hard Rejection |
| **G3** | Correctness | Test suite & functional regression checks | Hard Rejection |
| **G4** | Security | Dynamic exploit oracles & DAST probes (IDOR, Auth) | **Veto Overrides All Speedups** |
| **G5** | Concurrency | Race conditions & thread safety | Hard Rejection |
| **G6** | Complexity | Algorithmic scaling & Big-O invariants | Hard Rejection |
| **G7** | Benchmark | Empirical latency & noise-floor evaluation | Inconclusive if noisy; Reject if regressed |

---

## 5. Cryptographic Evidence Ledger

Every state transition in ProofStack is committed to a **tamper-evident SHA-256 append-only ledger**.

```mermaid
flowchart LR
    subgraph Block0 ["Ledger Entry 0 (Genesis)"]
        H0["Hash: 000000000000...<br/>Event: RUN_CREATED"]
    end

    subgraph Block1 ["Ledger Entry 1"]
        H1["Prev: Hash(Block 0)<br/>Event: BASELINE_MEASURED<br/>Payload: Latency: 24.2ms, Noise: 1.2%"]
    end

    subgraph Block2 ["Ledger Entry 2"]
        H2["Prev: Hash(Block 1)<br/>Event: MODEL_HYPOTHESIS<br/>Payload: Diff SHA: a8f9..., Model: nemotron"]
    end

    subgraph Block3 ["Ledger Entry 3"]
        H3["Prev: Hash(Block 2)<br/>Event: GATE_EVALUATION<br/>Payload: G4: FAIL (IDOR), G7: PASS"]
    end

    subgraph Block4 ["Ledger Entry 4 (Final Verdict)"]
        H4["Prev: Hash(Block 3)<br/>Event: DECISION_RECORDED<br/>Verdict: REJECTED"]
    end

    H0 --> H1 --> H2 --> H3 --> H4

    style Block0 fill:#2d3748,color:#fff
    style Block1 fill:#2d3748,color:#fff
    style Block2 fill:#2d3748,color:#fff
    style Block3 fill:#2d3748,color:#fff
    style Block4 fill:#ff4d4f,color:#fff
```

$$\text{Entry Hash}_n = \text{SHA256}(\text{bytes.fromhex}(\text{Prev Hash}_{n-1}) + \text{canonical\_json}(\text{Payload}_n))$$

If an adversary modifies even 1 millisecond of benchmark data or toggles a failed security gate to `PASS` in the database, the cryptographic hash link breaks, and the Cockpit will flag **Ledger Tampered!**

---

## 6. Quick Start Guide (Get Running in 3 Minutes)

### Prerequisites
- **Python 3.11 or 3.12**
- **Node.js 18+ & npm**
- **Git**

### Step 1: Clone & Python Virtual Environment
```bash
# Clone the repository
git clone https://github.com/nikhillakra2007-tech/something.git
cd something

# Create and activate virtual environment
python -m venv .venv

# On Linux/macOS:
source .venv/bin/activate
# On Windows (PowerShell):
.venv\Scripts\Activate.ps1

# Install backend dependencies
pip install -r requirements.txt
pip install -e .
```

### Step 2: Configure Environment
Copy the example environment file:
```bash
cp .env.example .env
```
*(Optional)* To enable live AI generation via NVIDIA Nemotron, add your Nebius API Key:
```ini
NEBIUS_API_KEY=nbf-your-api-key-here
NEBIUS_MODEL_ID=nvidia/nemotron-4-340b-instruct
```
> **Note**: If `NEBIUS_API_KEY` is not provided, ProofStack automatically runs in **Deterministic Rule Engine Fallback Mode** with 100% functionality and zero errors!

### Step 3: Run Database Migrations
Initialize the SQLite database with Alembic:
```bash
python -m alembic upgrade head
```

### Step 4: Start Backend Server (Port 8765)
```bash
python -m backend.server
```
*The FastAPI backend binds to `http://localhost:8765`. API documentation is live at `http://localhost:8765/docs`.*

### Step 5: Start the Web Cockpit Frontend (Port 3000)
In a new terminal window:
```bash
cd frontend
npm install
npm run dev
```
Open **`http://localhost:3000`** in your browser.

---

## 7. Interactive Demonstration: The 42,000x Speedup Trap

Experience the platform's core thesis in under 60 seconds:

```mermaid
sequenceDiagram
    autonumber
    actor Judge as Technical Judge / User
    participant UI as Web Cockpit (Port 3000)
    participant Backend as ProofStack Engine (Port 8765)

    Judge->>UI: Click "⚡ Seed Demo Fixture" in navbar
    UI->>Backend: POST /api/demo/seed
    Backend-->>UI: Fixture Loaded (Invoice Caching Optimization)
    Judge->>UI: Open Run Case File (/runs/run-idor-showcase-01)
    
    Note over UI: Viewport 1: Performance is 42,000x FASTER (Green)<br/>Viewport 2: Security Gate G4 breached (Red IDOR)<br/>Viewport 3: Zero-Compromise Interlock Bolt = RETRACTED<br/>Viewport 4: Final Verdict = STRICT REJECTION
    
    Judge->>UI: Click "Verify Hash-Chain Integrity"
    UI->>Backend: GET /api/runs/.../ledger/verify
    Backend-->>UI: 5/5 Hashes Intact, Zero Mutations
```

### What Happened?
1. An AI proposed an optimization for `app/invoices.py` that memoizes database lookups, providing a **42,000x speedup**.
2. However, the AI memoized results using only the invoice ID, omitting the `tenant_id` principal.
3. ProofStack's PSEC DAST probe immediately simulated a multi-tenant request and detected that Tenant B could view Tenant A's private invoices.
4. **The Zero-Compromise Interlock dropped**: G4 failed, rendering the entire patch **REJECTED**, preventing a severe data breach from reaching production.

---

## 8. Verification & Test Suite

ProofStack includes an automated verification gauntlet ensuring that every security boundary, AI schema, and ledger check is functioning.

```bash
# Run the automated Master Verification engine (all tests + PSEC + build)
python scripts/verify_phase.py
```

### Individual Test Suites:
```bash
# 1. Full Pytest Suite (76 passing, 1 skipped)
pytest tests/ -v

# 2. AI Model & Security Boundary Tests
pytest tests/test_model_service.py -v
pytest tests/test_model_api.py -v

# 3. PSEC Security Probe Self-Test (27 passing)
python -m proofstack.psec.selftest

# 4. Frontend Production Compilation Check
cd frontend && npm run build
```

---

## 9. API Reference

All protected endpoints require the `X-API-Key` header matching `SECRET_KEY` in `.env`.

| Method | Path | Auth | Description |
|---|---|---|---|
| `GET` | `/api/health` | Public | Service health & uptime status |
| `GET` | `/api/ready` | Public | Database readiness & connectivity check |
| `GET` | `/api/model/status` | Protected | AI model provider status, active model ID & redacted key preview |
| `GET` | `/api/projects` | Protected | List all registered repositories |
| `POST` | `/api/tasks` | Protected | Register verification task with schema intake |
| `POST` | `/api/runs` | Protected | Dispatch a verification run |
| `GET` | `/api/runs/{id}` | Protected | Fetch run status, lifecycle stage, and telemetry |
| `POST` | `/api/runs/{id}/start` | Protected | Execute verification run through orchestrator |
| `POST` | `/api/runs/{id}/analyze` | Protected | Trigger AI model analysis (NVIDIA Nemotron / rule fallback) |
| `POST` | `/api/runs/{id}/propose-patch` | Protected | Generate structured candidate patch diff |
| `GET` | `/api/runs/{id}/decision` | Protected | Fetch canonical deterministic decision record |
| `GET` | `/api/runs/{id}/ledger/verify` | Protected | Validate SHA-256 cryptographic ledger integrity |
| `GET` | `/api/runs/{id}/events` | Protected | Server-Sent Events (SSE) live telemetry stream |
| `POST` | `/api/demo/seed` | Protected | Seed deterministic 42,000x IDOR demonstration fixture |
| `POST` | `/api/demo/reset` | Protected | Cleanly reset demo fixture data |

---

## 10. Repository Layout

```
├── backend/                  # FastAPI Application & Orchestration
│   ├── model_service.py      # NVIDIA Nemotron client, prompt fencing & fallback
│   ├── orchestrator.py       # Deterministic state machine (BASELINING -> VERIFYING)
│   ├── server.py             # HTTP routes & SSE event streams
│   ├── schemas.py            # Pydantic v2 schemas (extra="forbid")
│   └── models.py             # SQLAlchemy models & cascade constraints
├── proofstack/               # Core Verification Engine
│   ├── psec/                 # Dynamic Application Security Testing (DAST) probes
│   ├── ledger/               # Cryptographic SHA-256 append-only ledger
│   ├── gates/                # Deterministic Gate Evaluators (G0 - G7)
│   └── llm/                  # Prompt templates, fencing & Nebius provider client
├── frontend/                 # Next.js 14 Cockpit (Tailwind CSS, Lucide icons)
│   ├── app/                  # App router views (Overview, Projects, Case Files)
│   ├── components/           # Zero-Compromise Interlock, Diff & Gate Viewports
│   └── lib/api.ts            # Typed client API & backend communication
├── scripts/                  # Verification & Demonstration Tools
│   ├── verify_phase.py       # Master automated verification script
│   ├── verify_e2e.py         # 24-step end-to-end integration verifier
│   └── seed_demo.py          # Deterministic IDOR showcase fixture loader
├── tests/                    # 77 Pytest test suites (Unit, API, Ledger, Security)
├── docs/                     # Comprehensive Phase Reports, Audits & Handoffs
└── pyproject.toml            # Project packaging & pytest configuration
```

---

## 11. Security Disclosures & Production Boundaries

- **Single-Host Sandbox vs. Multi-Tenant**: In local development, low-level process timeouts are used. Production deployments should run sandboxed candidate workloads inside dedicated gVisor / Firecracker microVMs or containerized runner nodes.
- **Ledger Verification**: The SHA-256 hash-chain provides tamper-evident auditability. Remote anchoring (e.g. RFC 6962 transparency log or cloud KMS checkpoints) can be attached for external multi-party non-repudiation.
- **Hardware Telemetry**: NVIDIA GPU telemetry is collected via NVML when available; workloads fall back transparently to CPU without fabricating fake hardware acceleration.

---

## 12. License

Apache-2.0 License.
