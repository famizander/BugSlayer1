# 🐞 BugSlayer

**An AI debugging tutor that helps beginners *understand* bugs, not just fix them.**

Built by team **Strivaholics** for **Hackpire Lumina**.

## The problem

- Beginner programmers struggle to read compiler errors and debug.
- Existing AI tools hand over corrected code without explaining the underlying problem.
- Students need guidance to build independent debugging skills.

## What BugSlayer does

1. Paste C++ code. BugSlayer compiles it (syntax check only, nothing is executed) and collects the compiler errors.
2. It explains each error in plain language.
3. It gives **progressive hints** instead of the answer:

| Level | What the student gets |
|-------|-----------------------|
| 1 | Nudge: where to look, no spoilers |
| 2 | Concept: what this kind of error means and why it happens |
| 3 | Pointer: the exact line and what is wrong with it |
| 4 | Solution: the corrected code with a full explanation |

4. After a bug is dealt with, it generates a short practice exercise targeting the same weakness.
5. Hint usage is tracked, so the team can measure learning impact (see `docs/metrics.md`).

## Architecture

```
frontend/index.html   Browser UI (no build step)
backend/app.py        FastAPI: /api/analyze, /api/hint, /api/quiz
backend/compiler.py   g++ -fsyntax-only wrapper + error parser
backend/ai.py         Claude-powered hints, with an offline fallback
backend/prompts.py    Tutor prompts (the learning-first behavior lives here)
backend/offline_kb.py Rule-based explanations for common errors (works with no API key)
tests/                pytest suite
docs/                 Lean canvas, roadmap, metrics
```

## Quick start

Requirements: Python 3.10+, `g++` installed (`sudo apt install g++` or Xcode CLT on macOS).

```bash
git clone <your-repo-url> bugslayer && cd bugslayer
python -m venv .venv && source .venv/bin/activate
pip install -r backend/requirements.txt

cp .env.example .env        # add your ANTHROPIC_API_KEY (optional)
uvicorn backend.app:app --reload
```

Open http://localhost:8000.

With no API key, BugSlayer still runs using the built-in rule-based explanations, which is handy for a demo with no internet.

### Docker

```bash
docker build -t bugslayer .
docker run -p 8000:8000 --env-file .env bugslayer
```

### Tests

```bash
pytest
```

## Roadmap

- **Phase 1:** Web interface, C++ input, compiler error explanations, AI hints, sample errors. *(scaffolded here)*
- **Phase 2:** Fuller AI analysis, skill-gap detection from quiz results, progress dashboard.
- **Phase 3:** Pilot with real beginners, before/after debugging comparison, prompt refinement.

See `docs/roadmap.md` and `docs/lean-canvas.md`.

## Safety notes

BugSlayer only runs `g++ -fsyntax-only` with a timeout and size limit; student code is never executed. If you deploy publicly, run the backend in a container with no secrets and limited resources, since `#include` directives can still read files on the server.

## License

MIT
