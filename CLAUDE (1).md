# CLAUDE.md — Hero Academy
## Single Source of Truth for All Development

---

## 0. HOW TO USE THIS FILE

Read this entire file before writing any code. Every architectural decision,
design choice, content structure, and behavioral rule is defined here.
When in doubt about anything — color, font size, button behavior, question
logic, data structure — the answer is in this file. Do not invent conventions
not defined here. If something is genuinely ambiguous, add a TODO comment
and flag it rather than guessing.

This file is the law. Code serves this file, not the other way around.

---

## 1. PROJECT OVERVIEW

**Name:** Hero Academy
**Type:** Single-file web application (index.html) with embedded CSS and JS
**Purpose:** Turn a struggling 3rd-grade boy (age 9) into a confident,
top-performing student ready for 4th grade — using engaging superhero-themed
gameplay backed by Massachusetts-rigor academic content.
**Target user:** One specific child, male, age 9, 3rd grade, Jefferson County
Public Schools, Kentucky
**Standards:** Kentucky Academic Standards (floor) + Massachusetts Curriculum
Frameworks Grade 3 (ceiling)
**Deployment:** GitHub repository → Netlify auto-deploy (static hosting, no backend)
**Storage:** localStorage only — no server, no accounts, no network required
**Device:** Desktop/laptop, keyboard + mouse, minimum 1024px viewport

---

## 2. CARDINAL RULES (never violate these)

1. **Single file only.** Everything — HTML, CSS, JS, content data — lives in
   index.html. No external files except Google Fonts (CDN link in head).

2. **Never punish wrong answers.** No red X. No "Wrong!" No point deduction.
   No harsh sounds. Wrong answers earn 0 PP and show a warm explanation. Always.

3. **Every session ends as a win.** The Victory Wrap-Up screen fires at the
   end of every session, even if cut short. The child never closes the app
   feeling like he failed.

4. **Text is never smaller than 16px.** Base body text is 20px. Question text
   is 20px. Button text is 20px bold. H1 is 32px. Nothing goes below 16px.

5. **Buttons are never smaller than 56px tall.** All interactive targets meet
   this minimum. This is non-negotiable for a 9-year-old user.

6. **The spaced repetition engine controls content selection.** The child
   never manually selects which mission or question to answer. The system
   decides what he needs based on mastery tracking.

7. **localStorage key is always `heroAcademy_playerData`.** Never rename it.
   All reads and writes go through the two helper functions:
   `savePlayerData(data)` and `loadPlayerData()`.

8. **No external JavaScript libraries.** Vanilla JS only. No jQuery, no React,
   no lodash. Google Fonts CDN link is the only external dependency.

9. **Tutor Mode is never mocked or placeholder.** Every sub-skill listed in
   Section 6 must have a real, complete Tutor Mode explanation written and
   stored in the CONTENT object. No "TODO: add explanation here."

10. **Theme is data-driven.** All theme strings (rank names, color accents,
    world names) live in the THEME config object at the top of the JS block.
    Changing theme means changing that object only — zero other code changes.

---

## 3. FILE STRUCTURE (inside index.html)

```
index.html
├── <head>
│   ├── meta charset, viewport, title
│   ├── Google Fonts link (Nunito 400, 600, 700, 800)
│   └── <style> block — ALL CSS
└── <body>
    ├── #app  (single mount point — all screens render here)
    └── <script> block — ALL JavaScript
        ├── THEME config object
        ├── CONTENT data object (all questions, passages, tutor mode text)
        ├── STATE object (runtime state, not persisted)
        ├── Helper functions (savePlayerData, loadPlayerData, etc.)
        ├── Spaced repetition engine
        ├── Question selection engine
        ├── Session manager
        ├── Screen renderers (one function per screen)
        ├── Event delegation handler
        └── App init (runs on DOMContentLoaded)
```

---

## 4. DESIGN SYSTEM

### 4.1 Typography

```css
--font-family: 'Nunito', system-ui, sans-serif;
--font-size-base: 20px;
--font-size-sm: 16px;
--font-size-md: 22px;
--font-size-lg: 26px;
--font-size-xl: 32px;
--font-weight-normal: 400;
--font-weight-semi: 600;
--font-weight-bold: 700;
--font-weight-black: 800;
--line-height-base: 1.6;
--line-height-tight: 1.3;
```

### 4.2 Color System (Superhero Theme — default)

```css
--color-bg:           #0F1B2D;   /* deep navy — main background */
--color-surface:      #1A2E45;   /* card/panel background */
--color-surface-hi:   #243B55;   /* elevated surface (modals) */
--color-border:       #2E4A6A;   /* subtle borders */
--color-accent-gold:  #FFD700;   /* primary accent — achievement */
--color-accent-blue:  #4FC3F7;   /* secondary accent — energy */
--color-success:      #66BB6A;   /* correct answer feedback */
--color-warning:      #FFA726;   /* try again, neutral feedback */
--color-text-primary: #F5F5F5;   /* main text */
--color-text-secondary: #B0BEC5; /* labels, metadata */
--color-text-on-gold: #0F1B2D;   /* dark text on gold buttons */
```

### 4.3 Spacing

Base unit: 8px. All padding and margin values are multiples of 8.

```css
--space-1: 8px;
--space-2: 16px;
--space-3: 24px;
--space-4: 32px;
--space-5: 40px;
--space-6: 48px;
```

### 4.4 Border Radius

```css
--radius-sm: 8px;
--radius-md: 12px;
--radius-lg: 20px;
--radius-xl: 28px;
--radius-full: 9999px;
```

### 4.5 Shadows

```css
--shadow-card: 0 4px 24px rgba(0,0,0,0.4);
--shadow-button: 0 4px 12px rgba(0,0,0,0.3);
--shadow-glow-gold: 0 0 20px rgba(255,215,0,0.3);
```

### 4.6 Motion

```css
--transition-fast: 150ms ease;
--transition-base: 200ms ease;
--transition-slow: 400ms ease;
--transition-celebrate: 600ms cubic-bezier(0.34, 1.56, 0.64, 1);
```

Always include:
```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
  }
}
```

### 4.7 Button Styles

**Primary button (gold — main actions):**
```
background: var(--color-accent-gold)
color: var(--color-text-on-gold)
font-size: 20px, font-weight: 700
min-height: 56px
padding: 16px 32px
border-radius: var(--radius-md)
border: none
cursor: pointer
box-shadow: var(--shadow-button)
transition: transform var(--transition-fast), box-shadow var(--transition-fast)
hover: transform translateY(-2px), shadow stronger
active: transform translateY(0)
focus: outline 3px solid var(--color-accent-gold), outline-offset 3px
```

**Secondary button (outlined — secondary actions):**
```
background: transparent
border: 2px solid var(--color-accent-blue)
color: var(--color-accent-blue)
Same sizing as primary
```

**Answer tile (question choices):**
```
background: var(--color-surface-hi)
border: 2px solid var(--color-border)
color: var(--color-text-primary)
font-size: 20px
min-height: 80px
border-radius: var(--radius-md)
full width in its grid cell
selected state: border-color var(--color-accent-blue), background slightly lighter
hover: border-color var(--color-accent-blue) at 50% opacity
```

### 4.8 Screen Layout

All screens render inside `#app`. Each screen is a full-viewport div.

```
#app {
  width: 100vw;
  min-height: 100vh;
  background: var(--color-bg);
  font-family: var(--font-family);
  font-size: var(--font-size-base);
  color: var(--color-text-primary);
  line-height: var(--line-height-base);
}

.screen {
  max-width: 860px;
  margin: 0 auto;
  padding: var(--space-4);
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
```

Max content width: 860px, centered. Wider than typical to fit reading passages
comfortably at 20px font size.

---

## 5. SCREEN SPECIFICATIONS

### Screen order and IDs:
- `screen-onboarding` — first launch only
- `screen-dashboard` — home screen
- `screen-warmup` — session warm-up wrapper
- `screen-question` — core question component (used in all modes)
- `screen-tutor` — Coach Hero explanation panel
- `screen-mission-complete` — between missions
- `screen-victory` — end of session
- `screen-badges` — badge collection view
- `screen-mission-map` — progress overview
- `screen-parent` — parent dashboard (PIN protected)

Screens are shown/hidden by the `showScreen(screenId)` function which:
1. Sets `STATE.currentScreen = screenId`
2. Clears `#app` innerHTML
3. Calls the matching render function
4. Scrolls to top

### 5.1 Onboarding Screen

Shown only when `loadPlayerData()` returns null.

Elements:
- App logo: ⚡ HERO ACADEMY ⚡ at H1 size in gold
- Subtitle: "Where Heroes Are Made" in accent blue, 22px
- Animated hero silhouette (CSS keyframe animation — simple pulsing glow)
- Label: "What's your hero name?" 22px
- Text input: 24px font, min-height 56px, max 12 characters, letters only,
  placeholder "Enter your hero name..."
- Label: "Pick your hero color:" 22px
- Five color swatch buttons (56px circles): Red #EF5350, Blue #4FC3F7,
  Green #66BB6A, Purple #CE93D8, Orange #FFA726
- Primary button: "BEGIN MY JOURNEY"

Validation: name must be 2-12 letters, one color must be selected.
On submit: animate "Hero Academy welcomes [Name]! Your training begins now."
for 2.5 seconds, then save initial player data and show dashboard.

Initial player data on first launch:
```javascript
{
  player: {
    name: enteredName,
    heroColor: selectedColor,
    rank: 1,
    totalPP: 0,
    currentStreak: 0,
    lastSessionDate: null,
    badges: [],
    sessionsCompleted: 0
  },
  progress: {},   // populated as questions are answered
  sessions: [],
  settings: {
    parentPin: "1234",  // default, parent can change
    theme: "superhero",
    soundEnabled: false  // off by default, no audio assets needed
  }
}
```

### 5.2 Hero Dashboard

Top bar:
- Gear icon (⚙) top right, 40px click target, opens parent PIN prompt
- No back button (this is home)

Hero card (full-width surface card):
- Hero avatar: large emoji or CSS-drawn hero figure in their chosen color
- Rank title in gold (from THEME.ranks[currentRank].title)
- Hero name in white, 26px, bold
- PP progress bar: filled portion = progress toward next rank
  - Label: "[totalPP] PP" left, "Next: [threshold] PP" right, 16px
- Progress bar height: 16px, rounded, gold fill on navy track

Stats row (two cards side by side):
- Left: 🏅 [badgeCount] Badges
- Right: ⭐ [goldMissionCount] Gold Missions

Streak display:
- If streak > 0: 🔥 [n]-Day Streak! in orange, 22px
- If streak = 0: "Start a new streak today!" in warning color, 20px

Today's Training preview:
- Section header "TODAY'S TRAINING:" 22px
- Two mission names with emoji icons, selected by session engine
- These are informational only — no tap action

Primary button: "▶ BEGIN TRAINING" full width, gold

Secondary text links below button:
- "View Missions" → screen-mission-map
- "My Badges" → screen-badges

### 5.3 Warm-Up

Top bar:
- "WARM-UP" centered, 22px
- ⚡ [totalPP] PP live counter, top right
- No back button during active session

Intro card shown before first question:
- "🔥 Time to warm up, [Name]!" 22px
- "Let's get those hero powers ready." 20px
- Dismisses automatically after 2 seconds OR on any tap

Progress indicator:
- Row of 10 dots: filled gold = complete, empty = remaining
- "Question [n] of 10" label, 16px secondary

Question renders in the shared question component (see 5.4).

After question 10: automatic transition to Mission Challenge (Math first).

### 5.4 Question Screen (Core Component)

This component is called with a question object and a mode:
`renderQuestion(questionObj, mode)`

Modes: 'warmup' | 'challenge' | 'champion'

**Top bar:**
- Mission name left, 16px secondary
- ⚡ [totalPP] PP right — updates live on correct answer

**Progress bar:**
- Thin bar below top bar showing position within current mission block
- Tier label right-aligned: "Tier 1" / "Tier 2" / "Tier 3"

**Question card:**
- Surface card, full width
- Question text: 20px, 1.6 line height
- If passage question: passage renders ABOVE question in scrollable box
  (max-height 45vh, overflow-y auto, passage text 20px, 1.6 line height)
  passage title in 22px bold at top of passage box

**Answer areas by question type:**

TYPE: multiple_choice
- 2×2 grid of answer tiles (always 4 options)
- Each tile: min-height 80px, 20px text, full content centered
- On tile tap: tile gets selected state (blue border)
- "CHECK ANSWER" button appears below grid after selection
- Button disabled until a tile is selected

TYPE: numeric_input
- Centered number input field: 120px wide, 56px tall, 28px font, centered text
- Numeric input type, no spinner arrows
- "CHECK ANSWER" button below

TYPE: drag_order
- Vertical stack of draggable tiles
- Each tile: full width, 56px min-height, 20px text
- Drag handle (☰) on left, 24px, secondary color
- Native HTML5 drag and drop
- "CHECK ORDER" button below stack

TYPE: sentence_select
- Passage displayed (same as passage layout above)
- Below passage: "Which sentence best answers the question?"
- Clickable sentences highlighted on hover, selected on click
- "CHECK ANSWER" button appears after selection

**Feedback states:**

Correct:
- Selected tile/input background flashes green for 600ms
- Feedback card slides up: "✅ That's right!" in success green, 22px
- "+[n] Power Points! ⚡" in gold, animated count-up
- PP counter in top bar increments
- Auto-advance to next question after 1800ms
- "NEXT →" button also shown for faster players

Incorrect:
- Selected tile gets warm orange border (never red)
- Feedback card slides up: "Not quite — keep going!" in warning color, 22px
- "The answer is: [correct answer]" in white, 20px
- "💡 Ask Coach Hero" link in accent blue — tapping opens tutor mode
- Auto-advance after 2200ms or on tap of "NEXT →"
- Zero PP awarded, no other penalty
- Missed question recorded in progress tracker

**Hint system:**
- After 45 seconds of no interaction: Coach Hero speech bubble appears
  at bottom: "Need a hint? No worries — tap the question again and
  look for the key words!"
- This is encouraging, not shaming

### 5.5 Tutor Mode

Triggered by:
1. Two consecutive wrong answers on same concept_id
2. Tapping "💡 Ask Coach Hero" after any wrong answer

Renders as full screen (not modal) so text has full reading space.

Elements:
- Coach Hero avatar (large emoji: 🦸, styled with hero color)
- "Let me show you something." in accent blue, 22px, italic
- Concept title in gold, 22px bold
- Explanation text: 20px, 1.6 line height, max 5 sentences
  (pulled from CONTENT[missionId].tutorMode[conceptId].explanation)
- Visual example: SVG diagram or structured HTML visual
  (pulled from CONTENT[missionId].tutorMode[conceptId].visual — HTML string)
- "Now try this one:" in white, 22px
- Easier version of question (Tier drops by 1)
- "+5 ⚡ for training with Coach Hero!" in gold — always awarded

First exposure to a concept: scroll-lock until explanation div is fully
scrolled through. After that: "Skip to question →" link appears at top.

After the tutor question (right or wrong): advance forward, never loop.

### 5.6 Mission Complete Screen

Elements:
- "⚡ MISSION COMPLETE! ⚡" H1, gold, slide-in animation
- Mission name and round number, 22px
- Results card:
  - Accuracy percentage with star rating
    (⭐⭐⭐ = 90%+, ⭐⭐ = 70%+, ⭐ = below 70%)
  - PP earned this mission block, animated count-up
  - Streak status
- Badge progress bar if applicable:
  "[n] more correct answers for Silver Badge!" with progress bar
- Coach Hero quote: short, specific encouragement referencing
  the actual mission just completed. 20px italic.
  (One quote per mission stored in CONTENT data)
- Two equal buttons: "NEXT MISSION →" and "TAKE A BREAK"

### 5.7 Victory Wrap-Up Screen

Always shown at session end.

Elements:
- "🏆 TRAINING COMPLETE, [Name]!" H1, animated entrance
- Session PP card:
  - "TODAY'S POWER" label
  - "⚡ +[sessionPP] Power Points" large, gold, animated count-up
  - "Total: [totalPP] PP" below
  - Rank progress bar with label
- Missions trained list with accuracy and checkmark
- Badge unlock animation if badge earned this session:
  full-width celebration with badge name, 600ms animation
- Rank-up animation if rank advanced:
  full-screen flash with new rank title, 1200ms
- Streak message: "🔥 [n]-Day Streak — Keep it going!"
- Encouragement paragraph: 2-3 sentences specific to today's performance
- Two buttons: "KEEP TRAINING" (secondary) and "DONE FOR TODAY" (primary)

"DONE FOR TODAY" saves all data and returns to dashboard.
"KEEP TRAINING" starts another mission block (no warm-up).

### 5.8 Badge Collection Screen

Top bar: "← BACK" left, "MY BADGES" centered

Earned section:
- "EARNED — [n] badges" header, 22px
- CSS grid, 3 columns, gap 16px
- Each badge tile: 100px × 120px, surface card
  - Emoji icon 40px
  - Badge name 16px below
  - Date earned on hover/focus
  - Tap to open badge detail modal

Locked section:
- "LOCKED" header, 22px, secondary color
- Same grid, but tiles show 🔒 with silhouette
- Badge name hidden — shows "???" 
- Tap shows only: "Keep training to unlock this!"

Badge detail modal (tap on earned badge):
- Badge emoji large (64px)
- Badge name 26px gold
- Description of what it means, 20px
- Date earned, 16px secondary
- "CLOSE" button

### 5.9 Mission Map Screen

Top bar: "← BACK" left, "MISSION MAP" centered

Two sections with world headers:

World header: "⚡ NUMBER KINGDOM" in gold 26px (Math)
World header: "📖 WORD REALM" in accent blue 26px (ELA)

Each mission card (surface card, full width):
- Left: mission emoji + mission name, 22px bold
- Right: badge earned (🥇/🥈/🥉 or empty) + accuracy percentage
- Bottom: progress bar, filled = questions answered / total questions
- Tap: shows mission detail (accuracy breakdown by tier, last played date)

This screen is READ ONLY. No "play this mission" button.
Text at top: "Your training path is chosen for you — the system
knows exactly what you need! 🦸" in secondary color, 20px.

### 5.10 Parent Dashboard

Access: gear icon on dashboard → PIN prompt (default 1234).
PIN prompt: four large digit buttons (80px each), shows entered dots.

Top bar: "← EXIT" left, "PARENT VIEW" centered

Sections:

**Header:**
"[Name]'s Progress" 26px
"Last session: [date], [duration] minutes" 20px secondary

**This Week (session history bar chart):**
Seven day labels (Mon–Sun)
Bar for each day proportional to session duration (max 40 min = full bar)
Gold bars for days with sessions, empty for missed days
No bar = missed day (no guilt language — just empty)

**Skill Health:**
One row per mission with:
- Mission name
- Color indicator: 🟢 Strong (85%+), 🟡 Good (70-84%), 🔴 Needs Work (<70%)
- Accuracy percentage
- Questions answered total

**Focus Areas (auto-generated):**
Lists any mission where accuracy < 65% over last 15 questions.
Plain language: "[Mission name]: He's getting [n]% of these right.
More practice is coming automatically."

**Recent Sessions table:**
Date | Duration | PP Earned | Missions
Last 10 sessions, most recent first

**Actions:**
- "Change PIN" — prompts current PIN then new PIN twice
- "Reset Progress" — confirmation dialog, destructive action, clears all data

---

## 6. CONTENT DATA STRUCTURE

All content lives in the `CONTENT` constant object in the JS block.
Structure:

```javascript
const CONTENT = {
  missions: {
    "m1-1": {
      id: "m1-1",
      world: "math",
      name: "Multiplication Mastery",
      emoji: "⚡",
      conceptIds: ["mult-concept", "mult-arrays", "mult-unknown", "mult-word"],
      tutorMode: {
        "mult-concept": {
          explanation: "...",  // 3-5 sentences, 20px reading level
          visual: "<div>...</div>"  // HTML string for inline diagram
        },
        // one entry per conceptId
      },
      missionCompleteQuote: "...",
      questions: [
        {
          id: "m1-1-q001",
          tier: 1,
          type: "multiple_choice",  // multiple_choice | numeric_input | drag_order | sentence_select
          conceptId: "mult-arrays",
          standardKY: "3.OA.1",
          standardMA: "3.OA.A.1",
          question: "Which multiplication equation matches this array?\n⭐⭐⭐\n⭐⭐⭐\n⭐⭐⭐\n⭐⭐⭐",
          options: ["3 × 3 = 9", "4 × 3 = 12", "3 × 4 = 12", "4 × 4 = 16"],
          correct: 1,  // index into options array (0-based). For numeric_input: string of correct value
          explanation: "There are 4 rows and 3 stars in each row. 4 × 3 = 12."
        }
        // ... more questions
      ]
    },
    "m1-2": { /* Division Decoded */ },
    "m1-3": { /* Fraction Fortress */ },
    "m1-4": { /* Place Value Plaza */ },
    "m1-5": { /* Measurement HQ */ },
    "m1-6": { /* Data & Graphs Galaxy */ },
    "m2-1": { /* Literary Text */ },
    "m2-2": { /* Informational Text */ },
    "m2-3": { /* Vocabulary Vault */ },
    "m2-4": { /* Writing Workshop */ }
  }
};
```

### 6.1 Question Count Targets

| Mission | Tier 1 | Tier 2 | Tier 3 | Total |
|---------|--------|--------|--------|-------|
| m1-1 Multiplication | 20 | 20 | 15 | 55 |
| m1-2 Division | 20 | 20 | 15 | 55 |
| m1-3 Fractions | 20 | 20 | 15 | 55 |
| m1-4 Place Value | 15 | 15 | 10 | 40 |
| m1-5 Measurement | 15 | 15 | 10 | 40 |
| m1-6 Data | 10 | 10 | 8 | 28 |
| m2-1 Literary | 12 | 10 | 8 | 30 |
| m2-2 Informational | 12 | 10 | 8 | 30 |
| m2-3 Vocabulary | 20 | 20 | 15 | 55 |
| m2-4 Writing | 15 | 15 | 10 | 40 |
| **TOTAL** | **159** | **155** | **114** | **428** |

### 6.2 Reading Passages

Passages for m2-1 (Literary) and m2-2 (Informational) are stored inline
in the question object as a `passage` field:

```javascript
{
  id: "m2-1-q001",
  tier: 2,
  type: "multiple_choice",
  conceptId: "character-traits",
  passage: {
    title: "The Bravest Turtle",
    text: "Maya the turtle had always been afraid...",
    wordCount: 280
  },
  question: "What is Maya's biggest challenge at the beginning of the story?",
  options: [...],
  correct: 0,
  explanation: "The first paragraph tells us Maya was afraid of deep water..."
}
```

Passage word counts by tier:
- Tier 1: 150-200 words
- Tier 2: 280-350 words
- Tier 3: 380-500 words

All passages are original content — never copied from published works.
Informational passages use real facts but original writing.
Topics for m2-2 (rotate across questions to build knowledge):
- Life cycles (butterflies, frogs, plants)
- American history (Abraham Lincoln, Harriet Tubman, Native Americans)
- Earth science (weather, seasons, water cycle)
- Ecosystems (rainforest, ocean, desert)
- Simple economics (needs vs. wants, goods and services)

---

## 7. PLAYER DATA SCHEMA

Stored as JSON string at localStorage key `heroAcademy_playerData`.

```javascript
{
  player: {
    name: String,           // hero name entered at onboarding
    heroColor: String,      // hex color selected
    rank: Number,           // 1-6
    totalPP: Number,        // lifetime power points
    currentStreak: Number,  // consecutive days with sessions
    lastSessionDate: String, // ISO date string YYYY-MM-DD
    badges: [String],       // array of badge IDs earned
    sessionsCompleted: Number
  },
  progress: {
    // keyed by question ID
    "m1-1-q001": {
      masteryLevel: Number,   // 0=unseen/struggling, 1=seen once correct,
                              // 2=correct twice, 3=mastered
      timesCorrect: Number,
      timesIncorrect: Number,
      lastSeen: String,       // ISO timestamp
      nextReview: String      // ISO timestamp
    }
    // ...
  },
  sessions: [
    {
      date: String,           // ISO date string
      durationMinutes: Number,
      ppEarned: Number,
      missionsPlayed: [String], // mission IDs
      accuracyByMission: {
        "m1-3": 0.84,         // float 0-1
        "m2-1": 0.91
      }
    }
    // last 90 sessions max — trim older entries
  ],
  settings: {
    parentPin: String,        // 4-digit string
    theme: String,            // "superhero" (default)
    soundEnabled: Boolean
  }
}
```

Helper functions (always use these — never access localStorage directly):

```javascript
function savePlayerData(data) {
  try {
    localStorage.setItem('heroAcademy_playerData', JSON.stringify(data));
  } catch(e) {
    console.error('Save failed:', e);
  }
}

function loadPlayerData() {
  try {
    const raw = localStorage.getItem('heroAcademy_playerData');
    return raw ? JSON.parse(raw) : null;
  } catch(e) {
    return null;
  }
}
```

---

## 8. SPACED REPETITION ENGINE

### 8.1 Mastery Levels and Review Intervals

```
Level 0 (unseen or failed 2+ times in a row):
  → Appears every session until answered correctly

Level 1 (answered correctly once):
  → nextReview = now + 1 day

Level 2 (correctly twice):
  → nextReview = now + 3 days

Level 3 (correctly three times — mastered):
  → nextReview = now + 7 days
  → After 7-day review if still correct: retired (shown monthly only)
```

On wrong answer: masteryLevel decreases by 1 (min 0), nextReview = today.

### 8.2 Question Selection Priority Queue

The `selectQuestions(count, mode)` function builds a question pool in order:

```
Priority 1 (HIGHEST): masteryLevel 0 questions not seen in this session
Priority 2: Questions where nextReview <= today
Priority 3: New questions (never seen — masteryLevel undefined in progress)
            → Select from current tier of current mission
Priority 4: masteryLevel 2-3 questions for warm-up/review
Priority 5 (LOWEST): Random from mastered pool (keeps content fresh)
```

Never repeat a question within the same session.
Never serve a Tier 3 question until player has 80%+ accuracy
on 20+ Tier 2 questions in that mission.

### 8.3 Adaptive Difficulty

Within a mission challenge block:
- Start at player's current working tier for that mission
- 4+ correct in a row → bump up one tier (max Tier 3)
- 2 incorrect in a row → bump down one tier (min Tier 1)
- Tier changes take effect on the next question

---

## 9. SESSION MANAGER

### 9.1 Session Structure

```
SESSION START
  → Check and update streak
  → Select today's two missions (one Math, one ELA)
  → Initialize sessionState
  → Show Warm-Up (10 questions, Level 2-3 review only)

WARM-UP COMPLETE
  → Transition to Mission Challenge — Mission A (Math)
  → 12 questions, adaptive difficulty

MISSION A COMPLETE
  → Show Mission Complete Screen
  → If "Next Mission": proceed to Mission B

MISSION B (ELA)
  → 12 questions, adaptive difficulty

MISSION B COMPLETE
  → Show Mission Complete Screen
  → If "Next Mission": show Victory Wrap-Up
  → If "Take a Break": show Victory Wrap-Up

VICTORY WRAP-UP
  → Calculate session PP total
  → Award session completion bonus (+20 PP)
  → Check and award badges
  → Check rank advancement
  → Save all data
  → Show wrap-up screen

DONE FOR TODAY → Dashboard
KEEP TRAINING → New Mission Challenge (skip warm-up, pick next priority mission)
```

### 9.2 Mission Selection Algorithm

Daily mission pair = one from Math world + one from ELA world.
Selection priority:
1. Mission with lowest accuracy percentage overall
2. Mission not played in longest time
3. Mission with most overdue review questions

Never play the same mission pair two days in a row.

### 9.3 Streak Logic

On session start:
- Load lastSessionDate
- If lastSessionDate = yesterday: currentStreak++
- If lastSessionDate = today: streak unchanged (already played today)
- If lastSessionDate = before yesterday OR null: currentStreak = 1

Save updated streak and lastSessionDate at session end.

### 9.4 Tutor Mode Trigger

Track `STATE.consecutiveWrongOnConcept` — a map of conceptId → count.
On wrong answer: increment count for that conceptId.
If count reaches 2: auto-trigger Tutor Mode for that conceptId.
Reset count for a conceptId on any correct answer in that concept.

---

## 10. BADGE DEFINITIONS

All badges stored as constants. Badge IDs are strings.

### Mission Badges (per mission, three tiers each):

Pattern: `{missionId}-bronze`, `{missionId}-silver`, `{missionId}-gold`

Criteria:
- Bronze: Answer 10 questions in this mission
- Silver: 75% accuracy over 20 questions in this mission
- Gold: 90% accuracy over 30 questions including at least 5 Tier 3 questions

### Streak Badges:
```
streak-1:   "First Training Day"    — complete first session
streak-3:   "Three-Day Warrior"     — 3 consecutive days
streak-7:   "Week of Power"         — 7 consecutive days
streak-14:  "Unstoppable"           — 14 consecutive days
streak-30:  "Legendary Dedication"  — 30 consecutive days
```

### Comeback Badges:
```
comeback-bounce:  "Bounce Back"      — wrong answer, then 5 correct in a row
comeback-finish:  "Never Quit"       — complete session after scoring below
                                       50% in warm-up
comeback-tutor:   "Tutor's Prize"    — watch Tutor Mode, then answer correctly
```

### Milestone Badges:
```
milestone-100:    "Century Club"     — 100 questions answered total
milestone-500:    "Five Hundred"     — 500 questions answered total
milestone-first-gold: "Gold Standard" — first Gold mission badge earned
champion-complete: "Champion Hero"   — complete the Champion Mission
```

Badge check runs at every session end via `checkAndAwardBadges(sessionData)`.

---

## 11. RANK SYSTEM

```javascript
const RANKS = [
  { level: 1, title: "Rookie Hero",    threshold: 0,     unlock: "Starting hero cape" },
  { level: 2, title: "Rising Hero",    threshold: 500,   unlock: "New costume color" },
  { level: 3, title: "Bold Hero",      threshold: 1500,  unlock: "Lightning ability animation" },
  { level: 4, title: "Elite Hero",     threshold: 3500,  unlock: "Hero name generator" },
  { level: 5, title: "Champion Hero",  threshold: 7000,  unlock: "Champion Mission unlocked" },
  { level: 6, title: "Legendary Hero", threshold: 12000, unlock: "Hall of Legends + certificate" }
];
```

Rank-up check runs after PP is awarded. If `totalPP >= RANKS[rank].threshold`:
trigger rank-up animation, update rank, save data.

Champion Mission unlocks at Rank 5:
- 30 questions, mixed Math and ELA
- All Tier 2-3 questions
- No adaptive difficulty (stays challenging)
- Timed sections: 90 seconds per question (timer shown, no penalty for timeout
  — just moves on with explanation)
- Completing it with 75%+ accuracy: awards Rank 6 + champion-complete badge
  + generates printable certificate HTML page

---

## 12. POWER POINTS REFERENCE

```javascript
const PP_VALUES = {
  warmup_correct:        3,
  challenge_tier1:       5,
  challenge_tier2:       8,
  challenge_tier3:       12,
  perfect_mission_block: 25,   // all questions in block correct
  session_complete:      20,
  streak_bonus:          10,   // × streak day, max 50
  tutor_engaged:         5,    // watching Coach Hero
  badge_earned:          30,
  rank_up_bonus:         50
};
```

PP is never deducted. Incorrect answers award 0. The total only ever goes up.

---

## 13. THEME CONFIGURATION OBJECT

At the very top of the JS block:

```javascript
const THEME = {
  name: "superhero",
  appTitle: "Hero Academy",
  appSubtitle: "Where Heroes Are Made",
  worldMath: "Number Kingdom",
  worldELA: "Word Realm",
  coachName: "Coach Hero",
  coachEmoji: "🦸",
  currencyName: "Power Points",
  currencySymbol: "⚡",
  rankPrefix: "",
  rankSuffix: " Hero",
  sessionStart: "Begin Training",
  missionComplete: "Mission Complete",
  sessionEnd: "Training Complete",
  streakEmoji: "🔥",
  colors: {
    primary: "#FFD700",
    secondary: "#4FC3F7",
    bg: "#0F1B2D"
  }
  // Swap all strings here to change theme — zero other changes needed
};
```

---

## 14. JAVASCRIPT ARCHITECTURE

### 14.1 State Object (runtime, not persisted)

```javascript
let STATE = {
  currentScreen: null,
  playerData: null,        // loaded from localStorage on init
  session: {
    active: false,
    startTime: null,
    ppEarnedThisSession: 0,
    questionsAnswered: 0,
    currentMissionId: null,
    currentMissionBlock: [],  // question queue for current block
    currentQuestionIndex: 0,
    missionResults: {},
    warmupComplete: false,
    missionsComplete: 0
  },
  question: {
    current: null,
    selectedAnswer: null,
    answered: false,
    consecutiveCorrect: 0,
    consecutiveWrong: 0,
    currentTier: 1
  },
  tutor: {
    conceptId: null,
    triggeredBy: null
  },
  consecutiveWrongOnConcept: {}  // conceptId → count
};
```

### 14.2 Event Handling

Use event delegation — one listener on `#app`:

```javascript
document.getElementById('app').addEventListener('click', handleAppClick);

function handleAppClick(e) {
  const action = e.target.closest('[data-action]')?.dataset.action;
  if (!action) return;
  ACTIONS[action]?.(e.target.closest('[data-action]'));
}
```

All interactive elements use `data-action="actionName"` attributes.
Additional data passed via `data-*` attributes on the same element.

### 14.3 Render Pattern

Each screen is a pure function that returns an HTML string:

```javascript
function renderDashboard() {
  const data = STATE.playerData;
  return `
    <div class="screen" id="screen-dashboard">
      <!-- content -->
    </div>
  `;
}

function showScreen(screenName) {
  STATE.currentScreen = screenName;
  const renderers = {
    'dashboard': renderDashboard,
    'warmup': renderWarmup,
    // ...
  };
  document.getElementById('app').innerHTML = renderers[screenName]();
  window.scrollTo(0, 0);
  // Run any post-render JS (e.g., drag-drop init)
  postRender(screenName);
}
```

### 14.4 Animation Utility

```javascript
function animateValue(elementId, start, end, duration) {
  // Animates a number counting up — used for PP display
}

function flashElement(elementId, className, duration) {
  // Adds class, removes after duration — used for feedback flashes
}

function slideIn(elementId, direction = 'up') {
  // CSS class toggle for entrance animations
}
```

---

## 15. BUILD SEQUENCE FOR CLAUDE CODE

Build in this exact order. Do not skip ahead.

**Phase 1 — Shell and Design System**
1. index.html base structure
2. All CSS custom properties and global styles
3. Button, card, input, progress bar components
4. `showScreen()` router
5. localStorage helpers

**Phase 2 — Onboarding and Dashboard**
6. Onboarding screen render + validation + initial data save
7. Hero Dashboard render with real data bindings
8. Parent Dashboard (PIN prompt + full view)

**Phase 3 — Question Engine**
9. Question data structure (populate 20 sample questions across 4 missions)
10. `renderQuestion()` for multiple_choice type
11. Answer selection, CHECK ANSWER, feedback states
12. PP award and top bar live update
13. Progress tracking write to playerData
14. numeric_input type
15. drag_order type
16. sentence_select type (with passage display)

**Phase 4 — Session Flow**
17. Warm-up session manager
18. Mission challenge block manager
19. Adaptive difficulty logic
20. Tutor Mode screen
21. Mission Complete screen
22. Victory Wrap-Up screen with animations
23. Streak logic
24. Badge check and award system

**Phase 5 — Spaced Repetition**
25. Full spaced repetition engine
26. Question selection priority queue
27. Mission selection algorithm

**Phase 6 — Content Population**
28. All 428 questions across 10 missions (full content)
29. All Tutor Mode explanations (one per conceptId)
30. All passages for m2-1 and m2-2

**Phase 7 — Polish and Secondary Screens**
31. Badge Collection screen
32. Mission Map screen
33. Rank-up animations
34. Champion Mission
35. PP count-up animations throughout

**Phase 8 — QA**
36. Test full session flow start to finish
37. Test localStorage persistence across page refresh
38. Test streak logic edge cases
39. Test parent dashboard PIN
40. Verify all 428 questions have correct answers populated
41. Verify no question text is below 20px
42. Verify all buttons meet 56px minimum height
43. Test at 1024px viewport width minimum

---

## 16. THINGS TO NEVER DO

- Never use `alert()`, `confirm()`, or `prompt()` — build custom UI for all dialogs
- Never use `var` — use `const` and `let` only
- Never inline event handlers (`onclick="..."`) — use data-action pattern only
- Never hard-code colors — always use CSS custom property variables
- Never write placeholder question content — all 428 questions must be real,
  curriculum-aligned, and correctly answered
- Never show the word "Wrong" or "Incorrect" in any user-facing text
- Never use a font size below 16px anywhere in the UI
- Never deduct PP for any reason
- Never allow the app to end a session without showing Victory Wrap-Up
- Never access localStorage directly — always use savePlayerData/loadPlayerData
- Never import external JS libraries
- Never create additional files — everything stays in index.html

---

## 17. CONTENT QUALITY STANDARDS

Every question must meet all of these:

1. **Standards-aligned:** question comment includes both KY and MA standard codes
2. **Age-appropriate language:** readable by a struggling 3rd grader (Flesch-Kincaid
   Grade 3-4 for question stems)
3. **Plausible distractors:** wrong answer choices reflect common misconceptions,
   not obviously wrong random answers
4. **Correct explanation:** the `explanation` field explains WHY the correct answer
   is right, not just restates it
5. **Massachusetts ceiling:** Tier 3 questions require reasoning and explanation,
   not just recall or procedure
6. **No trick questions:** questions test understanding, not reading gotchas

Tutor Mode explanations must:
- Use simple, conversational language (speak to the child directly as "you")
- Include a concrete real-world connection or analogy
- Be accurate — no mathematical or grammatical errors
- Be encouraging in tone — frame difficulty as normal and temporary

---

*End of CLAUDE.md — Hero Academy*
*Version 1.0 | Built for a 3rd grader who is going to become a Legendary Hero.*
