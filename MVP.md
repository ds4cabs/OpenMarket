# OpenMarket — MVP Version (Gemini AI Agent · Interactive Dashboard)

**Intern:** Jason Zhou
**Level:** UCSC undergrad, Intermediate Python
**Timeline:** 2–3 weeks (~48 hours)
**Paradigm:** **Interactive Dashboard** — Streamlit dashboard for GLP-1 RA class with drug selectors, side-by-side comparison views, on-click AI commercial brief.
**Database count:** **6** (expanded from 4 to add DailyMed for richer labels and RxNorm for cross-source drug normalization).

---

## The Agent

**What the agent does (autonomous workflow on click):** User selects 2+ drugs. "Generate commercial brief" triggers an autonomous Gemini workflow that calls 6 databases per drug, normalizes names across sources, pulls richer label info from DailyMed, and produces a comparative brief.

**Input:** Drug selection via Streamlit UI.
**Output:** Comparison table on screen. AI commercial brief on click (downloadable Markdown/PDF).

**Tools (6 public databases):**

1. `get_drug_approvals(drug_name)` — **openFDA Drugs@FDA**.
2. `get_drug_pricing(drug_name)` — **CMS NADAC**.
3. `get_faers_volume(drug_name, year)` — **openFDA FAERS**.
4. `get_exclusivity(drug_name)` — **FDA Orange Book**.
5. `get_dailymed_label(drug_name)` — **DailyMed API**: full structured prescribing label including boxed warnings, indications, dosing.
6. `get_rxnorm_normalize(drug_name)` — **RxNorm API**: standardize drug names (brand ↔ generic ↔ ingredient) across sources.

**Example runs (≥3):**

- *Dashboard:* Ozempic + Mounjaro. *Brief:* approval gap, pricing trajectory, FAERS volume comparison, boxed-warning content (DailyMed), normalized active-ingredient comparison (RxNorm: semaglutide vs tirzepatide).
- *Dashboard:* Wegovy alone. *Brief:* commercial profile with full label content extracted.
- *Dashboard:* all 5 GLP-1 RAs. *Brief:* class brief with normalized active ingredients and side-by-side label comparison.

---

## Week-by-Week

**Week 1 (~16h):** Build 6 tool functions; pre-fetch and cache data for 5 GLP-1 drugs.
**Week 2 (~22h):** Clone dashboard sub-template. Streamlit UI + Gemini agent.
**Week 3 (~10h):** Test 3 interactions; tune; brief-download; demo.

## What's OUT

SEC EDGAR forecasting, peak-sales model, ClinicalTrials.gov competitive scanner, Open Targets target-mechanism, 500 drugs / 15 TAs, BD inverse view.

## Stretch Goals

- 7th tool: `get_clinical_trials(drug_name)` for pipeline scan tab.

## Realistic CV Entry

*Built OpenMarket, a working interactive Streamlit dashboard with an embedded Gemini AI agent integrating 6 public databases for pharmaceutical commercial intelligence.*

- Wrapped 6 public databases (Drugs@FDA, CMS NADAC, openFDA FAERS, FDA Orange Book, DailyMed, RxNorm) into a Gemini agent invoked on user click for downloadable commercial briefs.
- Built drug-name normalization across heterogeneous data sources using RxNorm, enabling clean cross-source joins.

## Tech Stack

Python, `google-generativeai`, Streamlit, requests, pandas, markdown-pdf, openFDA API, CMS Open Data API, FDA Orange Book, DailyMed API, RxNorm API.

---

## Shared Agent Skeleton (three paradigms, one Gemini primitive)

Every intern's agent uses Gemini's automatic function calling, but the interface layer differs by paradigm. The cohort uses **one starter repo with three sub-templates** that interns clone in week 1:

- **Dossier-generator template** — CLI script: takes structured args, runs the agent workflow autonomously, writes `*.md` + `*.json` to disk. Used by Beyza, Chin Hung, Christina, Shucheng, Xiaoxue.
- **Dashboard template** — Streamlit page with selectors and tables; the agent is invoked on button-click for specific synthesis tasks. Used by Aaron, Jason, Shawn.
- **Computation-engine template** — Streamlit form (or CLI) that takes structured analytical inputs, runs the agent workflow, produces a downloadable analytical report with plots. Used by Reuben, Kening, Natalie.

**Why no chat interfaces?** Scientists need reproducible, shareable artifacts. The agent dimension (Gemini-as-orchestrator, autonomous tool-calling across multiple public databases, synthesis across sources) is preserved in all three paradigms; only the deliverable shape changes.

**Christina** (OpenRepurpose evidence-and-validation module) owns the starter repo with all three sub-templates. The shared repo should also include pre-built wrappers for the most heavily-used databases (ChEMBL, openFDA FAERS, Open Targets, ClinVar) so multiple interns don't redo the same boilerplate.

### Reference snippet — Gemini function calling (same across all three paradigms)

```python
import google.generativeai as genai
import os
genai.configure(api_key=os.environ["GEMINI_API_KEY"])

def my_tool(arg: str) -> dict:
    """One-line docstring Gemini uses to decide when to call this tool."""
    return {"result": ...}

model = genai.GenerativeModel(
    model_name="gemini-2.5-flash",
    tools=[my_tool, other_tool, ...],   # 4-8 tools per agent
    system_instruction=open("system_prompt.md").read(),
)
chat = model.start_chat(enable_automatic_function_calling=True)
response = chat.send_message("structured request — one shot, not a conversation")
```
