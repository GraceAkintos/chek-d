# Chek'd

Chek'd is a web app for taking class attendance with time-limited QR codes. A professor starts a session and shows a QR code that changes every few seconds. Students scan it with their phone camera and are checked in, so attendance is quick and hard to fake.

Chek'd is the web version of [Checkd](https://github.com/GraceAkintos/checkd), an Android app.

> **Status:** in early development. The project setup is in place, and the features below are being built.

## Features

- **Professors:** create classes, start a live attendance session, display a rotating QR code, and watch check-ins arrive in real time.
- **Students:** scan the QR code with their phone's camera to check in, then view their attendance history.
- **Hard to cheat:** each QR code holds a signed link that expires after a few seconds, so a screenshot shared with someone outside the room stops working.
- **No app to install:** everything runs in the browser on a phone or a laptop.

## How check-in works

1. The professor starts a session. The server generates a signed token for that session and the current time window.
2. The QR code shows a link such as `https://<app-url>/checkin?t=<token>`, and a new token replaces it every 15–30 seconds.
3. A student scans the code with their phone camera, which opens the link and asks them to log in if needed.
4. The server checks the token's signature and time window, then records the check-in. Each student can check in only once per session.

## Tech stack

- [Next.js 16](https://nextjs.org) (App Router) with TypeScript
- [Tailwind CSS 4](https://tailwindcss.com) and [shadcn/ui](https://ui.shadcn.com)
- [Supabase](https://supabase.com): Postgres database, Auth, Realtime and Row Level Security
- [`qrcode`](https://www.npmjs.com/package/qrcode) to generate QR codes
- Hosted on [Vercel](https://vercel.com)

## Getting started

### Requirements

- Node.js 20.9 or newer (22 recommended)
- A free [Supabase](https://supabase.com) account

### 1. Clone and install

```bash
git clone https://github.com/GraceAkintos/chek-d.git
cd chek-d
npm install
```

### 2. Create a Supabase project

1. Create a new project in the [Supabase dashboard](https://supabase.com/dashboard).
2. In **Authentication → URL Configuration**, set **Site URL** to `http://localhost:3000`.

### 3. Add environment variables

Create a file called `.env.local` in the project root:

```bash
NEXT_PUBLIC_SUPABASE_URL=https://<project-ref>.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=<publishable key>
SUPABASE_SERVICE_ROLE_KEY=<secret key>
CHECKIN_TOKEN_SECRET=<random string>
```

| Variable | Where to find it |
| --- | --- |
| `NEXT_PUBLIC_SUPABASE_URL` | Supabase dashboard → **Connect** |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | **Project Settings → API Keys** → publishable key |
| `SUPABASE_SERVICE_ROLE_KEY` | **Project Settings → API Keys** → secret key (server only, never share it) |
| `CHECKIN_TOKEN_SECRET` | Generate one with `openssl rand -hex 32` |

`.env.local` is ignored by Git. Never commit it.

### 4. Set up the database

Link the Supabase CLI to your project, then apply the migrations in `supabase/migrations/`:

```bash
npx supabase login
npx supabase link --project-ref <project-ref>
npx supabase db push
```

### 5. Run the app

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

To test check-in on your phone, start the server on your local network and open `http://<your-computer-ip>:3000` on the phone. The phone and computer must be on the same Wi-Fi.

```bash
npm run dev -- -H 0.0.0.0
```

## Scripts

| Command | What it does |
| --- | --- |
| `npm run dev` | Start the development server |
| `npm run build` | Build for production |
| `npm run start` | Run the production build |
| `npm run lint` | Check the code with ESLint |

## Project structure

```text
src/
├── app/              Pages and routes (Next.js App Router)
├── components/ui/    shadcn/ui components
└── lib/              Shared helpers
supabase/
├── config.toml       Supabase CLI configuration
└── migrations/       Database schema
```

## Deployment

1. Push the repository to GitHub.
2. Import it at [vercel.com/new](https://vercel.com/new).
3. Add the four environment variables from step 3 in the Vercel project settings.
4. In Supabase, change **Site URL** to your Vercel address.

Vercel redeploys automatically on every push to `main`.

## License

No license has been specified yet.
