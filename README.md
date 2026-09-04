# 🛠️ Self-Healing RAG: Autonomous Troubleshooting Copilot

![Python](https://img.shields.io/badge/Python-3.11+-blue.svg)
![LangGraph](https://img.shields.io/badge/Orchestration-LangGraph-orange.svg)
![VectorDB](https://img.shields.io/badge/Vector_DB-Chroma%20%7C%20FAISS-green.svg)
![Evaluation](https://img.shields.io/badge/Evaluation-RAGAS-red.svg)

##  Overview
**Self-Healing RAG** is an autonomous, self-correcting Retrieval-Augmented Generation pipeline designed to detect and repair its own failures before serving an answer to the user. 

Unlike standard RAG pipelines that blindly generate answers from whatever context they retrieve (often leading to hallucinations), this system uses a **closed feedback loop**. Inspired by Corrective RAG (CRAG) and Self-RAG architectures, our pipeline explicitly grades retrieval quality, rewrites queries, and iteratively re-retrieves data until it finds the correct context—defaulting to a graceful refusal only if all repair attempts are exhausted.

**Target Domain:** IT DevOps, Cloud Infrastructure (AWS/Kubernetes), and System Troubleshooting.

---

##  The Problem vs. The Solution

###  The Problem: Silent Retrieval Failures
Standard RAG pipelines assume the first retrieval attempt is always "good enough". If a user asks a highly ambiguous technical question (e.g., *"Why is my ingress failing with 502?"*), naive RAG fetches irrelevant network docs and confidently hallucinates an answer because nothing in the architecture checks if the retrieval was actually successful.

###  The Solution: Detect & Repair
Self-Healing RAG makes retrieval quality an explicit, evaluated step.
1. **Diagnose:** An LLM-as-a-judge grades the retrieved chunks (Correct, Ambiguous, Incorrect).
2. **Repair:** If the context is poor, the agent rewrites the query or decomposes it into sub-questions.
3. **Re-retrieve:** The system fetches new context and tries again, up to a strict retry limit.

---

##  System Architecture & The Healing Loop

The core engine is a state machine built with **LangGraph**. The workflow follows this autonomous loop:

```text
Query ➔ Retrieve ➔ Judge Quality ➔ Repair & Re-retrieve ➔ Generate ➔ Verify
                            ↑________________________| (Loops up to N times)
