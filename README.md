# ADK Agents Assignment (Part A + Part B)

Build and demo production-style AI agents using **Google ADK**, add tools (LangChain + HTTP), and ship a small **hackathon agent app** with a basic **Gradio** UI.

> All deliverables in this repo are **Colab-first**. Each lab has a self-contained `.ipynb` you can “Run all” to reproduce the demo and the smoke tests.

---

## What’s included

* **Part A — 3 Codelabs**

  * **A1**: Prototype → ADK Agent (Kitchen Renovation Proposal)
  * **A2**: Tools with ADK (Wikipedia via LangChain, Open-Meteo Weather, FX)
  * **A3**: SQLite Travel Agent (FunctionTool helpers + deterministic tests)
* **Part B — Hackathon Agent App**

  * **Job Interview Agent** (question generator + answer scorer)

    * **Gradio** UI (2 tabs: Generate Questions / Score Answer)

Each project includes runnable code, smoke tests, and a placeholder for an unlisted YouTube walkthrough.

---

## Repository layout

```
adk-assignment/
  part-a/
    A1-prototype-agent/
      CMPE297A1.ipynb
      README.md
    A2-tools-with-adk/
      CMPE297A2.ipynb
      README.md
    A3-travel-agent-sqlite/
      CMPE297A3.ipynb
      README.md
  part-b/
    job-interview-agent/
      CMPE297B.ipynb
      README.md
      demo_runner.py            # created by the notebook
      tests_smoke.py            # created by the notebook
  videos/
    links.md                    # paste unlisted YouTube URLs
```

> If you prefer local desktop runs, each notebook prints the CLI commands it shells out to (e.g., `adk run a3_travel_agent`) and writes any helper files it generates.

---

## Prerequisites

* Python 3.10+ (Colab uses 3.12 in these runs)
* **Google AI Studio** key set as `GOOGLE_API_KEY` (fastest path)
* Internet access for A2 tools (Wikipedia / Open-Meteo / FX)

> We intentionally **disable Vertex** in all Colabs to avoid OAuth prompts.

---

## Quickstart (Colab)

Open each notebook and click **Runtime → Run all**. Every notebook:

* installs/updates **google-adk** (v1.16.0),
* sets `GOOGLE_API_KEY`,
* writes a small agent package,
* runs the agent via `adk run …`,
* and executes **smoke tests** to verify output shape.

---

## Part A — Codelabs

### A1 — Prototype → ADK Agent (Kitchen Renovation)

**Notebook**: `part-a/A1-prototype-agent/CMPE297A1.ipynb`

What it does:

* Creates `a1_prototype_agent` (no tools).
* Generates a concise Markdown proposal with the sections:

  * Scope of Work
  * Materials & Fixtures
  * Phased Timeline
  * Budget Breakdown (table)
  * Risks & Mitigations
  * Next Steps
* Saves `Kitchen_Renovation_Proposal_*.md` and verifies headings.

**Run snippets used**

* `adk run a1_prototype_agent`
* Simple regex validator: prints `All sections present ✅` when good.

---

### A2 — Tools with ADK (LangChain + Weather + FX)

**Notebook**: `part-a/A2-tools-with-adk/CMPE297A2.ipynb`

Tools (exposed via `FunctionTool`):

* `wiki_via_langchain(topic)` → summary from LangChain’s Wikipedia tool.
* `weather_outlook(city, country?, days?)` → Open-Meteo HTTP API (no key).
* `get_fx_rate(base, target)` → public FX REST API (fallback included).

Agent policy highlights:

* If any tool is used, **end the answer with**:
  `Sources: <comma-separated names>` (e.g., `Wikipedia, Open-Meteo`).
* “Tell me about/history of …” **must** use Wikipedia tool first.
* Weather answers summarize the `summary` field of `weather_outlook`.

**Sample prompts**

* “Tell me about the history of Kyoto (max 4 sentences).”
* “Weather outlook in Tokyo for the next week (short).”
* “Exchange rate from SGD to JPY (return JSON).”

---

### A3 — Travel Agent with SQLite (deterministic)

**Notebook**: `part-a/A3-travel-agent-sqlite/CMPE297A3.ipynb`

What it builds:

* Fresh `travel.db` with tables: `hotels`, `attractions`, `flights`.
* `a3_travel_agent` exposing 3 SQL helpers:

  * `hotels_by_city(city, max_price?, limit?)`
  * `attractions_by_city(city, category?, limit?)`
  * `flights_search(origin, dest, max_price?, limit?)`

Important detail:

* The tool functions **return pre-formatted strings** that include a header (`Hotels:`, `Attractions:`, `Flights:`) **and** `Source: travel.db`.
  The agent is instructed to **print them verbatim** for single-tool queries.

Regression tests:

* 3 tests for Hotels/Attractions/Flights patterns.
* Expected output ends with `Source: travel.db`.
* The notebook prints `All tests passed ✅` when successful.

---

## Part B — Hackathon Agent App (Job Interview Agent)

**Notebook**: `part-b/job-interview-agent/CMPE297B.ipynb`

What it includes:

* `job_interview_agent` with **two tools that return final strings**:

  * `get_questions(role, count) -> "Questions:\n1. …\n2. …"`
  * `score_answer(answer, question?) -> "Score: X. Feedback: …"`
* **Strict tool-only policy**: every user message triggers exactly one tool, and the **tool’s output is printed verbatim** (no “Okay.”, no extra prose).
* A small **smoke test**:

  * Ensures `Questions:` format for generation.
  * Ensures `Score:` + `Feedback:` for grading.
  * Prints `Smoke tests passed ✅` when good.
* `demo_runner.py` writes a reproducible `demo_output.txt` with two prompts and outputs.
* A tiny **Gradio** UI with two tabs:

  * “Generate Questions” (role + count)
  * “Score Answer” (optional question + answer)
  * In Colab it launches a public `*.gradio.live` URL.

**Package/version notes**

* ADK: `google-adk==1.16.0`
* Gradio: `gradio==5.4.0`
* Websockets pin to satisfy ADK & google-genai:

  * `websockets==15.0.1`

**Typical outputs**

```
Questions:
1. How do you structure a React component for reusability?
2. Explain hydration and server-side rendering.

Score: 3. Feedback: Clarify reasoning and add specifics.
```

---

## Environment & keys

Set **Google AI Studio** key:

```bash
export GOOGLE_API_KEY=your_real_key_here
```

In the notebooks we use:

```python
os.environ["GOOGLE_API_KEY"] = KEY
# and force non-Vertex:
for var in ["GOOGLE_GENAI_USE_VERTEXAI","GOOGLE_VERTEX_PROJECT","GOOGLE_VERTEX_LOCATION","GOOGLE_CLOUD_PROJECT"]:
    os.environ.pop(var, None)
```

> Do **not** commit real keys. Each folder should include a `.env.example`; copy to `.env` locally if you run outside Colab.

---

## How to run locally (optional)

* Create a venv, install requirements printed by each notebook (at minimum `google-adk==1.16.0`).
* Ensure `GOOGLE_API_KEY` is set in your shell or the package’s `.env`.
* From the folder with the generated agent package, run:

```bash
adk run a3_travel_agent
# or
adk run job_interview_agent
```

For the Gradio UI (Part B), install:

```bash
pip install "websockets==15.0.1" "gradio==5.4.0"
python -m gradio <script>   # notebook generates the Blocks app in-code
```

---

## Video links (unlisted)

Update `videos/links.md` when you upload:

```md
# Videos
- A1 — Prototype → ADK Agent: https://youtu.be/XXXXXXXXXXX
- A2 — Tools with ADK:       https://youtu.be/XXXXXXXXXXX
- A3 — Travel Agent (SQLite): https://youtu.be/XXXXXXXXXXX
- Part B — Job Interview + UI: https://youtu.be/XXXXXXXXXXX
```

---

## Troubleshooting (based on issues we hit)

* **400 INVALID_ARGUMENT: API key not valid**
  Set a real **AI Studio** key in the notebook before running. Restart the runtime after changing it.

* **ADK `model_params` validation error**
  Current ADK models don’t accept arbitrary `model_params`. Remove custom `model_params={...}` from the `Agent(...)` call.

* **“Okay.” / extra prose breaks tests**
  Tools already return final strings. The agent **must** print them verbatim. We enforce this with a strict instruction block.

* **“no such column …” in A3**
  Make sure your tool SQL matches the **current** SQLite schema. The notebook overwrites `db_tools.py` with the right queries.

* **ADK CLI sessions hanging**
  When scripting from Python, pipe `exit\n` after your prompt (we use `run_adk_once(...)`) to close the interactive session cleanly.

* **Gradio import error / websockets conflict**
  Pin:
  `pip install "websockets==15.0.1" "gradio==5.4.0"`
  Then flush `sys.modules` entries for `websockets` and re-import.

---

## Submission checklist

* [ ] Part A — A1 notebook runs and proposal validates
* [ ] Part A — A2 notebook runs; tools invoked; “Sources:” appended
* [ ] Part A — A3 notebook runs; **tests pass**
* [ ] Part B — Job Interview Agent runs; smoke tests pass; Gradio UI launches
* [ ] `videos/links.md` updated with 3–4 unlisted links
* [ ] `.env.example` checked in; **no real secrets** in repo

---

## License

Academic use only; retain third-party licenses/attribution where applicable.

---

### Credits

* Google **ADK**
* LangChain community tools
* Open-Meteo
* Community hackathon agent ideas (adapted for the Job Interview Agent)
