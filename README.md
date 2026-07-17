# 📈 Signalist Stock Tracker

[![Next.js 15](https://img.shields.io/badge/Next.js-15-black?style=flat-square&logo=next.js)](https://nextjs.org/)
[![React 19](https://img.shields.io/badge/React-19-blue?style=flat-square&logo=react)](https://react.dev/)
[![Inngest](https://img.shields.io/badge/Inngest-Event--Driven-purple?style=flat-square)](https://www.inngest.com/)
[![Google Gemini](https://img.shields.io/badge/AI-Google%20Gemini-orange?style=flat-square&logo=google-gemini)](https://ai.google.dev/)

An AI-powered, event-driven stock market application delivering real-time financial insights, automated user alerts, and personalized market sentiment digests. Built with Next.js 15, Inngest, and Google Gemini, Signalist offers a streamlined command center for modern investors.

---

## 🌟 Key Features

### 👤 For Investors
* **Real-Time Financial Charts:** Interactive and high-fidelity technical analysis charts powered by the embedded **TradingView Widget**.
* **Instant Market Data:** Direct integration with the **Finnhub API** to fetch live quotes, basic financials, and company profiles.
* **Custom Watchlists:** Build and manage a personalized selection of active tickers with a responsive search-and-add tool.
* **Smart Price Alerts:** Set threshold-based alerts (e.g., alert if price falls below \$150) and receive automated email dispatches when those thresholds are crossed.
* **AI-Driven Market Digests:** Receive customized, email-delivered market digests summarizing your watchlists. Features natural language market sentiment analysis synthesized by **Google Gemini**.

### 🔑 For Administrators (T.B.A)
* **Admin Command Center:** Manage global trackable assets and post platform-wide announcements.
* **User Monitoring & Metrics:** Track overall platform registrations and examine user activity dashboards.

---

## 🛠️ Tech Stack & Tooling

Signalist is engineered for rapid execution, absolute type-safety, and elegant scaling:

* **Framework:** [Next.js 15](https://nextjs.org/) (App Router) utilizing React 19 features
* **Database:** [MongoDB](https://www.mongodb.com/) via [Mongoose ODM](https://mongoosejs.com/) for reliable document schemas
* **Authentication:** [Better Auth](https://www.better-auth.com/) — lightweight, modern, and ultra-secure session management
* **Styling & UI:** [Tailwind CSS](https://tailwindcss.com/) & [Shadcn UI](https://ui.shadcn.com/) (primitives using Radix UI and Lucide Icons)
* **Workflow & CRONs:** [Inngest](https://www.inngest.com/) for zero-infrastructure background queuing and scheduled jobs
* **Generative AI:** [Google Gemini API](https://ai.google.dev/) for generating contextual digest summaries
* **Mail Dispatch:** [Nodemailer](https://nodemailer.com/) for handling HTML transactions and daily email reports

---

## ⚙️ Core Architecture

The backend operates on a decoupled, event-driven model using **Inngest** to process CPU-intensive or scheduled tasks outside the standard request-response lifecycle.

```
                  ┌──────────────────────────────┐
                  │   User Action / CRON Trigger │
                  └──────────────┬───────────────┘
                                 │
                                 ▼
                     ┌───────────────────────┐
                     │ Inngest Event Stream  │
                     └───────────┬───────────┘
                                 │
         ┌───────────────────────┼───────────────────────┐
         ▼                       ▼                       ▼
┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐
│  Price Watchers  │    │ AI Digest Runner │    │ Sentiment Scrape │
└────────┬─────────┘    └────────┬─────────┘    └────────┬─────────┘
         │                       │                       │
         ▼                       ▼                       ▼
┌──────────────────┐    ┌──────────────────┐    ┌──────────────────┐
│ Check DB Alerts  │    │ Query Finnhub    │    │ Call Gemini API  │
└────────┬─────────┘    └────────┬─────────┘    └────────┬─────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                                 ▼
                     ┌───────────────────────┐
                     │  Nodemailer Dispatch  │
                     └───────────────────────┘
```

### Event Flows
1. **Daily Sentiment Digest:** At a scheduled time, Inngest triggers a CRON job. It pulls active user watchlists, collects up-to-date quotes from Finnhub, formats the raw data into an optimized prompt, calls **Google Gemini** for synthesis, and dispatches a rich-text HTML email via **Nodemailer**.
2. **Threshold Monitoring:** Scheduled sync checks fetch stock movements. If a user’s dynamic price target is breached, an asynchronous event dispatches an alert email instantly without clogging user-facing thread loops.

---

## 📁 Project Directory Structure

```bash
├── app/
│   ├── (auth)/             # Auth views (Login, Sign-Up) powered by Better Auth
│   ├── (root)/             # Dashboard, Stock Profiler, and Watchlist views
│   └── api/
│       └── inngest/        # Inngest event execution & webhook receiver endpoint
├── components/             # Shareable interface components
│   ├── forms/              # Validated visual inputs for watchlists and alerts
│   └── ui/                 # Atomic design tokens (Buttons, Cards, Dialogs via Shadcn)
├── database/
│   ├── models/             # Mongoose Schemas (User, Stock, Alert, Watchlist)
│   └── mongoose.ts         # Persistent MongoDB connection pool logic
├── hooks/                  # Custom helper hooks (Debouncers, viewport utilities)
├── lib/
│   ├── actions/            # Server Actions (database updates, Finnhub REST fetches)
│   ├── better-auth/        # Security credentials configuration
│   ├── inngest/            # Queue definitions, job triggers, and Gemini prompts
│   └── nodemailer/         # SMTP transport setup and email compilation
```

---

## 🚀 Getting Started

Follow these steps to run a copy of the project local to your machine for development and testing.

### Prerequisites
* **Node.js** (v18.x or higher recommended)
* **npm** or **pnpm**
* A running **MongoDB** cluster (local or Atlas)

### 1. Clone the repository
```bash
git clone https://github.com/Dsaar/signalist_stock-tracker-app.git
cd signalist_stock-tracker-app
```

### 3. Setup Environment Variables
Create a `.env.local` file in the root directory:

```env
NODE_ENV='development'

NEXT_PUBLIC_BASE_URL='http://localhost:3000'

MONGODB_URI=your_mongodb_connection_string

BETTER_AUTH_SECRET=your_super_secret_better_auth_key

BETTER_AUTH_URL=http://localhost:3000

GEMINI_API_KEY=your_google_gemini_api_key

INNGEST_DEV=1

#NODEMAILER
NODEMAILER_EMAIL=your_sending_email@gmail.com
NODEMAILER_PASSWORD=your_app_specific_email_password

#FINNHUB
NEXT_PUBLIC_FINNHUB_API_KEY=your_finnhub_api_key
```

### 4. Run Development Servers
Signalist requires the Next.js framework server as well as the Inngest runner emulator to evaluate background events locally:

```bash
# Terminal 1: Run Next.js Dev Server
npm run dev

# Terminal 2: Run Inngest Local Dev Server
npx inngest-cli@latest dev
```

* Open [http://localhost:3000](http://localhost:3000) to view the application UI.
* Open [http://localhost:8288](http://localhost:8288) to view the Inngest automation panel to test triggers, track schedules, and debug runs.

---

## 🔒 Security & Optimization

* **Server-Action Isolation:** Database mutations and API credentials are kept strictly server-side using React 19 Server Actions.
* **Debounced Inputs:** Finnhub lookup queries on the UI are debounced to respect rate-limiting parameters.
* **Safe Session Serialization:** Handled securely by Better Auth with tokenized sessions stored in HTTP-only cookies.
