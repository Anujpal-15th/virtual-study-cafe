# Virtual Study Cafe

**Study together online: live rooms with video, chat and a shared timer, plus solo study tools, progress tracking and an AI study assistant.**

![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django_4.2-092E20?logo=django&logoColor=white)
![Channels](https://img.shields.io/badge/Django_Channels-WebSockets-44B78B)
![WebRTC](https://img.shields.io/badge/WebRTC-video_%26_audio-333333?logo=webrtc&logoColor=white)
![Gemini](https://img.shields.io/badge/Google_Gemini-AI-8E75B2?logo=googlegemini&logoColor=white)

> Built during the **Edunet Foundation × EY × AICTE Full Stack Web Development** program, where I learned Django.

---

## Features

**Study rooms**
- Create public or private rooms and browse the ones that are active
- Live video and audio between members over WebRTC, with signalling through WebSockets
- Real-time chat with typing indicators
- A study timer that stays in sync for everyone in the room
- Empty rooms are removed automatically: a background job runs every 5 minutes

**Solo study**
- A personal study room with a focus timer
- Task list for organising study work

**Progress tracking**
- Logged study sessions and total study time
- Study goals and a study schedule
- Achievements unlocked at milestones
- Leaderboard

**AI study assistant**
- Chatbot for study questions, powered by Google Gemini (`gemini-2.5-flash`)

**Accounts**
- Sign up, log in, email verification and password reset
- Profiles with avatars, plus in-app notifications

---

## How it works

```
Browser ──HTTP──► Django views ──► SQLite
   │
   ├──WebSocket──► Channels consumer (Daphne, ASGI)
   │                 chat · typing · timer sync · WebRTC signalling
   │
   └──WebRTC (peer to peer)──► other members' browsers   (video + audio)

APScheduler ── every 5 min ──► remove inactive rooms
Chatbot view ──► Google Gemini API
```

The project is split into Django apps:

| App | Responsibility |
|---|---|
| `accounts` | Auth, email verification, profiles, preferences |
| `rooms` | Study rooms, memberships, WebSocket consumer, cleanup scheduler |
| `solo` | Solo study room and tasks |
| `tracker` | Study sessions, goals, schedules, achievements, leaderboard |
| `chatbot` | Gemini-powered study assistant |
| `notifications` | In-app notifications |

---

## Tech stack

| Layer | Tools |
|---|---|
| Backend | Django 4.2, Django Channels 4, Daphne (ASGI) |
| Real-time | WebSockets, WebRTC |
| Background jobs | APScheduler |
| AI | Google Gen AI SDK (Gemini) |
| Database | SQLite |
| Other | Pillow (avatars), python-dotenv |

---

## Getting started

**Requirements:** Python 3.10+

```bash
git clone https://github.com/Anujpal-15th/virtual-study-cafe.git
cd virtual-study-cafe

python -m venv venv
# Windows: venv\Scripts\activate
# macOS/Linux: source venv/bin/activate

pip install -r requirements.txt
cp .env.example .env          # then fill in the values below

python manage.py migrate
python manage.py create_achievements
python manage.py createsuperuser   # optional, for /admin
python manage.py runserver
```

Open http://127.0.0.1:8000.

### Environment variables (`.env`)

| Variable | Required | Notes |
|---|---|---|
| `SECRET_KEY` | Yes | Any long random string |
| `DEBUG` | No | `True` for local development |
| `ALLOWED_HOSTS` | No | Defaults to `localhost,127.0.0.1` |
| `GEMINI_API_KEY` | For the chatbot | Free key from [Google AI Studio](https://aistudio.google.com/app/apikey). Without it, the rest of the app still works. |
| `EMAIL_HOST_USER`, `EMAIL_HOST_PASSWORD` | For real emails | Gmail address and an App Password. Without them, emails are printed to the console in debug mode. |

> Video calls need camera and microphone permission, and browsers only allow them on `localhost` or HTTPS.

---

## Notes and limitations

- Real-time messaging uses Channels' in-memory layer, so it runs as a single server process. Multi-server deployment would need the Redis channel layer (`channels-redis` is already in `requirements.txt`).
- WebRTC connects browsers directly, with no TURN server, so some strict networks may block video.
- SQLite is used for development.

---

## What I learned

- Building a multi-app Django project with models, forms, signals and management commands
- Handling WebSockets with Django Channels and async consumers
- Using WebRTC for peer-to-peer video and audio, with the server only relaying signalling
- Running background jobs with APScheduler
- Calling an LLM API from a web app

---

Made by [Anuj Pal](https://github.com/Anujpal-15th)
