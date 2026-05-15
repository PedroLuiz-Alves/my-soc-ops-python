# Copilot Workspace Instructions

**Soc Ops** — Social Bingo game (Python 3.13+, FastAPI + Jinja2 + HTMX).

## ⚠️ MANDATORY: Pre-Commit Checklist

**Do NOT commit without:**
```bash
uv run ruff check .   # Lint (snake_case, imports, conventions)
uv run pytest         # Test (all 25 must pass)
uv run uvicorn app.main:app --reload  # Build & run locally
```
Then verify in browser at `http://localhost:8000`. Required: type hints, no unused imports, immutable Pydantic models.

---

## 🏗️ Architecture

**Stack**: FastAPI (routes) + Jinja2 + HTMX (partial updates) + SessionMiddleware (cookie-based sessions).

### Key Files
- `app/main.py` — FastAPI routes + HTMX endpoints
- `app/game_service.py` — GameSession (state machine)
- `app/game_logic.py` — Board generation, bingo detection
- `app/models.py` — BingoSquareData (frozen Pydantic), GameState enum
- `app/data.py` — Question bank (24 + 1 FREE_SPACE)
- `app/templates/` — Jinja2 + HTMX components

### Core Concepts
- **GameSession**: Holds `game_state` (START → PLAYING → BINGO), `board` (25 squares), `winning_line`
- **BingoSquareData**: Frozen model; modify via `model_copy(update={...})`
- **State Persistence**: Signed cookies → in-memory `_sessions` dict → survives refresh
- **Frontend**: HTMX declarative updates (no JavaScript), forms return HTML fragments

### Game Flow
1. `GET /` — Render home with session
2. `POST /start` — Generate 5×5 board (center is FREE)
3. `POST /click/{id}` — Toggle square, check 12 winning lines (5 rows + 5 cols + 2 diags)
4. `POST /dismiss-modal` — Reset modal, continue playing

---

## 📋 Commands

```bash
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000  # Dev
uv run pytest                    # Tests
uv run pytest tests/test_game_logic.py -v  # Single file, verbose
uv run ruff check . --fix        # Auto-fix lint
```

---

## 💡 Patterns

**Add API endpoint**: Route in `main.py` → return `TemplateResponse()` with context → create Jinja2 template with HTMX attrs (`hx-post="/endpoint"`, `hx-swap="innerHTML"`)

**Modify state**: Call method on `GameSession` instance → methods mutate internally → return to template for HTMX diff-render.

**Add questions**: Edit `app/data.py` QUESTIONS list. `generate_board()` samples 24 of them.

**Testing**: API tests use `TestClient`, logic tests import functions directly.

---

## 🎨 Styling & Tools

- **CSS**: Custom utilities in `app/static/css/app.css`. See [css-utilities.instructions.md](instructions/css-utilities.instructions.md)
- **Design**: See [frontend-design.instructions.md](instructions/frontend-design.instructions.md) (avoid AI slop)
- **Global Rules**: [general.instructions.md](instructions/general.instructions.md) (no Simple Browser, etc.)
- **Agents**: [.github/agents/](../agents/) — TDD, Pixel Jam, Quiz Master, etc.

---

## 🔧 Tips

- **HTMX debugging**: Check DevTools Network tab for request/response
- **No Simple Browser**: Use `$BROWSER http://localhost:8000` — HTMX needs full browser
- **Type hints mandatory** for all functions/vars
- **Immutable models**: Use `.model_copy(update={...})` to modify Pydantic
- **Winning lines cached** via `@functools.cache`

---

## 📚 Resources

- [README.md](../../README.md), [Workshop](../../workshop/), [API Docs](http://localhost:8000/docs)
