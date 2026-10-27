<div align="center">

# 🎬 Tamasha · API

### Every movie showing in Nairobi tonight, plus the events you host yourself.

**Django REST backend that keeps real-time cinema schedules in sync and powers a full event-management system.**

[![Frontend](https://img.shields.io/badge/Frontend_Repo-tamasha--web-0f172a?style=for-the-badge&logo=github)](https://github.com/JessyWaweru/tamasha-web)

![Python](https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white)
![Django](https://img.shields.io/badge/Django_5-092E20?logo=django&logoColor=white)
![DRF](https://img.shields.io/badge/DRF-A30000?logo=django&logoColor=white)
![Clerk](https://img.shields.io/badge/Clerk_JWT-6C47FF?logo=clerk&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white)
![cloudscraper](https://img.shields.io/badge/cloudscraper-F38020?logo=cloudflare&logoColor=white)

</div>

---

## 🍿 What it does

Nairobi's cinema listings are spread across a dozen theatre sites. Tamasha collects them into **one API**:

- 🕷️ **A data-sync pipeline** pulls now-showing movies and their full schedules (cinema → date → time → ticket link)
- 🎟️ **Event management**: users create, edit and manage their own events, with speakers, sponsors and attendees
- 🔐 **Clerk authentication**: the API verifies Clerk-issued JWTs against Clerk's JWKS keys, so there are no passwords to store

## 📸 Screenshots

| 🏠 Home | 🎬 Movies |
|:---:|:---:|
| ![Home](screenshots/desktophomepage1.png) | ![Movies](screenshots/desktopmovie1.png) |
| 🕐 **Showtimes by cinema** | 📅 **Events** |
| ![Movie details](screenshots/desktopmoviedetails1.png) | ![Events](screenshots/desktopevents1.png) |

## 🕷️ The data-sync pipeline

```bash
python manage.py scrape_movies
```

- Uses **cloudscraper** to get past Cloudflare's bot checks, with a polite delay between requests 🫖
- Upserts every movie as an `Event`, then rebuilds its `Showtime`s per `Cinema`
- Stores the real ticket checkout link for every showtime
- It's a plain management command, so a cron job or Render Cron can run it on a schedule

## 🧩 Data model

```
Event (movie or user event) ──< Showtime >── Cinema
  │                             date · time · ticket_link
  ├── Sponsor
  ├──< Speaker
  └──< Attendee
```

`GET /api/events/{id}/` returns the event **with its showtimes and cinemas nested inside**, so the frontend gets everything it needs in one request.

## 📡 API

| Method | Endpoint | What it does | Access |
|:---:|---|---|:---:|
| `GET` | `/api/events/` · `/api/events/{id}/` | Movies & events, with nested showtimes | 🌍 |
| `POST` `PUT` `DELETE` | `/api/events/…` | Create & manage events | 🔐 |
| `GET` `POST` … | `/api/speakers/` · `/api/sponsors/` · `/api/attendees/` | Event details | 🔐 |
| `GET` | `/api/movies/` | Live now-showing snapshot | 🌍 |

🔐 = send `Authorization: Bearer <Clerk session JWT>`

## 🚀 Run it locally

```bash
git clone https://github.com/JessyWaweru/tamasha-api.git
cd tamasha-api
python -m venv venv && source venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py scrape_movies     # 🍿 fill the database with real showtimes
python manage.py runserver         # → http://127.0.0.1:8000/api/
```

| Env var | Purpose |
|---|---|
| `CLERK_SECRET_KEY` | Clerk backend key (needed for authenticated routes) |
| `CLERK_JWKS_URL` | Your Clerk instance's JWKS endpoint |
| `DATABASE_URL` | Postgres in production (SQLite locally by default) |
| `SECRET_KEY` · `FRONTEND_URL` | Django secret, CORS origin |

Then start the [frontend](https://github.com/JessyWaweru/tamasha-web).

---

<div align="center">

**Built by Jessy Bryan Waweru**: actuary by training, full-stack engineer by obsession.

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?logo=linkedin&logoColor=white)](https://www.linkedin.com/in/jessy-waweru-b8051a288)
[![Email](https://img.shields.io/badge/Email-EA4335?logo=gmail&logoColor=white)](mailto:bryanjessywaweru@gmail.com)
[![GitHub](https://img.shields.io/badge/More_projects-181717?logo=github)](https://github.com/JessyWaweru)

</div>
