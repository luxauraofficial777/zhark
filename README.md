# zhark
<img width="1024" height="1024" alt="fubbu" src="https://github.com/user-attachments/assets/accc77a0-ccc9-48a9-bca7-2f8544ce7027" />

Lux Aura Presents "ZHARK" Automonous Long-Horizon Agent for "Liminal Lore" Agentic Toolchain Harness

# ZHARK

**Autonomous long-horizon research and strategy engine — the Virtual CSO.**

ZHARK is our domestic equivalent to Sakana Marlin. It runs multi-hour investigation cycles autonomously, forming hypotheses, gathering web evidence, resolving contradictions, mapping causal relationships, and synthesizing executive-ready strategic options. No cloud dependency — runs entirely on local LLMs via Ollama or any OpenAI-compatible endpoint.

# Zhark Release Document
## Version: v1.1 — August 12, 2026

**Author:** Lux Aura / Antigravity Suite  
**Target Subsystem:** Autonomous Research & Hypothesis Synthesis Engine  

---

### Executive Summary

**Zhark v1.1** (+0.1 upgrade) consolidates all historical vendored forks under a single canonical engine (`tools/zhark/zhark_orchestrator.py`), merges MAG engine vector integration with thread-safe signal handlers, and supports GammaLanguage v1.11C `?ZHARK` research directives.

---

### Key Technical Upgrades in v1.1

1. **HAZ-01 Tool Fork Consolidation:**
   - Merged 5 historical version forks into the canonical `tools/zhark/` module (expanded from 736 to 772 lines).
   - Combined MAG vector engine integration and `token_counter` from canonical with thread-safe signal handling and Gamma frame wrapping from V1.1C.
   - Installed `importlib` forwarding shims across legacy directories.

2. **`?ZHARK` Directive Routing:**
   - Wired Gamma v1.11C Opcode `0x16` (`?ZHARK`) for dispatching asynchronous research queries into hive contexts (`#HIVE`).

3. **MAG Memory Persistence:**
   - Automated commitment of confirmed research hypotheses and evidence graphs to LTESM semantic memory nodes via `!MEM_COMMIT`.

---

### Verification Matrix

- **Import Integrity:** Canonical `zhark` imports cleanly across all legacy paths.
- **Orchestration Test:** Multi-hypothesis research cycles complete without signal collisions.


---

## What It Does

Give ZHARK a strategic question. It will:

1. **Form hypotheses** — Generate 3-5 testable, falsifiable claims from your question
2. **Gather evidence** — Autonomously search the web (DuckDuckGo, no API key needed) and extract content
3. **Analyze** — Use LLM to determine if evidence supports or refutes each hypothesis, with relevance weighting
4. **Verify** — Generate multiple candidate analyses, score consistency, track convergence across revisions (inference-time compute scaling)
5. **Detect contradictions** — Flag conflicting hypotheses and supersede the weaker ones
6. **Refine** — Spawn sub-hypotheses for inconclusive findings, deepening the investigation tree
7. **Map causality** — Extract causal relationships (direct, mediated, confounded, feedback, inhibitor) and identify leverage points
8. **Synthesize strategy** — Convert findings into executive-ready options with risk level, timeframe, confidence, key assumptions, and leading indicators

A full investigation runs **50 iterations × ~8 min = ~6.5 hours autonomous**, then produces a markdown + JSON strategy report.

---

## Orchestration Loop

```
HYPOTHESIS → GATHER → ANALYZE → VERIFY → CONTRADICT → REFINE → CAUSAL → SYNTHESIZE
     ↑                                                                    │
     └────────────────────────────────────────────────────────────────────┘
                          (repeat until convergence)
```

**Convergence criteria:** >70% of hypotheses resolved (SUPPORTED or REFUTED) AND ≥2 supported.

---

## Modules

| File | Purpose |
|------|---------|
| `zhark_orchestrator.py` | Main async loop controller with signal handlers for graceful shutdown |
| `zhark_hypothesis_graph.py` | DAG of hypotheses with weighted evidence, contradiction detection, confidence scoring |
| `zhark_causal_mapper.py` | Causal DAG: direct, mediated, confounded, feedback, inhibitor edges. Leverage point identification |
| `zhark_strategy_synthesizer.py` | Converts findings to executive-ready options (title, rationale, risk, timeframe, confidence, assumptions, indicators) |
| `zhark_state.py` | SQLite state (4 tables) + VerifyBuffer for inference-time compute scaling. Checkpoint/resume every cycle |
| `zhark_web_gatherer.py` | DuckDuckGo HTML scraping + content extraction. Rate-limited, auto-generates "evidence for/against" queries |
| `zhark_verify.py` | VerifyBuffer re-exports |
| `__main__.py` | CLI: run, status, resume, cycles, report |

---

## Verification Buffer — Inference-Time Compute Scaling

ZHARK "thinks longer" on hard problems by generating multiple candidate outputs for the same query:

```
Query: "Analyze hypothesis: X causes Y"
    ├── Candidate 1 (temp=0.4) → Score: 0.72
    ├── Candidate 2 (temp=0.5) → Score: 0.68
    └── Candidate 3 (temp=0.6) → Score: 0.75

    Consensus: 0.717 (spread: 0.07) → CONSISTENT → Accepted
```

- **3-5 candidates** at increasing temperatures for diversity
- **Jaccard similarity** for pairwise consistency scoring
- **Convergence tracking** across revisions (improving / stable / insufficient)
- **Acceptance:** avg score ≥ threshold AND spread < 0.2

---

## State Management

SQLite-backed with 4 tables:

| Table | Purpose |
|-------|---------|
| `zhark_cycles` | Research cycle metadata (question, phase, iteration, status) |
| `zhark_verification` | Verification buffer entries (query, output, score, status) |
| `zhark_checkpoints` | Full state snapshots (hypothesis graph, causal map, strategy) |
| `zhark_telemetry` | Audit trail — all events with timestamps |

Every cycle, ZHARK checkpoints its full state. Resume from any checkpoint with `python -m zhark resume <cycle_id>`.

---

## CLI

```powershell
# Run a research cycle
python -m zhark run "What is the optimal Go-to-Market strategy for AI dev tools in 2026?"

# Custom parameters
python -m zhark run "Question" --max-iterations 100 --convergence 0.85 --max-depth 7

# Check status
python -m zhark status

# Resume an interrupted cycle
python -m zhark resume <cycle_id>

# List all cycles
python -m zhark cycles

# View a cycle report
python -m zhark report <cycle_id>
```

---

## Configuration

| Env Variable | Default | Description |
|--------------|---------|-------------|
| `ZHARK_MAX_ITERATIONS` | `50` | Max investigation cycles |
| `ZHARK_CONVERGENCE_THRESHOLD` | `0.75` | Avg confidence to stop early |
| `ZHARK_MAX_DEPTH` | `5` | Max hypothesis tree depth |
| `ZHARK_CYCLE_DELAY` | `5` | Seconds between cycles |
| `ZHARK_VERIFY_CANDIDATES` | `3` | LLM candidates per verification |

ZHARK uses the Liminal Lore `LLMProvider` abstraction — point it at Ollama, vLLM, OpenRouter, or any OpenAI-compatible endpoint via the shared config system.

---

## Output

Each completed cycle produces:

- **`zhark_report_<cycle_id>.md`** — Executive-ready markdown strategy report
- **`zhark_report_<cycle_id>.json`** — Full structured report (hypotheses, causal map, strategy options)
- **`hyp_checkpoint_<cycle_id>.json`** — Hypothesis graph snapshot
- **`causal_checkpoint_<cycle_id>.json`** — Causal map snapshot

---

## Integration

- **LLM Provider** — Uses shared `config/provider_abstraction.py` (Ollama, OpenAI-compat, OpenRouter)
- **VW Nexus** — Can register as an agent, query RAG, post findings as tasks
- **Setup Wizard** — ZHARK env vars included in `.env` template; `--ship` validates ZHARK

---

## Comparison to Sakana Marlin

| Capability | Marlin | ZHARK |
|-----------|--------|-------|
| Autonomous multi-hour execution | ✓ | ✓ (checkpoint/resume) |
| Hypothesis formation | ✓ | ✓ (LLM + graph) |
| Web data gathering | ✓ | ✓ (DuckDuckGo, no API key) |
| Contradiction resolution | ✓ | ✓ (automatic supersede) |
| Causal mapping | Partial | ✓ (full causal DAG) |
| Strategy synthesis | Partial | ✓ (executive-ready) |
| Inference-time compute scaling | ✓ | ✓ (verification buffer) |
| Self-hosted | ✗ | ✓ (no cloud dependency) |
| Cost | API usage fees | Free (local models) |

---

<div align="center">

**LUX AURA**

*Autonomous agentic systems for the curious.*

[🌐 Bandcamp](https://luxaura.bandcamp.com) · [📘 Facebook](https://facebook.com/LuxAuraOfficial) · [💻 GitHub](https://github.com/luxauraofficial777) · [▶️ YouTube](https://youtube.com/c/LuxAuraOfficial)

</div>

