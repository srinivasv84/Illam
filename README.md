# Illam · இல்லம்

A beautiful, real-time family management app built with React, Supabase, and Vercel.

Track tasks, meals, medical info, school schedules, grocery lists, occasions, home maintenance, travel plans, and family chat — all in one place. Works beautifully on iPhone, iPad, and web.

## Features

- **Dashboard** — glance at pending tasks, upcoming occasions, today's meals, and home maintenance alerts
- **To-do & tasks** — assign responsibility to family members with due dates
- **Calendar** — birthdays, anniversaries, school events, holidays
- **Occasions** — track extended family birthdays and anniversaries with 60-day countdown
- **Food & health** — weekly meal planner, medication tracker, medical info (blood group, allergies)
- **Home log** — service records for appliances, vehicles, documents with "due soon" alerts
- **School & classes** — Advay and Saaral's schedules and extracurriculars
- **Grocery list** — organised by category, check off as you shop
- **Travel** — trip planner with destination, dates, notes
- **Family chat** — real-time messages with anyone in your family
- **Settings** — customise avatars (colour, emoji, initials), app name, branding

All data syncs in real-time across devices. Works offline with service workers (coming soon).

## Quick start (local development)

### 1. Clone this repo
```bash
git clone https://github.com/YOUR-USERNAME/illam.git
cd illam
```

### 2. Install dependencies
```bash
npm install
```

### 3. Set up environment
Copy `.env.example` to `.env.local` and add your Supabase credentials:
```bash
cp .env.example .env.local
```

Edit `.env.local`:
```
VITE_SUPABASE_URL=https://your-project.supabase.co
VITE_SUPABASE_ANON_KEY=your-anon-key-here
```

### 4. Run development server
```bash
npm run dev
```

Open http://localhost:5173 in your browser.

### 5. Deploy to Vercel

See [DEPLOYMENT.md](./DEPLOYMENT.md) for detailed instructions on:
- Setting up Supabase
- Creating database tables
- Deploying to Vercel
- Installing as home screen app on iPhone/iPad

## Tech stack

- **Frontend:** React 18, Vite, CSS custom properties
- **Database & Auth:** Supabase (PostgreSQL + PostgREST + Realtime)
- **Hosting:** Vercel
- **Icons:** Tabler Icons
- **Fonts:** DM Sans, DM Serif Display

## Browser support

- iOS Safari 14+
- Android Chrome/Firefox
- Desktop Chrome, Safari, Firefox, Edge

## Keyboard shortcuts

- `Enter` in task input → add task
- `Enter` in chat input → send message
- `Enter` in grocery input → add item

## Family setup

After deployment, each family member signs up with their own email. In Supabase, assign them all the same `family_id` so they see shared data.

See DEPLOYMENT.md → "Phase 4" for details.

## Architecture

```
illam/
├── src/
│   ├── pages/         # Dashboard, Tasks, Chat, Settings, etc.
│   ├── components/    # Sidebar, UI components (Modal, Avatar, etc.)
│   ├── App.jsx        # Main app shell
│   ├── AppContext.jsx # Global state, colour system, helpers
│   ├── index.css      # Design system & styling
│   ├── main.jsx       # Entry point
│   └── lib/supabase.js # Supabase client setup
├── index.html         # PWA meta tags for iOS install
├── package.json
├── vite.config.js
├── DEPLOYMENT.md      # Full setup guide
└── README.md          # This file
```

## Customisation

- **Colours:** Edit `COLORS` array in `src/AppContext.jsx`
- **Family members:** Edit `DEFAULT_MEMBERS` in `src/AppContext.jsx`
- **Styling:** Edit CSS variables in `src/index.css` (--accent, --bg, --text-*, etc.)
- **App name:** Settings page, or edit sidebar brand in `src/components/Sidebar.jsx`

## Real-time features

All pages listen to Supabase Realtime subscriptions:
- Tasks update instantly when anyone adds/completes a task
- Chat messages appear live
- Grocery items sync across devices
- Occasions countdown updates

## Offline support

Currently relies on browser cache. Service Worker coming soon for full offline-first support.

## Known limitations

- Family linking requires manual Supabase entry (no "family code" yet)
- No image uploads for profiles (can add)
- No recurring tasks (can add)
- No notifications to phone (can add Firebase Cloud Messaging)

## Contributing

This is your family's private app, but feel free to extend it! Ideas:

- Medical appointment reminders
- School report cards tracker
- Family budget tracking
- Shared expense splits
- Photo album
- Holiday countdown
- Kids' chore chart with rewards
- Expense splits for shared purchases

## License

MIT — feel free to use, modify, and share within your family.

## Support

See DEPLOYMENT.md for troubleshooting. For bugs or feature requests, open an issue or update the code yourself!

---

Built with ❤️ for families like yours. Enjoy Illam! 🏠
