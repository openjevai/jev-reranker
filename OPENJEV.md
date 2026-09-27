# OpenJEV Support

This fork adds optional [OpenJEV](https://openjev.sh) support alongside the
original TypeSafe integration. TypeSafe remains the default; OpenJEV is a free
community gateway to the same Jev model built by [TypeSafe](https://typesafe.ai).

## What was added

- **`src/jev_reranker/reranker.py`** — `provider` parameter and `JEV_PROVIDER`
  env var on `JevReranker.__init__`. Provider-aware defaults for `api_key_env`,
  `model`, and `endpoint` when OpenJEV is selected.
- **`.env.sample`** — documented `OPENJEV_API_KEY`, `JEV_PROVIDER`, and OpenJEV
  defaults.
- **`README.md`** — OpenJEV note after the intro; options table updated.
- **`docs/spec.md`** — provider selection rule documented.
- **`tests/conftest.py`** — `OPENJEV_API_KEY` and `JEV_PROVIDER` removed from
  the environment in offline tests.

## Provider selection rule

1. Explicit `provider="openjev"` (or `JEV_PROVIDER=openjev` env) → OpenJEV.
2. Otherwise, if `TYPESAFE_API_KEY` is set → TypeSafe (unchanged default).
3. Otherwise, if only `OPENJEV_API_KEY` is set → OpenJEV.
4. Otherwise → TypeSafe (default, raises `ConfigurationError` at use time if
   no key is found).

When OpenJEV is selected:
- `api_key_env` defaults to `OPENJEV_API_KEY`
- `model` defaults to `openjev`
- `endpoint` defaults to `https://api.openjev.sh/v1/systemone`

All explicit arguments and `JEV_MODEL` / `TYPESAFE_ENDPOINT` env vars override
the provider defaults. Anyone with a TypeSafe key sees zero behaviour change.

## Configuration

```sh
# Option A — explicit provider
JEV_PROVIDER=openjev
OPENJEV_API_KEY=your-openjev-key

# Option B — auto-detect (leave TYPESAFE_API_KEY unset)
OPENJEV_API_KEY=your-openjev-key
```

Or in Python:

```python
JevReranker(provider="openjev", api_key="your-openjev-key")
```

## Verification

- A live POST to `https://api.openjev.sh/v1/systemone` with model `openjev`,
  state `ping`, one noul question — returned HTTP 200.
- No hardcoded `api.typesafe.ai` default remains that would override the
  provider-aware defaults; the TypeSafe endpoint is still the default when
  TypeSafe is selected.

## Upstream

Original project: https://github.com/hotchpotch/jev-reranker by @hotchpotch (MIT).
