# Bridgio

Sign language to text and speech, in the browser. It reads 16 signs from a webcam.

## What it does

- Streams webcam frames (10 per second) to a classifier and shows the predicted sign as text.
- Reads the result aloud with the browser's Web Speech API.
- Keeps a translation history, shows connection status, and has a light/dark theme.
- Recognises 16 signs: BUT, DO, DOWN, HEART, HELP, I LOVE YOU, KISS, ME, OK, OR, PEACE, PRAY, SAME, STOP, THAT, UP.

## How it works

Three services, one hop each:

```
React frontend --Socket.IO--> Node backend --WebSocket--> Python ML server
```

- **Frontend** grabs a 640x480 JPEG from the camera and emits it as a `frame` event.
- **Backend** relays the frame to the ML server and sends the answer back as a `prediction` event.
- **ML server** decodes the frame, pulls hand landmarks with MediaPipe (126 features: 21 landmarks x 3 coordinates x 2 hands) and classifies them with a small fully connected PyTorch network.

The model was trained on landmark samples that the team recorded ourselves (`ML/collect_data.py`, `ML/train.py`, data in `ML/dataset/`). It classifies single frames, so it knows static signs only.

## Stack

- Frontend: React 19, TypeScript, Vite, Socket.IO client
- Backend: Node.js, Express, Socket.IO, ws
- ML: Python 3.13+, FastAPI, MediaPipe, PyTorch, OpenCV, uv

## Run it locally

Needs Node.js, pnpm, Python 3.13+, uv and a webcam. Three terminals:

```bash
# ML server (port 8000)
cd ML
uv sync
uv run python app.py

# Backend (port 3000)
cd backend
pnpm install
pnpm dev

# Frontend (port 5173)
cd frontend
pnpm install
pnpm dev
```

Open http://localhost:5173 and allow camera access. `start.sh` (or `start.ps1` on Windows) starts all three.

The ports and URLs are hardcoded in the code, not read from env vars (the backend honours `PORT`).

## Notes

Won first prize at the MecLabs Hackathon (2025). A team project, with the dataset recorded by all of us. Contributors: [Hashim](https://github.com/justhashim), [Khansa Shahan](https://github.com/khanzaaa07), [Anna Catherene](https://github.com/cathereneanna), [Jeeson Paul](https://github.com/Jeeson-Paul).
It is a prototype: 16 signs, single frames, no sentence-level translation.
