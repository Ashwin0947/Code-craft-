# Code-craft-
project_root/
├── app/
│   ├── __init__.py
│   ├── main.py
│   ├── ai_engine.py
│   └── utils.py
├── tests/
│   └── test_ai_engine.py
├── requirements.txt
├── Dockerfile
├── README.md
└── .github/
    └── workflows/
        └── ci.yml

# app/main.py
from fastapi import FastAPI, UploadFile
from app.ai_engine import analyze_code

app = FastAPI()

@app.post("/analyze")
async def analyze(file: UploadFile):
    content = await file.read()
    result = analyze_code(content.decode())
    return {"suggestions": result}


# app/ai_engine.py
def analyze_code(code: str) -> list:
    suggestions = []
    if "def" in code and ":" not in code:
        suggestions.append("Add return type and proper colon to function definition")
    if "== None" in code:
        suggestions.append("Use 'is None' instead of '== None'")
    return suggestions


# app/utils.py
def clean_code(code: str) -> str:
    return code.strip()


# tests/test_ai_engine.py
from app.ai_engine import analyze_code

def test_analyze_code():
    code = "def add(x, y)\n    return x+y"
    suggestions = analyze_code(code)
    assert "Add return type" in suggestions[0]


# requirements.txt
fastapi
uvicorn
pytest


# Dockerfile
FROM python:3.10-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]


# .github/workflows/ci.yml
name: Python CI

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'
      - name: Install dependencies
        run: pip install -r requirements.txt
      - name: Run tests
        run: pytest


# README.md (truncated preview)
# Code Crafter AI
Your personal AI coding companion — write smarter, debug faster, and build cleaner code.

## Features
- AI code suggestions
- Bug detection
- CLI and API

## Run
```bash
uvicorn app.main:app --reload
```

## Test
```bash
pytest
