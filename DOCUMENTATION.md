# ChainReaction Py Engine – Documentation

This document explains how to build, run, and integrate the pure-Python Chain Reaction
engine contained in `python-engine/`.

## 1. Prerequisites

- Python 3.10+
- `pip` and a virtual environment (recommended)

## 2. Installation

From the repository root:

```bash
cd python-engine
python -m venv .venv
source .venv/bin/activate  # Windows: .venv\Scripts\activate
pip install -e .
```

This installs the package `chainreaction-py` in editable mode along with its dependency
(NumPy). You can now import `chainreaction_py` from any script/notebook.

## 3. Quick Start

```python
from chainreaction_py import new_game, apply_move, validate_move, Move

state = new_game(width=6, height=8, players=[0, 1])
move = Move(row=2, col=3)
validate_move(state, player=0, mv=move)
result = apply_move(state, player=0, mv=move)
print("Winner:", result.outcome.winner)
print("Timeline steps:", len(result.timeline.steps))
state = result.final_state  # feed into the next turn
```

## 4. Module Overview

| Module | Responsibility |
| --- | --- |
| `python_engine.board` | NumPy-backed grid storage (owners/orb counts, neighbor lookup). |
| `python_engine.game_state` | Data classes (`GameState`, `GameStatus`, `PlayerState`) with cloning helpers. |
| `python_engine.engine` | Implements `new_game`, `validate_move`, `apply_move`, `replay`, and reaction logic. |
| `python_engine.reaction` | Timeline structs (`ReactionTimeline`, `ReactionStep`, etc.). |
| `python_engine.result` | `MoveResult` and `GameResult` containers. |
| `python_engine.errors` | Exception hierarchy matching the Rust enums. |

## 5. Architecture Highlights

- **State cloning**: `GameState.copy()` deep-copies the board (NumPy arrays) and player list
  to ensure immutability between calls, matching the Rust semantics.
- **Reaction simulation**: `_simulate_reactions` mirrors the Rust wave-based algorithm,
  queuing unstable cells per wave and recording explosions/transfers for each step.
- **Outcome detection**: `_evaluate_result` derives `GameResult` after each move, updating
  `GameStatus` (active vs. won).

## 6. Testing

You can add pytest suites under `python-engine/tests/` (not included by default). After
installing with `pip install -e .[dev]`, run:

```bash
pytest
```

## 7. Serialization

Every data class exposes primitives only (ints, lists, dicts), so it is straightforward to
emit JSON:

```python
import json
payload = json.dumps(result.final_state.to_dict())
```

## 8. Extending

- Integrate directly into Python services (FastAPI, Django, etc.) by importing the API.  
- For performance-sensitive workloads, you can experiment with Numba or Cython on top of
  the NumPy-backed arrays without changing the public API.  
- Keep this package side by side with the Rust/PyO3 engine to compare correctness and
  potentially use it for tests, simulations, or fallbacks.
