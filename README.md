# Interview AI

An AI-powered interview preparation platform. Upload your resume, paste a job description, add a short self-description, and get a tailored **interview report**: match score, likely technical and behavioral questions (with the interviewer's intent and how to answer), skill gaps, and a day-wise preparation plan. It can also generate an ATS-friendly, job-tailored **resume PDF**.

## Features

- **JWT authentication** - register, login, logout, protected routes; tokens live in HTTP cookies and are blacklisted on logout
- **Resume parsing** - upload a PDF (max 3 MB); text is extracted server-side with `pdf-parse`
- **AI interview report** - Google Gemini returns schema-validated JSON (Zod schema -> JSON schema) so output is always structured:
  - Match score (0-100) against the job description
  - Technical and behavioral questions with intention + answer strategy
  - Skill gaps with severity (low / medium / high)
  - Day-wise preparation plan
- **Tailored resume PDF** - Gemini generates HTML, Puppeteer renders it to an A4 PDF
- **Report history** - every report is stored per user and can be reopened later

## Tech Stack

| Layer | Tech |
| --- | --- |
| Frontend | React 19, Vite, React Router 7, Axios, SCSS |
| Backend | Node.js, Express 5 |
| Database | MongoDB + Mongoose |
| AI | Google Gemini (`@google/genai`) |
| Auth | JWT, bcryptjs, cookie-parser |
| Other | Multer (uploads), pdf-parse, Puppeteer, Zod |

## Project Structure

```
interview-ai/
├── Backend/
│   ├── server.js                 # entry point
│   └── src/
│       ├── app.js                # express app, CORS, routes
│       ├── config/database.js    # MongoDB connection
│       ├── controllers/          # auth + interview logic
│       ├── middlewares/          # JWT auth, file upload
│       ├── models/               # user, interviewReport, blacklist
│       ├── routes/               # /api/auth, /api/interview
│       └── services/ai.service.js# Gemini calls + Puppeteer PDF
└── Frontend/
    └── src/
        ├── app.routes.jsx
        ├── features/auth/        # login, register, context, hooks
        └── features/interview/   # home, report page, hooks, styles
```

## Getting Started

### Prerequisites

- Node.js 18+
- A MongoDB instance (local or [MongoDB Atlas](https://www.mongodb.com/atlas))
- A Google Gemini API key from [Google AI Studio](https://aistudio.google.com/apikey)

### 1. Clone

```bash
git clone https://github.com/<your-username>/interview-ai.git
cd interview-ai
```

### 2. Backend

```bash
cd Backend
npm install
cp .env.example .env     # then fill in your values
npm run dev              # runs on http://localhost:3000
```

### 3. Frontend

```bash
cd Frontend
npm install
npm run dev              # runs on http://localhost:5173
```

> The frontend calls the API at `http://localhost:3000` and the backend allows CORS from `http://localhost:5173`. Change both if you use different ports.

## Environment Variables

Defined in `Backend/.env` (see `Backend/.env.example`):

| Variable | Description |
| --- | --- |
| `MONGO_URI` | MongoDB connection string |
| `JWT_SECRET` | Secret used to sign auth tokens |
| `GOOGLE_GENAI_API_KEY` | Google Gemini API key |

## API Reference

Base URL: `http://localhost:3000/api`

### Auth

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| POST | `/auth/register` | Public | Register with `username`, `email`, `password` |
| POST | `/auth/login` | Public | Login with `email`, `password` |
| GET | `/auth/logout` | Public | Clear cookie and blacklist the token |
| GET | `/auth/get-me` | Private | Get the logged-in user |

### Interview

| Method | Endpoint | Access | Description |
| --- | --- | --- | --- |
| POST | `/interview` | Private | Generate a report (`multipart/form-data`: `resume` PDF, `selfDescription`, `jobDescription`) |
| GET | `/interview` | Private | List all of the user's reports |
| GET | `/interview/report/:interviewId` | Private | Get one report |
| POST | `/interview/resume/pdf/:interviewReportId` | Private | Generate a tailored resume PDF |

## Known Limitations & Future Improvements

- Move the hardcoded API base URL in the frontend to a `VITE_API_URL` env variable
- Add input validation (Zod) on request bodies and handle missing resume uploads
- Set cookie options (`httpOnly`, `secure`, `sameSite`) and add rate limiting on AI endpoints
- Scope the resume-PDF endpoint to the logged-in user's reports
- Reuse a single Puppeteer browser instance instead of launching one per request
- Add tests and a Docker setup

## Developer

**Love Patidar**
B.Tech, Electrical Engineering - MANIT Bhopal

- GitHub: [@your-github-username](https://github.com/your-github-username)
- LeetCode: [lovepatidar](https://leetcode.com/lovepatidar)
- Codeforces: [engrlovepatidar](https://codeforces.com/profile/engrlovepatidar)

## License

Released under the [MIT License](LICENSE).
