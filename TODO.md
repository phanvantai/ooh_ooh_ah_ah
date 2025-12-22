# 🧩 GAME TODO LIST
*(Working title: Dad Shift)*

> This file is the single source of truth for design scope.
> Code starts **only after Section 1–3 are locked**.

---

## 0. Project Setup
- [x] Create GitHub repository
- [ ] Add README.md (game vision & pillars)
- [x] Add TODO.md (this file)
- [ ] Decide Godot version (4.x)

---

## 1. CORE DESIGN (LOCK BEFORE CODING)

### 1.1 Game Pillars
- [ ] Game length: **2–3 hours total**
- [ ] Tone: slice of life, dry humor, non-judgmental
- [ ] Player role: **new father**
- [ ] Theme: newborn care + daily work pressure

---

### 1.2 Time Structure
- [ ] Total days: **5 days**
- [ ] Phases per day: Morning / Afternoon / Night
- [ ] End-of-day resolution logic
- [ ] Save system (auto-save per phase)

---

### 1.3 Core Stats
- [ ] Energy
- [ ] Stress
- [ ] Bond (baby)
- [ ] Job Stability
- [ ] Confidence

---

### 1.4 Narrative Flags
- [ ] delegated_baby
- [ ] stayed_present
- [ ] missed_work_deadline
- [ ] asked_for_help
- [ ] accepted_help
- [ ] ignored_advice

---

## 2. EVENT SYSTEM DESIGN

### 2.1 Event Model
Each event must define:
- id
- day
- phase
- conditions
- text
- choices
- effects

- [ ] Decide data format (JSON / Resource)

---

### 2.2 Event Categories
- [ ] Baby care
- [ ] Work pressure
- [ ] Family / NPC interaction
- [ ] Quiet downtime

---

### 2.3 Key Narrative Events
- [ ] Delegated Baby (danger path)
- [ ] Stayed Present (avoid danger)
- [ ] No Baby in Room (follow-up)
- [ ] Baby Breathing (emotional cooldown)

---

## 3. ENDING SYSTEM

### 3.1 Endings
- [ ] Still Awake
- [ ] Functional Adult
- [ ] Present Father
- [ ] On Autopilot
- [ ] The Quiet Mistake
- [ ] It Gets Easier (hidden)

---

### 3.2 Ending Logic
- [ ] Define conditions for each ending
- [ ] No explicit good/bad labels
- [ ] Lock input before ending
- [ ] Minimalist ending text

---

## 4. FLOW & BALANCE

### 4.1 Days 1–3 (Learning Phase)
- [ ] Forgiving events
- [ ] Introduce stats gradually
- [ ] Teach "no perfect choice"

---

### 4.2 Days 4–5 (Pressure Phase)
- [ ] Higher stress baseline
- [ ] Fewer safe options
- [ ] Introduce irreversible flags

---

### 4.3 Replay Design
- [ ] Decisions echo later days
- [ ] Hide consequences until Day 5
- [ ] ~40–50 minutes per run

---

## 5. TECH (GODOT)

### 5.1 Scene Structure
- [ ] Main loop
- [ ] Event UI
- [ ] Stat display UI
- [ ] Ending scene

---

### 5.2 Data & State
- [ ] GameState singleton
- [ ] Event loader
- [ ] Flag manager
- [ ] Save / Load system

---

## 6. CONTENT PRODUCTION

### 6.1 Writing
- [ ] Event text – draft
- [ ] Event text – tone & humor pass
- [ ] Ending text polish

---

### 6.2 UI / UX
- [ ] Minimal UI
- [ ] Subtle stat feedback
- [ ] No visual overload

---

## 7. FINAL PASS
- [ ] Full playtest (single run)
- [ ] Playtest (different play styles)
- [ ] Emotional pacing check
- [ ] Scope trim
- [ ] Lock content

---

## 8. OPTIONAL / POST-LAUNCH
- [ ] Extra endings
- [ ] New events
- [ ] Localization

---

## 📌 DESIGN NOTE
> This game is not about winning.
> It is about staying present.
