# AI Experts Assignment (Python)

OAuth2-based HTTP client with token refresh handling.

## Running Tests

### Locally

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
python -m pytest -v
```

### Docker

```bash
docker build -t ai-assignment .
docker run --rm ai-assignment
```
