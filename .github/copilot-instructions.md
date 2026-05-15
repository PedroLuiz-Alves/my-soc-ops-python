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

## 🦸 Marvel Comic Book Design Guide

**Current Theme**: Bold pop-art aesthetic with comic book styling. This is the authoritative design reference.

### Color Palette
```css
--marvel-red: #e60000       /* Primary brand color */
--marvel-blue: #003da5      /* Secondary accent */
--marvel-gold: #ffd700      /* Premium accents */
--marvel-yellow: #ffeb3b    /* Marked squares & highlights */
--marvel-dark: #1a1a2e      /* Dark background */
```

**Usage**:
- **Red**: Headers, primary buttons, FREE space (bold statements)
- **Blue**: Secondary buttons, accents (contrast with red)
- **Gold/Yellow**: BINGO events, marked squares, celebration states
- **Dark Navy**: Background gradient, depth, immersion

### Typography

**Principle**: Heavy, dramatic, confident — no subtle fonts.

- **Titles**: `font-weight: 900`, `font-style: italic`, `letter-spacing: 0.05em`
- **Text Shadow**: `3px 3px 0px rgba(0,0,0,0.3)` for depth (not blur, sharp offset)
- **Comic Outline**: `-webkit-text-stroke: 1px rgba(0,0,0,0.3)` + `paint-order: stroke fill`
- **All-caps labels**: Buttons, headers, notifications (never mixed case)

### Visual Components

**Comic Card** (`.comic-card`):
- `border: 3px solid #000` (thick black border)
- `box-shadow: 4px 4px 0px rgba(0,0,0,0.5), 8px 8px 0px rgba(0,0,0,0.1)` (offset 3D)
- Subtle repeating gradient for texture (halftone effect)

**Buttons** (`.btn-marvel`):
- `font-weight: 900`, `font-size: 1.125rem`, `letter-spacing: 0.05em`
- `border: 3px solid #000`
- Active state: `translate(2px, 2px)` (pressed-down feel)
- Never use rounded corners (comic panels are square/angular)

**Board Squares**:
- Unmarked: `bg-white` with subtle hover
- Marked: `bg-marvel-yellow` (#ffeb3b) with bold checkmark
- Winning: `bg-marvel-gold` with `.animate-pop`
- FREE space: `bg-marvel-red` with bold white text

**Modals**:
- Golden background (`.comic-bubble` or `.comic-card` + gold)
- Large emoji (4-6rem for impact: 💥, 🎉, ⚡)
- `.animate-pop` on entry
- Taglines in ALL-CAPS with `.comic-title` styling

### Animations

**Pop Effect** (`.animate-pop`):
```css
@keyframes pop {
    0% { transform: scale(1); }
    50% { transform: scale(1.1); }
    100% { transform: scale(1); }
}
```
Used for: BINGO events, board interactions, modal entrance.

**Glow Effect** (`.animate-glow`):
```css
@keyframes glow {
    0%, 100% { box-shadow: 0 0 10px rgba(255, 215, 0, 0.5); }
    50% { box-shadow: 0 0 20px rgba(255, 215, 0, 0.8); }
}
```
Used for: Winning notification, celebration states, emphasis.

**No blur or fade**: Use crisp transitions, not soft animations (comic book aesthetic).

### Background & Depth

**Page Background**:
```css
background: linear-gradient(135deg, #1a1a2e 0%, #0f3460 100%);
```
Dark navy gradient with slight purple tint — immersive without distraction.

**Comic Grid** (`.comic-grid`):
```css
background-image: 
    linear-gradient(rgba(0,0,0,0.1) 1px, transparent 1px),
    linear-gradient(90deg, rgba(0,0,0,0.1) 1px, transparent 1px);
background-size: 40px 40px;
```
Subtle grid pattern adds comic panel feel without being busy.

### DO's and DON'Ts

✅ **DO**:
- Use thick 3px+ borders (bold outlines)
- All-caps for buttons, headers, notifications
- Sharp offset shadows (not blur)
- Primary colors: red, blue, yellow, gold
- Heavy font weights (600+, prefer 900)
- Emoji for visual interest (use sparingly, strategically)
- High contrast (dark background, bright text/elements)

❌ **DON'T**:
- Use thin lines or subtle borders
- Rounded corners on cards/buttons (unless exceptional reason)
- Soft shadows or glows (except `.animate-glow`)
- Pastel or muted colors (go bold or go home)
- Generic fonts (Arial is fine, but make it HEAVY)
- Blur effects or transparency fades
- Text below 14px for buttons (readability matters)
- Mixed case in UI labels

### Adding New Components

1. **Create reusable CSS class** in `app/static/css/app.css` (e.g., `.btn-marvel`, `.comic-card`)
2. **Apply to template** (e.g., `<div class="comic-card p-6">`)
3. **Test in browser** at `http://localhost:8000` — visual design matters
4. **Use existing colors** — don't add new shades. Extend palette intentionally.

Example: New alert banner
```html
<div class="bg-marvel-red text-white font-bold py-3 px-4 border-b-4 border-black">
    🚨 NEW ALERT TEXT HERE
</div>
```

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
