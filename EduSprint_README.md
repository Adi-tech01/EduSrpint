# EduSprint — AI-Powered Adaptive Learning Companion
**Problem statement:** Smart Education
**Event:** Digital Campus on Google Cloud Hack Sprint — Shri Shankaracharya Technical Campus (GeeksforGeeks)

**Live demo:** https://claude.ai/artifact/WiKRYsDWc33RmN4rWYPdB1

---

## 1. Problem

On a shared campus, every student learns at a different pace, but classroom pacing, static PDFs and one-size-fits-all quizzes treat everyone the same. Weaker students fall behind silently; faster students get bored. Teachers don't have time to write personalized material or track per-student mastery across a whole cohort.

## 2. Solution

EduSprint is a single web app that gives every student:

- **An AI Tutor** that explains any concept on demand, in plain language, with step-by-step working — like a 24/7 teaching assistant.
- **An Adaptive Quiz Engine** that generates fresh multiple-choice questions on any subject/topic and **raises or lowers difficulty automatically** based on the student's rolling accuracy, round to round.
- **A gamified progress system** — XP, levels, streaks and unlockable badges — to keep students coming back.
- **A live campus leaderboard**, so mentors and peers can see engagement across the cohort from the same underlying data, with zero extra teacher effort.

Everything above is **fully functional in the live demo**, not mocked — the AI Tutor and quiz generator make real AI calls at runtime, and progress is saved to a real-time shared datastore.

## 3. Key features

| Feature | What it does |
|---|---|
| AI Tutor chat | Subject-aware, streaming explanations for any doubt |
| Adaptive quiz | 5 AI-generated MCQs per round; difficulty adapts using accuracy (≥80% → harder, ≤40% → easier) |
| XP & leveling | Non-linear XP curve, animated progress bar |
| Streaks | Daily-use streak counter to build a study habit |
| Badges | 6 unlockable achievements (first quiz, 3-day streak, perfect round, multi-subject practice, etc.) |
| Subject mastery | Per-subject accuracy tracking, visualized as progress bars |
| Leaderboard | Real-time ranked view of top learners on the deployment |
| Light/dark theme | Full theme-aware UI, mobile-responsive |

## 4. Tech stack (demo build)

- **Frontend:** Single self-contained HTML/CSS/JS page, Tailwind CSS, Google Fonts — no build step, loads instantly, works on mobile and desktop.
- **AI generation:** On-demand large-language-model calls for both the tutor's answers and the quiz question generator (structured JSON output).
- **Data layer:** Real-time document datastore for per-student progress (XP, streaks, subject stats, badges) and for the shared leaderboard collection.
- **Auth:** Session-scoped user identity, with a local fallback so the app degrades gracefully offline.

## 5. Production architecture on Google Cloud Platform

The demo's AI and data calls map 1:1 onto managed GCP services for a production rollout:

- **Vertex AI (Gemini models)** — tutor explanations and adaptive quiz generation.
- **Firestore** — real-time student progress, streaks, badges, and leaderboard (same document shape as the demo).
- **Cloud Run** — stateless, autoscaling hosting for the web app and any API layer.
- **Firebase Auth / Identity Platform** — student and mentor login, replacing the demo's session id.
- **BigQuery + Looker Studio** — cohort-level mastery analytics and dashboards for teachers.
- **Cloud Storage + Cloud CDN** — study material uploads and static asset delivery; foundation for future RAG-based tutoring over uploaded notes/past papers.

```
Student device
     │
     ▼
Cloud Run (EduSprint web app)
     │            │
     ▼            ▼
Vertex AI      Firestore  ──► BigQuery ──► Looker Studio
(Gemini)       (progress,        (analytics for teachers)
               leaderboard)
     ▲
     │
Cloud Storage (study material, future RAG context)
```

## 6. Adaptive difficulty algorithm (innovation)

Instead of adjusting difficulty only between whole quizzes, EduSprint recalculates a difficulty signal after **every 5-question round**:

- Accuracy ≥ 80% → next round moves up a difficulty tier (easy → medium → hard)
- Accuracy ≤ 40% → next round moves down a tier
- Otherwise → difficulty holds steady

This keeps each student in their optimal "challenge zone" without any teacher configuration.

## 7. Judging criteria alignment

- **Functionality (40%):** AI Tutor, adaptive quiz, XP/streak/badges, and leaderboard are all live and working end-to-end.
- **User Experience (20%):** Tabbed single-page flow, instant answer feedback, animated progress, fully responsive with dark mode.
- **Technical Complexity (20%):** Real-time generative AI calls with structured JSON output, adaptive-difficulty logic, real-time shared datastore, documented GCP production path (Vertex AI, Firestore, Cloud Run, BigQuery).
- **Innovation (10%):** Per-round (not per-quiz) adaptive difficulty; teacher-facing mastery analytics emerge for free from the same student-facing data model.
- **Presentation (10%):** In-app "Architecture" tab plus this README walk through problem, solution, stack and architecture.

## 8. How to try it

1. Open the live demo link above.
2. **Dashboard** — see XP, level, streak and badges (starts empty).
3. **AI Tutor** — pick a subject, ask a doubt, get a step-by-step answer.
4. **Adaptive Quiz** — pick a subject/topic, generate 5 questions, answer them; watch difficulty adjust on the next round.
5. **Leaderboard** — see how you rank against other students who've opened the app.
6. **Architecture** — problem statement, GCP production plan, and judging-criteria mapping for reviewers.

## 9. Future roadmap

- Upload lecture notes/past papers → RAG-grounded tutor answers (Cloud Storage + Vertex AI Search)
- Teacher dashboard with class-wide weak-topic heatmaps (BigQuery + Looker Studio)
- Voice-based tutoring for accessibility
- Spaced-repetition scheduling for long-term retention
