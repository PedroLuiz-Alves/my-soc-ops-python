
---

# 🎉 Soc Ops — Social Bingo for Real People

**Break the ice at your next mixer.** Find people who match your bingo board questions, get 5 in a row, and win! 
A fast, fun way to make meaningful connections at any event.

**Built with modern web tech**: Python • FastAPI • HTMX • Jinja2  
**Perfect for**: Team building, corporate mixers, conference icebreakers, networking events

---

## ⚡ In 30 Seconds

```bash
git clone <repo>
cd my-soc-ops-python
uv sync && uv run uvicorn app.main:app --reload
```

Then open **`http://localhost:8000`** and start playing!

---

## 🎮 How It Works

| Step | What You Do |
|------|-------------|
| � **Start** | Generate a randomized 5×5 bingo board with social questions |
| 👥 **Mingle** | Move around the room and chat with people |
| ✅ **Mark** | When someone matches a square (e.g., "Has traveled to 3+ countries"), click it! |
| 🎊 **Win** | Get 5 in a row (horizontal, vertical, or diagonal) and shout **BINGO!** |

**Questions are randomized every game**, so no two events are the same.

---

## ✨ Key Features

- **Zero Setup** — Just show up with a device. No accounts, no login.
- **Responsive Design** — Works on phone, tablet, or desktop.
- **Quick Games** — Average game time: 5–10 minutes.
- **Customizable Questions** — Edit the question bank in `app/data.py` for your event.
- **State Persistence** — Refresh the page? Your game is still there.

---

## 🏗️ Architecture

**Frontend** (HTMX + Jinja2)
- Lightweight template-based UI  
- Dynamic partial updates (no page reloads)

**Backend** (FastAPI)
- Modern async Python framework  
- Session-based game state  
- RESTful API endpoints for all game actions

**Game Logic**
- Deterministic bingo detection (5 rows + 5 cols + 2 diagonals)  
- Immutable Pydantic models for state safety

---

## 🚀 Learn by Building

This project is a **hands-on lab** for mastering modern web development with AI coding assistants. Learn:

- **Part 00**: Project overview & mandatory checklist
- **Part 01**: Context engineering & environment setup  
- **Part 02**: Design-first frontend with HTMX
- **Part 03**: Custom AI agent development (Quiz Master)
- **Part 04**: Multi-agent orchestration & async workflows

[**Start the Lab →**](https://copilot-dev-days.github.io/agent-lab-python/docs/step.html?step=00-overview)


---

## 🛠️ Dev Commands

```bash
# Install & run
uv sync
uv run uvicorn app.main:app --reload --host 0.0.0.0 --port 8000

# Quality checks (required before commit)
uv run ruff check .   # Linting
uv run pytest         # Tests

# Auto-fix code
uv run ruff check . --fix
```

---

## 📂 Project Structure

```
app/
 main.py           # FastAPI routes + HTMX endpoints
 game_service.py   # GameSession state machine
 game_logic.py     # Board generation & bingo detection
 models.py         # Pydantic models (frozen/immutable)
 data.py           # Question bank (25 questions)
 templates/        # Jinja2 templates + HTMX
 static/           # CSS & static assets

tests/                # Pytest suite (all 25+ tests must pass)
workshop/             # Standalone lab guides for learning
```

---

## 💡 Made For

- **Developers** learning FastAPI, HTMX, and modern web patterns
- **Event Organizers** who want a fun, fast icebreaker tool
- **Teams** practicing TDD, CI/CD, and collaborative coding

---

## 📖 Resources

- [Lab Guide](https://copilot-dev-days.github.io/agent-lab-python/) — Step-by-step learning path
- [API Docs](http://localhost:8000/docs) — Swagger UI (live when app is running)
- [Contributing](CONTRIBUTING.md) — How to extend or improve
- [Security](SECURITY.md) — Safety & best practices

---

## 🤝 Contributing

Found a bug? Want to add new questions or features? 
[See CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

---

**Built with ❤️ for developers who learn by doing.**
