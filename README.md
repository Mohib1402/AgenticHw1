---

# ADK Agents Assignment (Part A + Part B)

> Build and demo production-style AI agents using the **Agent Development Kit (ADK)**, attach tools (including **MCP Toolbox**), and ship a hackathon-grade agent app.

## 🎯 What’s included

* **Part A — 3 Codelabs**

  1. *From Prototypes to Agents with ADK*
  2. *Building AI Agents with ADK: Empowering with Tools*
  3. *Travel Agent using MCP Toolbox (DB) + ADK*
* **Part B — Hackathon Agent App**

  * Recreate one app from the curated hackathon list with your own twist

Each project includes: **code**, **README**, and an **unlisted YouTube video** showing a code walkthrough and live execution.

---

## 📁 Repository Layout

```
adk-assignment/
  part-a/
    01-prototypes-to-agents/
      README.md
      colab.ipynb            # or /app with desktop code
      requirements.txt
      .env.example
    02-tools-with-adk/
      README.md
      colab.ipynb            # or /app with desktop code
      requirements.txt
      .env.example
    03-travel-agent-mcp/
      README.md
      app/
        main.py
        requirements.txt
        .env.example
      mcp-toolbox/
        tools.yaml           # MCP tools config (DB queries)
        notes.md
  part-b/
    chosen-hackathon-agent/
      README.md
      app/
        main.py
        requirements.txt
        .env.example
      eval/
        test_sanity.py       # optional “lite eval”
  shared/
    scripts/
      record_demo_checklist.md
  videos/
    links.md                 # paste all unlisted YouTube URLs here
```

---

## 🧰 Prerequisites

* **Python 3.10+**
* A Google model path:

  * **Fastest local dev:** **Google AI Studio** API key

    * Set `GOOGLE_API_KEY` in `.env`
  * **(Optional) Vertex AI** (for GCP/Cloud Run/Cloud SQL integration)

    * Use Application Default Credentials (ADC) or service account
* **(For A3 only)** a Postgres database (Cloud SQL or local Docker) and **MCP Toolbox** binary

> If using Colab, install packages within the notebook and store secrets with `os.environ` or Colab secrets.

---

## 🚀 Quickstart (Local Desktop)

```bash
# create & activate a venv
python -m venv .venv && source .venv/bin/activate

# Part A, lab 1 (example)
cd part-a/01-prototypes-to-agents
pip install -r requirements.txt

# copy env and add your key(s)
cp .env.example .env
# open .env and set GOOGLE_API_KEY=...

# run
python main.py        # or: adk run    (if CLI is available)
```

**Colab path:** open `colab.ipynb`, install `requirements.txt` contents with `pip`, and set environment variables in-notebook.

---

## 🔐 Environment Variables

Create `.env` from `.env.example` in each project you run.

```ini
# AI Studio (local dev)
GOOGLE_API_KEY=your_api_key_here

# Optional: Vertex AI (when deploying to GCP)
GCP_PROJECT_ID=your_project_id
GCP_LOCATION=us-central1
VERTEX_MODEL=projects/PROJECT/locations/LOCATION/publishers/google/models/gemini-2.0-flash
```

**Tip:** Start with **AI Studio** locally. Switch to **Vertex** only if you need Cloud Run/Cloud SQL or GCP IAM.

---

## 🧩 Part A — Codelabs

### A1) From Prototypes to Agents with ADK

**Goal:** Stand up a minimal agent using ADK and run basic prompts.

**Run**

```bash
cd part-a/01-prototypes-to-agents
pip install -r requirements.txt
cp .env.example .env   # set GOOGLE_API_KEY
python main.py         # or adk run
```

**Try these prompts**

* “Summarize this paragraph in 3 bullets.”
* “Draft a 5-step plan to prepare for a demo.”

**Deliverables**

* ✅ Code (Colab **or** desktop)
* ✅ README explaining setup/run and sample prompts
* ✅ YouTube video (3–5 min): show code → run 1–2 prompts → recap

---

### A2) Building AI Agents with ADK: Empowering with Tools

**Goal:** Attach 2–3 tools and demonstrate tool selection and invocation.

**Suggested tools**

* **Function tool** (e.g., `get_current_date()` or `csv_stats(path)`)
* **Built-in search tool** (to ground responses)
* *(Optional)* Third-party tool (LangChain/OpenAPI/MCP)

**Run**

```bash
cd part-a/02-tools-with-adk
pip install -r requirements.txt
cp .env.example .env
python main.py
```

**Demo flow**

1. Ask something that **requires** the function tool
2. Ask for “latest on <topic>” to trigger search
3. Show tool call logs and the final answer

**Deliverables**

* ✅ Code + README
* ✅ YouTube video (4–6 min): intro → tools → live run → recap

---

### A3) Travel Agent using MCP Toolbox (DB) + ADK

**Goal:** Query a hotels DB through **MCP Toolbox** tools and compose natural answers.

**Local DB (suggested for dev)**

```bash
# example: local Postgres via Docker (adjust to your setup)
docker run --name mcp-pg -e POSTGRES_PASSWORD=postgres -e POSTGRES_USER=postgres \
  -e POSTGRES_DB=travel -p 5432:5432 -d postgres:16
# seed script/SQL provided in this folder (see mcp-toolbox/notes.md)
```

**Start MCP Toolbox**

```bash
cd part-a/03-travel-agent-mcp/mcp-toolbox
# Ensure tools.yaml points to your DB (host, port, user, db)
# Download/permit the toolbox binary, then:
./mcp-toolbox --config ./tools.yaml   # name may vary; document exact binary in notes.md
```

**Run the agent**

```bash
cd ../app
pip install -r requirements.txt
cp .env.example .env    # DB creds if needed, GOOGLE_API_KEY, etc.
python main.py
```

**Try these prompts**

* “Find 3 hotels in <city> under $200/night with breakfast.”
* “Search hotel names containing ‘Hilton’ and sort by rating.”

**Deliverables**

* ✅ Code + README
* ✅ YouTube video (6–8 min): arch diagram → show `tools.yaml` → start toolbox → run 2 queries → recap

---

## 🧪 (Optional) “Lite Eval”

Add a tiny sanity test that asserts expected substrings and/or tool calls:

```python
# part-b/chosen-hackathon-agent/eval/test_sanity.py
import subprocess, json

def run_agent(input_text: str) -> str:
    # replace with your actual entrypoint/call
    out = subprocess.check_output(["python", "app/main.py", "--prompt", input_text])
    return out.decode()

def test_happy_path():
    text = run_agent("Plan a 2-day NYC itinerary with a museum")
    assert "Day 1" in text or "itinerary" in text
```

Run with:

```bash
pip install -r requirements.txt pytest
pytest -q
```

---

## 🏆 Part B — Hackathon Agent App

**Pick one** project from the curated list and re-implement a minimal, working version. Add your twist (feature, UX, or deployment). Keep a **golden demo path** that is deterministic.

**Run**

```bash
cd part-b/chosen-hackathon-agent
pip install -r requirements.txt
cp .env.example .env
python app/main.py
```

**Deliverables**

* ✅ Code + README (user story, features, setup, run, demo prompts)
* ✅ YouTube video (5–8 min): why → architecture → live run → recap
* ✅ (Optional) eval/ tests

---

## 📹 Video Links (Unlisted YouTube)

Update `videos/links.md` with your final URLs:

```md
# Videos
- Part A — Codelab 1: From Prototypes to Agents → https://youtu.be/XXXXXXXXXXX
- Part A — Codelab 2: Tools with ADK → https://youtu.be/XXXXXXXXXXX
- Part A — Codelab 3: Travel Agent (MCP + DB) → https://youtu.be/XXXXXXXXXXX
- Part B — Hackathon Agent App → https://youtu.be/XXXXXXXXXXX
```

---

## ✅ Submission Checklist

**Part A**

* [ ] A1: Code + README + video
* [ ] A2: Code + README + video
* [ ] A3: Code + README + video (with MCP Toolbox + DB)

**Part B**

* [ ] Code + README + video
* [ ] *(Optional)* eval tests

**Repo**

* [ ] `.env.example` in each app
* [ ] `videos/links.md` populated
* [ ] Clear run instructions verified on a clean venv/Colab

---

## 🧯 Troubleshooting

* **Auth errors**: Ensure `GOOGLE_API_KEY` (AI Studio) or ADC (Vertex) is set. Restart shell/Colab runtime after changes.
* **Tool not invoked**: Tighten tool descriptions and inputs; reduce number of tools to avoid confusion.
* **MCP Toolbox connection**: Start the toolbox **before** running the agent; verify DB host/port/user in `tools.yaml`.
* **DB not seeded**: Run seed SQL and re-try your queries with terms known to exist.
* **Rate limits / timeouts**: Reduce result sizes and keep prompts deterministic.

---

## 📄 License

This repository is for academic use. If you reuse third-party code, retain original licenses and add attribution in each sub-project’s README.

---

### Credits

* Google ADK, MCP Toolbox, and community hackathon projects that inspired these builds.
