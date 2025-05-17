# ConfNote
Set up your conference notes by importing a conference schedule, selecting your sessions, taking your notes and getting an email summary sent to you at the end of the session.

# Project Prototype Plan

## DAY 1–2: Core Data & Schedule Selection
1. Project & Infra Setup
- [ ] Initialize Vercel + V0 project, connect to GitHub repo
- [ ] Create a new Supabase project (free tier)
- [ ] Configure Supabase “Auth Flow” to Magic Link only
- [ ] Setup Prisma integration with Supabase
- [ ] Add environment variables in Vercel for SUPABASE_URL & SUPABASE_KEY

2. Prisma and Supabase Setup, & Security
- [ ] Define tables in Supabase:

```
users (auto-managed by Auth) - email, name, profilePicture?, selections

conferences - title, startDate, endDate, location, website

conferenceSpeakers - conferenceId, name, bio, profilePicture,

sessions - title, startDate, startTime, endDate, endTime, location, description, conferenceSpeakerId, conferenceId

selections - userId, sessionId, conferenceId

notes - selectionId, blob?, title, summary, aiOverwritten
```

- [ ] Write Row-Level Security (RLS) policies so each table row references auth.uid() and users only see their own data
- [ ] Test RLS by attempting cross-user reads/writes

3. Routing & Layout
- [ ] `/` – Conference list
- [ ] `/sign-up` - User sign-up - limited to 100 signups
- [ ] `/sign-in` – Magic-link email entry form
- [ ] `/auth/callback` – Supabase session handler
- [ ] `/new-conference` – CSV or URL import UI
- [ ] `/conference/:id` – Session browse & select
- [ ] `/conference/:id/my-schedule` – User’s selections + “Take Notes”
- [ ] `/conference/:id/my-schedule/:session-id/note` - Note taking page for the specific session for the users' schedule

4. Conference Upload & Parsing
- [ ] Build CSV upload component (drag/drop or file picker)
- [ ] Parse CSV into { title, speaker, startTime, endTime, location, description }
- [ ] Insert parsed sessions into Supabase, linking to a new conference record

5. Session Selection Wizard
- [ ] Query sessions grouped by time slot from Supabase
- [ ] UI to pick exactly one per slot
- [ ] Persist each choice to selections with user_id = auth.uid()

## DAY 3: Note-Taking & AI
1. Note Editor
- [ ] Integrate a Markdown-friendly editor (e.g. Tiptap or react-markdown)
- [ ] Toggle UI between text & handwriting stub (canvas placeholder)
- [ ] Autosave on change to notes table (with user_id, session_id)

2. AI-Driven Metadata
- [ ] On save or explicit “Generate” click, call OpenAI to produce:
```
aiTitle

aiSummary
```
- [ ] Store these in the same notes row; allow user overrides

DAY 4: Export & Wrap-Up
1. Calendar Export
- [ ] Use an NPM ICS generator to create .ics from your selections
- [ ] “Download Schedule” button that streams the file in browser

2. Summary Email
- [ ] Build “Conference Summary” page compiling all notes + a “General Observations” entry
- [ ] “Email Me Summary” → open mailto: with prefilled body or hook a transactional email service (optional)

3. Final Polish
- [ ] Seed a few test conferences & users via Supabase dashboard
- [ ] Verify Magic Link flow end-to-end (sign-up, click link, land authenticated)
- [ ] Minor UI/UX touches (loading states, mobile responsiveness)

## Stretch / Later Iterations
- Handwriting capture & storage (Supabase Storage bucket)
- Full auth methods (GitHub/Google) via Supabase OAuth
- Conference publisher portal (role-based RLS)
