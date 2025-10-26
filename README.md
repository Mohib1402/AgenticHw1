# ADK Agents — Four Colabs + One Demo Video

Build and demo production-style AI agents with **Google ADK**. This repo ships four **Colab-first** notebooks you can “Run all” to reproduce the results end-to-end.

> One video covers everything: A1, A2, A3, and Part B.

---

## What’s inside

* **A1 — Prototype → ADK Agent (Kitchen Renovation)**

  * Minimal ADK agent (no tools) that generates a concise Markdown proposal with required sections.
  * Saves a `Kitchen_Renovation_Proposal_*.md` file and validates sections.

* **A2 — Tools with ADK (LangChain + Weather + FX)**

  * Three tools exposed via `FunctionTool`:

    * `wiki_via_langchain(topic)` → concise summary (LangChain Wikipedia).
    * `weather_outlook(city, country?, days?)` → Open-Meteo forecast (no key).
    * `get_fx_rate(base, target)` → public FX rate JSON (fallback included).
  * Agent policy appends `Sources: …` when tools are used.

* **A3 — Travel Agent with SQLite**

  * Seeds `travel.db` and exposes three SQL tools:

    * `hotels_by_city`, `attractions_by_city`, `flights_search`.
  * Tools return **final formatted strings** (include header + `Source: travel.db`).
  * Deterministic **A3-6 regression tests** pass.

* **Part B — Job Interview Agent + Gradio UI**

  * Two tools that return final strings:

    * `get_questions(role, count)` →

      ```
      Questions:
      1. …
      2. …
      ```
    * `score_answer(answer, question?)` →
      `Score: X. Feedback: …`
  * Strict **tool-only** output (no extra words).
  * **Smoke tests** pass.
  * Simple **Gradio UI** with two tabs: *Generate Questions* / *Score Answer*.

---

## Files

```
README.md
CMPE297A1.ipynb   # A1: Prototype → ADK Agent
CMPE297A2.ipynb   # A2: Tools with ADK
CMPE297A3.ipynb   # A3: SQLite Travel Agent (deterministic tests)
CMPE297B.ipynb    # Part B: Job Interview Agent + Gradio UI
```

---

## Prerequisites (Colab-first)

* Python 3.10+ (Colab runtime uses 3.12 in our runs)
* **Google AI Studio** API key (fastest path)

  * Set in-notebook as `os.environ["GOOGLE_API_KEY"] = "…"`.
  * We explicitly **disable Vertex** in all notebooks to avoid OAuth prompts.
* Internet access (A2 tools + Part B UI)

> Do **not** commit real keys. Use environment variables only.

---

## How to run (Colab)

1. Open each notebook:

   * `CMPE297A1.ipynb`
   * `CMPE297A2.ipynb`
   * `CMPE297A3.ipynb`
   * `CMPE297B.ipynb`
2. Click **Runtime → Run all**.
3. Each notebook:

   * Installs/updates `google-adk==1.16.0` (and other pins it needs).
   * Sets `GOOGLE_API_KEY`.
   * Writes the agent package files it needs.
   * Uses `adk run …` under the hood.
   * Prints **smoke-test** results (A3 prints “All A3-6 tests passed ✅”; Part B prints “Smoke tests passed ✅”).
4. In Part B, the **Gradio UI** launches and prints a `*.gradio.live` URL.

---

## Video (single link for all four)

* **Full demo (A1 + A2 + A3 + Part B):** [DEMO](https://drive.google.com/file/d/1rUVncP4FqRRukMc_mHnILzWLkApCd-vn/view?usp=sharing)

---

## Reproducibility & pins

* `google-adk==1.16.0`
* For Part B (Gradio) compatibility:

  * `websockets==15.0.1`
  * `gradio==5.4.0`

The notebooks:

* ensure **non-Vertex** routing by unsetting `GOOGLE_GENAI_USE_VERTEXAI`, `GOOGLE_VERTEX_*`, etc.
* use **tool-only, verbatim** output where formatting matters (A3 + Part B).
* close ADK CLI sessions by piping `exit\n` for stable scripted runs.

---

## Expected outputs (high level)

* **A1**: Markdown proposal with headings: *Scope of Work*, *Materials & Fixtures*, *Phased Timeline*, *Budget Breakdown*, *Risks & Mitigations*, *Next Steps*.
* **A2**: Short answers ending with `Sources: Wikipedia, Open-Meteo` (or similar) when tools run; JSON for FX on request.
* **A3**:

  ```
  Hotels:
  *   The Gate Hotel Asakusa — Asakusa — $135
  …
  Source: travel.db
  ```

  *(similar for Attractions/Flights; tests pass)*
* **Part B**:

  ```
  Questions:
  1. …
  2. …

  Score: 3. Feedback: Clarify reasoning and add specifics.
  ```

---

## Troubleshooting (based on what we hit)

* **INVALID API key** → set a real **AI Studio** key before running; restart runtime if you change it.
* **`model_params` validation error** in ADK → remove custom `model_params` from `Agent(...)` (some versions reject them).
* **Agent adds “Okay.” or extra prose** → the strict instruction forces **verbatim tool output**; don’t alter it.
* **SQLite schema mismatches (A3)** → notebook overwrites `db_tools.py` with queries that match the current schema.
* **Gradio/websockets import errors** → pin to `websockets==15.0.1` and `gradio==5.4.0`, then re-import.

---
