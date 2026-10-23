# Agent Instructions for CrewAI OSS

CrewAI is a Python based framework for building AI agents and agentic systems.
Follow these guidelines when contributing:

## Key Guidelines

1. Follow Python best practices and idiomatic patterns.
2. Maintain existing code structure and organization.
3. Write unit tests for new functionality focusing on behaivor and not
   implementation.
4. Document public APIs and complex logic.
5. Suggest changes to the `docs/` folder when appropriate
6. Follow software principles such as DRY and YAGNI.
7. Keep diffs as minimal as possible.

## Message Content

`LLMMessage.content` is `str | list[dict[str, Any]] | None`; the list form is
multimodal content parts. Never `str()` it — that puts a Python repr
(`[{'type': 'text', 'text': 'hi'}]`) in front of the model and into memory.
Collapse a message to text with the helper instead:

```python
from crewai.utilities.agent_utils import message_content_text

text = message_content_text(msg)  # "" for None; joined text for a parts list
```

Parts arrive from a model and are typed `dict[str, Any]`, so a `text` key that
is not a string is possible. `_content_parts_text` skips those blocks rather
than raising, and names a list with no usable text `[multimodal content]`.

## Changing Docs

1. Edit MDX under `docs/edge/en/*` and reference it from `docs/docs.json` if
   needed.
2. Do not modify files under `docs/v*/`. Those are frozen release snapshots
   managed by devtools.
3. Do not delete or rename files under `docs/images/` as frozen snapshots
   may reference them.
4. If you want to preview your changes locally, use `cd docs && mintlify dev`.
   To check for broken links, run `cd docs && mintlify broken-links`.
5. After editing English docs, sync translations to `ar`, `ko`, and `pt-BR`
   before finishing the task. Follow [DOCS_TRANSLATIONS.md](DOCS_TRANSLATIONS.md).

## Cursor Cloud specific instructions

- Install with uv 0.13.4 (the version CI pins) and `uv sync --all-groups --all-extras`. The sync is idempotent. `.python-version` selects CPython 3.13, so run project commands with `uv run`.
- CrewAI is a library and a CLI. Confirm the environment with `uv run crewai version` and an offline `Flow.kickoff()`. `crewai create flow <name>` writes a new project in the current directory.
- Pytest is configured with `--block-network` in `pyproject.toml`. `uv run ruff check` rewrites files because `[tool.ruff] fix = true`; use `uv run ruff check --no-fix` for a report.
- `uv run pre-commit install` exits when `core.hooksPath` is set. Leave that git setting in place on Cloud Agent VMs.
- Docs preview is separate from the library install: `cd docs && mintlify dev`.
