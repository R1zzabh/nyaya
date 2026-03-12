# AGENTS.md - NyayaAI Agent Guidance

This repository is a mono-root with the main codebase in `nyaya-ai/`.
Set your working directory to `nyaya-ai/` for most commands.

Cursor/Copilot rules:
- No `.cursor/rules/`, `.cursorrules`, or `.github/copilot-instructions.md` found.

Other agent guidance:
- Follow the project notes in `nyaya-ai/docs/notes/CLAUDE.md`.
- Key items from that file are summarized below.

## Quick Reference (Paths)
- App code: `nyaya-ai/src/`
- Tests: `nyaya-ai/tests/`
- Scripts: `nyaya-ai/scripts/`
- Config: `nyaya-ai/.env` (create from `.env.template`)
- Docker: `nyaya-ai/docker-compose.yml`

## Build / Lint / Test
There is no dedicated build step for this Python repo.

### Setup
```bash
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### Infra (Database + Services)
```bash
docker-compose up -d
docker-compose ps
docker-compose down
```

### Tests
```bash
pytest tests/
pytest tests/ -v
```

Single test examples:
```bash
pytest tests/test_pipeline.py::TestSettings::test_settings_load
pytest tests/test_pipeline.py::TestPDFExtractor::test_clean_text
pytest tests/test_pipeline.py -k "validate" -v
```

### Lint / Type Check
No linter/formatter is configured in this repo.
Optional type check (if you want signal from the pinned tool):
```bash
mypy src
```

## Common Scripts
Run from `nyaya-ai/`:
```bash
python scripts/01_download_sample.py
python scripts/03_run_extraction.py
python scripts/04_validate_quality.py
python scripts/05_standardize_courts.py
python scripts/08_download_diverse.py
python scripts/11_smart_extract_to_10k.py
python scripts/13_final_validation.py
```

Database init:
```bash
python -c "from src.data_pipeline.loaders.db_loader import DatabaseLoader; DatabaseLoader().init_db()"
```

## Code Style Guidelines

### Formatting
- Use 4 spaces for indentation.
- Prefer double quotes for strings (matches existing code).
- Use module, class, and function docstrings with triple quotes for public APIs.
- Keep lines reasonably short (PEP 8 style); no enforced formatter.

### Imports
- Order: standard library, third-party, then local `src.*` imports.
- Group with a blank line between sections.
- Use explicit imports (avoid wildcard imports).

### Typing
- Use type hints for public functions and class methods.
- Prefer `Optional[T]` for nullable values.
- Use Pydantic models for structured data (`src/database/schemas.py`).
- Validate constraints with Pydantic `Field` and `field_validator`.

### Naming
- `snake_case` for variables/functions.
- `PascalCase` for classes.
- `UPPER_SNAKE_CASE` for constants.
- Prefer descriptive names that match domain terms (case_id, court, verdict).

### Logging
- Use `loguru.logger` for logging, not `print`.
- Log structured fields with keyword args (e.g., `logger.info("...", case_id=...)`).
- Keep error logs actionable and include the failing `case_id` or file path.

### Error Handling
- Wrap external I/O (files, APIs, DB) in `try/except`.
- Log errors and return safe defaults rather than raising in pipeline loops.
- Use `ValidationResult` to track errors/warnings instead of throwing.

### Data Pipeline Conventions
- `PDFExtractor.extract_text()` returns a **dict**, not a string. Check `success` and `text`.
- Text truncation logic in metadata extraction keeps first 15K and last 5K chars.
- Confidence scores must be in range 0.0-1.0 for every extracted field.

### Database Conventions
- Use `DatabaseLoader.load_case(...)` with the full signature:
  `metadata`, `full_text`, `validation`, `extraction_model`, `source_file`, `page_count`.
- Detect duplicates using `source_file_path` (not `pdf_path`).
- Always `commit()` or `rollback()` and close sessions in `finally` blocks.

### Tests
- Use `pytest` and keep tests deterministic.
- Use `pytest.approx` for float comparisons.
- Keep tests in `tests/` and mirror module names when possible.

## Repo Conventions and Safety
- Do not commit `.env` or data dumps; use `.env.template` for keys.
- Large data/log files live under `data/` and `*.log`; avoid editing them.
- Use `docker-compose` for database services; avoid hardcoded local DB tweaks.

## Helpful Queries
```bash
python -c "from src.data_pipeline.loaders.db_loader import DatabaseLoader; import json; db = DatabaseLoader(); print(json.dumps(db.get_database_stats(), indent=2))"
```

## Notes for Agents
- Read `nyaya-ai/CLAUDE.md` before major changes for pipeline rules.
- The primary workflow is download -> extract -> validate -> load.
- Be careful with LLM API costs; keep batch sizes small unless asked.
