# Illam — Deployment Guide

## Phase 1: Set up Supabase

### 1. Create a Supabase project
- Go to https://supabase.com
- Click "New Project"
- Enter project name: `illam-family`
- Choose a password (save it!)
- Choose a region closest to you
- Click "Create new project"
- Wait for it to initialize (2–3 minutes)

### 2. Create database tables

Once your project is ready, go to the **SQL Editor** and paste this code to create all tables:

```sql
-- Family members (just a reference table)
CREATE TABLE family_members (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  user_id uuid NOT NULL REFERENCES auth.users(id) ON DELETE CASCADE,
  family_id uuid NOT NULL,
  name TEXT,
  role TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Tasks
CREATE TABLE tasks (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  text TEXT NOT NULL,
  assignee TEXT DEFAULT 'all',
  cat TEXT DEFAULT 'Home',
  due_date DATE,
  done BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- Events (calendar)
CREATE TABLE events (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  name TEXT NOT NULL,
  date DATE NOT NULL,
  type TEXT DEFAULT 'Other',
  notes TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Trips
CREATE TABLE trips (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  destination TEXT NOT NULL,
  who TEXT DEFAULT 'all',
  from_date DATE,
  to_date DATE,
  notes TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Classes & activities
CREATE TABLE classes (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  name TEXT NOT NULL,
  schedule TEXT,
  who TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Grocery items
CREATE TABLE grocery (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  item TEXT NOT NULL,
  qty TEXT,
  cat TEXT DEFAULT 'Vegetables',
  bought BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Occasions
CREATE TABLE occasions (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  name TEXT NOT NULL,
  date DATE NOT NULL,
  type TEXT DEFAULT 'birthday',
  relation TEXT,
  notes TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Meals
CREATE TABLE meals (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  day TEXT NOT NULL,
  breakfast TEXT,
  lunch TEXT,
  dinner TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Medications
CREATE TABLE medications (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  name TEXT NOT NULL,
  dose TEXT,
  timing TEXT,
  who TEXT NOT NULL,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Home log
CREATE TABLE home_log (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  item TEXT NOT NULL,
  last_serviced DATE,
  next_due TEXT,
  cat TEXT DEFAULT 'Other',
  notes TEXT,
  created_at TIMESTAMP DEFAULT NOW()
);

-- Chat messages
CREATE TABLE chat_messages (
  id uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  family_id uuid NOT NULL,
  sender TEXT NOT NULL,
  text TEXT NOT NULL,
  sent_at TIMESTAMP DEFAULT NOW()
);

-- Enable RLS (Row Level Security) for all tables
ALTER TABLE tasks ENABLE ROW LEVEL SECURITY;
ALTER TABLE events ENABLE ROW LEVEL SECURITY;
ALTER TABLE trips ENABLE ROW LEVEL SECURITY;
ALTER TABLE classes ENABLE ROW LEVEL SECURITY;
ALTER TABLE grocery ENABLE ROW LEVEL SECURITY;
ALTER TABLE occasions ENABLE ROW LEVEL SECURITY;
ALTER TABLE meals ENABLE ROW LEVEL SECURITY;
ALTER TABLE medications ENABLE ROW LEVEL SECURITY;
ALTER TABLE home_log ENABLE ROW LEVEL SECURITY;
ALTER TABLE chat_messages ENABLE ROW LEVEL SECURITY;
ALTER TABLE family_members ENABLE ROW LEVEL SECURITY;

-- Create policies (allow authenticated users to read/write their family's data)
CREATE POLICY "Users can access their family's data" ON tasks
  FOR ALL USING (
    family_id IN (
      SELECT family_id FROM family_members WHERE user_id = auth.uid()
    )
  );

-- Repeat this policy for all other tables...
-- (For simplicity, you can temporarily disable RLS in development)
```

### 3. Get your Supabase credentials

- Go to **Settings** → **API**
- Copy your **Project URL** (looks like `https://xyz.supabase.co`)
- Copy your **anon public key** (the long string under "anon")

---

## Phase 2: Set up Vercel & deploy

### 1. Prepare your code for Vercel

In the `illam` folder on your computer, create a `.env.local` file:

```
VITE_SUPABASE_URL=https://YOUR-PROJECT.supabase.co
VITE_SUPABASE_ANON_KEY=YOUR-ANON-KEY-HERE
```

Replace with your actual values from Step 3 above.

### 2. Create a GitHub repository

- Go to https://github.com/new
- Create a repo called `illam`
- Do NOT initialize with README
- Click "Create repository"

### 3. Push your code to GitHub

Open Terminal in the `illam` folder and run:

```bash
git init
git add .
git commit -m "Initial commit: Illam family app"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/illam.git
git push -u origin main
```

Replace `YOUR-USERNAME` with your actual GitHub username.

### 4. Deploy to Vercel

- Go to https://vercel.com
- Sign in with your GitHub account (or create one)
- Click "Add New..." → "Project"
- Select your `illam` repo
- In **Environment Variables**, add:
  - `VITE_SUPABASE_URL` = your Supabase URL
  - `VITE_SUPABASE_ANON_KEY` = your anon key
- Click "Deploy"
- Wait for the build to finish (2–3 minutes)
- Your app is now live! The URL will be something like `illam.vercel.app`

---

## Phase 3: iPhone & iPad home screen install

Share the URL `https://illam.vercel.app` with your family members.

### On iPhone/iPad:
1. Open Safari
2. Paste the URL in the address bar
3. Tap the **Share** button (arrow pointing up)
4. Scroll down and tap **Add to Home Screen**
5. Tap **Add** to confirm
6. The app now appears as an icon on their home screen — opens full-screen like a native app

---

## Phase 4: Add family members to your family

Right now, each person needs to sign up with their own email.

**To link them to one family:**
- After sign up, you'll need to manually create a `family_members` record in Supabase
- Or we can add a "family code" feature so people can join with a code

For now, after everyone signs up, go to Supabase → **family_members** table and add each person with their user_id and a shared `family_id` (use a UUID — you can generate one at https://www.uuidgenerator.net).

---

## Troubleshooting

**Build fails with "missing dependencies":**
```bash
npm install
npm run build
```

**Env variables not working:**
- Make sure they're in Vercel's Environment Variables (not in `.env.local`)
- Redeploy after adding them

**Can't sign up:**
- Check Supabase → **Authentication** → **Users** to see if account was created
- Check email for confirmation link

---

## Next steps

Once deployed:
1. Invite the family to sign up at your Vercel URL
2. Assign each person a `family_id` in Supabase
3. Start adding tasks, meals, occasions!
4. Chat is real-time — try it out

Enjoy Illam! 🏠
