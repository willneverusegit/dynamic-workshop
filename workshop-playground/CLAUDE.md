# CLAUDE.md — Workshop Playground

## Project

Demo repository for Claude Code workshop hands-on exercises and security audit demos.

## Conventions

- **Language**: English for code, variable names, comments. German for communication with participants.
- **Python version**: 3.10+
- **Test framework**: pytest
- **Error handling**: Prefer explicit error handling — no bare `except:` clauses. Always catch specific exceptions and provide meaningful error messages.
- **Logging**: Use the built-in `logging` module; do not use `print()` for operational output.

## Structure

```
workshop-playground/
├── access_control.py       # Main CLI application
├── test_access_control.py  # pytest test suite
├── requirements.txt        # Dependencies
├── users.json              # User database (auto-created)
└── logs/                   # Access log directory (auto-created)
```

## Running the App

```bash
python access_control.py --help
python access_control.py add alice
python access_control.py check alice
python access_control.py remove alice
python access_control.py backup users_backup.json
```

## Running Tests

```bash
pip install -r requirements.txt
pytest -v
```

## Workshop Notes

This repo contains **intentional vulnerabilities** for Block 3.3 Security Demo.
Do NOT use this code in production.
