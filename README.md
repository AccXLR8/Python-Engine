# ChainReaction Py Engine

This folder contains a pure-Python implementation of the Chain Reaction engine using NumPy
arrays for efficient board math. It mirrors the Rust engine's public API (`new_game`,
`validate_move`, `apply_move`, `replay`) while remaining a standalone package (`chainreaction-py`).

## Project Structure

```
python-engine/
├── pyproject.toml               # Packaging metadata, Python 3.10+, NumPy dependency
├── README.md                    # This file (architecture overview)
├── DOCUMENTATION.md             # Usage guide, API details, run instructions
└── python_engine/
    ├── __init__.py              # Re-exports the public API
    ├── board.py                 # NumPy-backed board storage (owners/orb counts)
    ├── engine.py                # Game orchestration functions
    ├── errors.py                # Exception hierarchy mirroring Rust enums
    ├── game_state.py            # Data classes for GameState / GameStatus / PlayerState
    ├── move.py                  # Simple move dataclass
    ├── reaction.py              # Reaction timeline structs
    └── result.py                # MoveResult / GameResult containers
```

## Architecture Notes

- **NumPy backing**: The board keeps two arrays (`owners`, `orbs`) to provide constant-time
  access and vectorized counting when evaluating eliminations.
- **Deterministic rules**: Every move clones the state, drops an orb, and runs deterministic
  reaction waves until the board stabilizes or one eligible player remains.
- **Safe late games**: Exploding cells are completely cleared, victory stops winner-only
  reactions immediately, and cycle detection plus an explosion limit prevent hangs.
- **Timeline fidelity**: `ReactionTimeline` captures the placement, each wave's explosions,
  and per-orb transfers, allowing frontends to animate or audit reactions exactly.
- **Serialization ready**: Each data class offers simple Python primitives (ints/lists)
  making it easy to `json.dumps()` a `GameState` or `MoveResult`.

See `DOCUMENTATION.md` for installation and usage instructions.
