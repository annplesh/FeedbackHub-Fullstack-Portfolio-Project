# CLAUDE.md — Feedback Platform

## Project Map

```
feedback-platform/
├── index.html                  # Main HTML entry point, loads Google Fonts
├── package.json                # Dependencies and npm scripts
├── vite.config.js              # Vite config with React plugin
├── tailwind.config.js          # Tailwind setup: colors, fonts, breakpoints
├── postcss.config.js           # PostCSS plugins (Tailwind, autoprefixer)
├── .env                        # Supabase URL + anon key (local only)
├── .env.example                # Template for environment variables
├── .gitignore                  # Git ignore rules
├── CHANGELOG.md                # Project change history
│
└── src/
    ├── main.jsx                # React root — mounts <App /> into #root
    ├── App.jsx                 # Root component: routing + layout
    ├── index.css               # Tailwind directives + global styles
    ├── supabaseClient.js       # Supabase client initialization
    │
    ├── data/
    │   └── mockFeedback.js     # Hardcoded seed data (do not modify)
    │
    ├── hooks/
    │   ├── useFeedback.js      # Feedback CRUD logic + Supabase queries
    │   └── useProfile.js       # User profile: avatar, name, storage ops
    │
    ├── components/
    │   ├── Navbar.jsx          # Top navigation bar
    │   ├── StarRating.jsx      # Star component (input + display modes)
    │   ├── FeedbackCard.jsx    # Single feedback card for the Wall
    │   ├── AskAI.jsx           # AI response generator (Edge Function)
    │   ├── AvatarUpload.jsx    # Avatar upload to Supabase Storage
    │   └── CategorySelect.jsx  # Category dropdown for feedback form
    │
    └── pages/
        ├── LoginPage.jsx       # Email login (magic link)
        ├── RegisterPage.jsx    # User registration (name + email)
        ├── SubmitPage.jsx      # Public feedback submission form
        ├── WallPage.jsx        # Public feedback wall + sorting
        └── AdminPage.jsx       # Admin moderation: approve / reject

---

## Rules for Claude Code

### Structure
- Do NOT change the folder structure or move files.
- Do NOT create new folders unless explicitly instructed.
- Do NOT introduce new libraries or npm dependencies.
- Do NOT create a global state solution (Context, Zustand, Redux, etc.) unless explicitly requested.
- Do NOT refactor the architecture unless explicitly requested.

### Components
- Use functional components only. No class components.
- Do NOT rewrite a component fully unless explicitly asked — make targeted edits only.
- Modify only the files directly relevant to the task.
- Preserve each component's single responsibility. Do not merge component concerns.
- Follow existing naming conventions: PascalCase for components, camelCase for functions and variables.

### React
- Use hooks correctly. No side effects during render — all side effects go in `useEffect`.
- Keep state minimal and colocated — lift state only when necessary.
- Do NOT add `useEffect` unless the task explicitly requires it.
- Do NOT add `useContext`, `useReducer`, or `useRef` unless explicitly requested.

### Tailwind
- Keep Tailwind class lists minimal, readable, and consistent with existing patterns.
- Do NOT introduce arbitrary values (e.g. `w-[327px]`) unless there is no utility alternative.
- Do NOT add new custom classes to `index.css` unless explicitly asked.
- Do NOT modify `tailwind.config.js` custom colors or fonts unless explicitly asked.

### Data
- Do NOT modify `src/data/mockFeedback.js`.
- Do NOT replace mock data with fetch calls unless explicitly asked.

---

## How the Project Works

### State
`useFeedback.js` owns all feedback state. It initialises from `mockFeedback.js` and exposes:
- `approvedItems` — filtered list of entries with `approved: true`
- `submitFeedback({ name, message, rating })` — appends a new entry with `approved: false`

`App.jsx` calls `useFeedback` and passes `approvedItems` and `submitFeedback` down as props.

### Routing
`App.jsx` holds a `page` string (`'submit'` | `'wall'`). `Navbar` receives `page` and `setPage` and switches pages by calling `setPage`. No router library is used.

### Data Flow
```

mockFeedback.js
→ useFeedback.js (state initialisation)
→ App.jsx (destructures approvedItems + submitFeedback)
→ SubmitPage (receives onSubmit callback)
→ WallPage (receives items array)
→ FeedbackCard (receives single item + index)

```

### Components
- `StarRating` — stateless in display mode; manages internal `hovered` state in input mode.
- `FeedbackCard` — purely presentational; receives one item object and an index for animation delay.
- `Navbar` — receives `page` and `setPage`; renders navigation state, triggers no side effects.
- `SubmitPage` — owns local form state (name, message, rating, errors, status); calls `onSubmit` on success.
- `WallPage` — owns local `sort` state; derives sorted list via pure function; renders `FeedbackCard` per item.

### Workflow
- Before any changes — list affected files and describe what will change (one line per file).
- Wait for confirmation before editing.
- After changes — one line per file: what was changed.
- If task touches more than 3 files — flag it and ask for confirmation.
```
