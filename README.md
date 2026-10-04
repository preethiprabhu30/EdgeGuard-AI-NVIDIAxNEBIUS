# EdgeGuard AI — Autonomous Physical Safety & Inspection Agent

EdgeGuard AI is a hackathon-ready Physical AI MVP implementing:

**Sense → Understand → Decide → Act → Verify**

It includes:
- live USB/webcam capture
- mock/local demo mode
- NVIDIA NIM VLM client
- Nebius/OpenAI-compatible inference client
- safety-event JSON parsing
- autonomous policy engine
- alert + actuator abstraction
- verification loop
- FastAPI backend
- browser dashboard with live camera/event feed
- synthetic event generator
- policy evaluation script
- Docker support

## 1. Quick start

```bash
python -m venv .venv
# Windows:
.venv\Scripts\activate
# Linux/macOS:
source .venv/bin/activate

pip install -r requirements.txt
copy .env.example .env   # Windows
# cp .env.example .env   # Linux/macOS

uvicorn app.main:app --reload
```

Open:

`http://localhost:8000`

By default it starts in **mock mode** so you can test the full agent without a cloud model.

## 2. NVIDIA NIM mode

Run a compatible NVIDIA VLM NIM and set:

```env
EDGEGUARD_PROVIDER=nvidia
NVIDIA_BASE_URL=http://localhost:8001/v1
NVIDIA_API_KEY=not-used
NVIDIA_MODEL=<model-id>
```

NIM exposes an OpenAI-compatible `/v1/chat/completions` API, so the same client code can be reused.

## 3. Nebius endpoint mode

Deploy your model on Nebius Serverless Endpoints and set:

```env
EDGEGUARD_PROVIDER=nebius
NEBIUS_BASE_URL=https://<your-endpoint>/v1
NEBIUS_API_KEY=<token>
NEBIUS_MODEL=<model-name>
```

The application intentionally keeps the base URL/model configurable because endpoint names and hosted models can differ by account and deployment.

## 4. Demo

1. Put a webcam in view of the inspection area.
2. Start the app.
3. Press **Start Agent**.
4. Introduce a safe demo hazard, e.g. a red "SPILL" card, blocked pathway marker, object placed inside a marked exclusion zone, or simulated sensor warning.
5. Watch the dashboard show:
   - live frame
   - detected scene
   - hazard type
   - confidence
   - decision
   - action
   - verification
6. Keep the demo running for at least one minute.

Do **not** use dangerous real hazards for a demo.

## 5. API

- `GET /api/status`
- `POST /api/agent/start`
- `POST /api/agent/stop`
- `GET /api/events`
- `GET /api/frame`
- `POST /api/demo/hazard`
- `POST /api/demo/clear`

## 6. Architecture

```text
Camera / Sensors
       |
       v
  Perception
(NVIDIA/Nebius VLM)
       |
       v
Structured Scene JSON
       |
       v
 Policy / Decision Engine
       |
       v
Alerts / Robot Command
       |
       v
 Verification Capture
```

## 7. Synthetic data / policy evaluation

```bash
python scripts/generate_synthetic_data.py
python scripts/evaluate_policy.py
```

These scripts are intentionally stateless and container-friendly, so they can also be run as Nebius serverless jobs.

## 8. Docker

```bash
docker build -t edgeguard-ai .
docker run --rm -p 8000:8000 --device=/dev/video0:/dev/video0 --env-file .env edgeguard-ai
```

On Windows/macOS Docker camera passthrough varies; running the Python app directly is usually simpler for a webcam demo.

## 9. Repository checklist

Before submission:
- replace placeholder project links
- add your chosen NVIDIA model/model card
- add Nebius deployment screenshots/config
- add a 60–90 second hardware demo video
- add architecture diagram
- add LICENSE
- never commit API keys
