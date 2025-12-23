# Dad Shift

*A short, narrative-driven game about being a new father.*

---

## 🎮 Overview

**Dad Shift** is a small, story-focused game about the first days of caring for a newborn — told from the perspective of a father who is also trying to keep his job.

The game is not about winning or optimizing stats.
It is about making imperfect choices while tired, stressed, and still showing up.

Playtime per run is **~40–50 minutes**, with multiple endings encouraging replay. Total content is designed for **2–3 hours**.

---

## 🧠 Core Themes

- Newborn care is repetitive, exhausting, and emotionally subtle
- Work does not pause when life changes
- Small decisions can echo later
- Being present matters, even when nothing improves immediately

The tone is **light, dry, and gently humorous**, avoiding melodrama or moral judgment.

---

## 📌 Design Philosophy

> This game is not about being a good or bad parent.
>
> It is about being there — or not — when it mattered.

---

## 🛠 Tech Stack

- Engine: **Godot 4.2 LTS**
- Platform: PC (initially)
- Style: Minimal UI, text-focused

---

## 🚧 Project Status

This project is in **design-first development**.

All narrative structure, systems, and endings are defined **before** implementation.

> This file is the single source of truth for design scope.
> Code starts **only after sections 1–3 are locked**.

---

## 0. Project Setup

- [x] Create GitHub repository
- [x] Create project documentation (this file)
- [x] Decide Godot version (4.2 LTS)

---

## 1. CORE DESIGN (LOCK BEFORE CODING)

### 1.1 Game Pillars

- [x] Game length: **2–3 hours total**
- [x] Tone: slice of life, dry humor, non-judgmental
- [x] Player role: **new father**
- [x] Theme: newborn care + daily work pressure

---

### 1.2 Time Structure

- [x] Total days: **5 days**
- [x] Phases per day: Morning / Afternoon / Night
- [x] End-of-day resolution logic
- [x] Save system (auto-save per phase)

---

### 1.3 Core Stats

Each stat answers a single, human question. Stats are not meant to be optimized, only to shift tone, events, and outcomes.

- [x] Energy: How much the player can still endure. Drops quickly, never causes game over, but unlocks dangerous or heavy events when low.
- [x] Stress: Accumulated pressure. Builds up easily, fades slowly. High stress changes narrative tone and event weight rather than punishing directly.
- [x] Bond (baby): Emotional connection through presence. Increases slowly when the player is physically and emotionally there. Cannot be farmed or rushed.
- [x] Job Stability: How secure the player’s work situation is. Reflects missed deadlines and performance, without leading to sudden failure or success.
- [x] Confidence: The player’s trust in themselves as a parent. Influences available choices and internal narration. Easy to lose, hard to regain.

---

### 1.4 Narrative Flags

Flags represent irreversible narrative facts — things that have happened. They are not numerical and do not reset.

- [x] delegated_baby: The player has handed over baby care during a moment of exhaustion. This opens the path to the most uncomfortable ending and cannot be undone.
- [x] stayed_present: The player chose to remain physically present despite being overwhelmed. This prevents the worst ending without granting strong advantages.
- [x] missed_work_deadline: A concrete failure at work. Influences future pressure and dialogue but does not immediately end the job.
- [x] asked_for_help: The player openly admitted needing help. Alters tone and future interactions, without solving problems outright.
- [x] accepted_help: The player accepted assistance without disappearing. Reduces stress slightly and is distinct from abandoning responsibility.
- [x] ignored_advice: The player listened but chose not to follow guidance. This subtly affects later dialogue and character reactions.

---

## 2. EVENT SYSTEM DESIGN

### 2.1 Event Model

Each event represents a small, everyday moment rather than a dramatic plot beat.

Each event must define:

- id: Unique identifier for debugging, flags, and narrative tracking.
- day: In-game day (1–5).
- phase: Morning / Afternoon / Night.
- conditions: Logical requirements based on stats and narrative flags.
- text: Short narrative description of the situation.
- choices: 2–3 imperfect options presented to the player.
- effects: Stat changes, flag updates, or hidden consequences.

- [x] Decide data format (JSON / external data)

Design rules:

Events do not solve problems permanently.

No explicit “good” or “bad” choices.

Consequences may be delayed or understated.

Some events exist only to change tone or internal state.

---

### 2.2 Event Categories

Events are grouped by emotional function, not by gameplay mechanics.
Categories help control pacing, tone, and player fatigue.

#### Baby Care

Events focused on direct interaction with the newborn.

**Design intent:**

- Repetitive, tiring, emotionally subtle
- Often drains Energy while increasing Bond
- Rarely feel "rewarding" in the moment
- Serve as the emotional backbone of the game

**Typical situations:**

- Feeding
- Crying at inconvenient times
- Holding the baby while nothing happens

---

#### Work Pressure

Events representing ongoing job responsibilities that do not pause.

**Design intent:**

- Create constant background anxiety
- Affect Job Stability and Stress
- Rarely allow full success, only damage control
- Force trade-offs against presence at home

**Typical situations:**

- Messages from coworkers
- Missed or rushed deadlines
- Working while distracted or exhausted

---

#### Family / NPC Interaction

Events involving people around the player.

**Design intent:**

- Provide contrast, advice, or quiet judgment
- Influence Confidence and narrative flags
- No NPC is purely right or wrong
- Advice may help, harm, or simply add pressure

**Typical NPCs:**

- Partner
- Parents / in-laws
- Friends or coworkers

---

#### Quiet Downtime

Low-stakes moments with minimal mechanical impact.

**Design intent:**

- Let the player breathe
- Reinforce tone and atmosphere
- Occasionally unlock introspective narration
- Prevent emotional overload

**Typical situations:**

- Sitting alone at night
- Watching the baby sleep
- Brief, calm moments without decisions

---

#### Category Rules

- Each phase should usually contain only one dominant category
- Baby care events appear most frequently
- Quiet downtime events are intentionally rare
- Category balance shifts slightly across days

---

### 2.3 Key Narrative Events

Key Narrative Events are structural anchors.
They are rare, carefully placed, and irreversible.

These events do not exist to surprise the player mechanically —
they exist to quietly decide what kind of story this run becomes.

#### Delegated Baby (Danger Path)

A critical moment where the player chooses to hand over newborn care due to exhaustion or pressure.

**Design intent:**

- Represents absence rather than failure
- Often framed as a "reasonable" or "temporary" decision
- Sets the `delegated_baby` narrative flag
- Cannot be undone or compensated for later

**Design rules:**

- Never presented as a dramatic choice
- Often occurs when Energy is low and Stress is high
- Immediately feels relieving, later feels empty
- This event unlocks the path toward The Quiet Mistake ending

---

#### Stayed Present (Avoid Danger)

A counterpoint to Delegated Baby.

The player chooses to remain physically present despite exhaustion, confusion, or lack of progress.

**Design intent:**

- Not a "heroic" act
- Does not significantly improve stats
- Sets the `stayed_present` narrative flag
- Prevents the most uncomfortable ending without guaranteeing a good one

**Design rules:**

- Choice text avoids emotional language
- Often feels pointless in the moment
- Quietly closes off the danger path

---

#### No Baby in Room (Follow-up)

A delayed consequence event.

The player enters a room expecting to find the baby, but the space is empty.

**Design intent:**

- Emotional discomfort without explicit explanation
- Forces the player to remember earlier decisions
- Does not set new flags
- Changes internal narration tone

**Design rules:**

- Can only occur if `delegated_baby` is true
- No meaningful choices are offered
- Ends quickly, without resolution

---

#### Baby Breathing (Emotional Cooldown)

A quiet grounding event.

The player checks on the baby late at night and listens to their breathing.

**Design intent:**

- Reduce emotional overload
- Lower Stress slightly
- Reinforce presence without reward
- Serve as contrast after heavy narrative beats

**Design rules:**

- No stat optimization opportunity
- Minimal text
- No branching choices
- Often follows high-stress phases

---

#### Narrative Event Rules

- Key Narrative Events appear at most once per run
- They are never marked as special or important in-game
- Their impact is felt later, not immediately

---

## 3. ENDING SYSTEM

### 3.1 Endings

Endings reflect how the player lived through the days, not how well they optimized stats.

They are quiet summaries, not rewards or punishments.

#### Still Awake

The player survives the five days in a constant state of exhaustion.

**Characteristics:**

- Energy is consistently low
- Stress is high but not catastrophic
- No strong narrative flags dominate

**Tone:**

Foggy. Unresolved. Time feels blurred together.

This ending represents endurance without reflection.

---

#### Functional Adult

The player keeps things running.

**Characteristics:**

- Job Stability remains mostly intact
- Baby care is handled efficiently
- Emotional presence is limited

**Tone:**

Practical. Controlled. Slightly detached.

Life continues, but something remains unaddressed.

---

#### Present Father

The player chooses presence over optimization.

**Characteristics:**

- `stayed_present` flag is set
- Bond is steadily built through repeated presence
- Work suffers slightly but does not collapse

**Tone:**

Quietly hopeful. Grounded. Human.

This ending does not promise ease — only meaning.

---

#### On Autopilot

The player moves through routines without engagement.

**Characteristics:**

- Confidence is low
- Choices prioritize damage control
- No major narrative flags are set

**Tone:**

Numb. Mechanical. Emotionally distant.

Nothing breaks. Nothing connects.

---

#### The Quiet Mistake

An ending defined by absence.

**Characteristics:**

- `delegated_baby` flag is set
- Follow-up events reinforce emotional distance
- Stats may appear stable or even "good"

**Tone:**

Uncomfortable. Hollow. Unspoken.

This ending avoids drama. That is what makes it difficult.

---

#### It Gets Easier (Hidden)

A rare, non-obvious ending.

**Characteristics:**

- Presence is chosen repeatedly
- Help is accepted without disappearance
- No single stat is maximized

**Tone:**

Gentle. Open-ended. Forward-looking.

This ending is not about success —
it is about believing that the days ahead may feel lighter.

---

#### Ending Rules

- Endings are never labeled as good or bad
- No numerical score is shown
- The ending text is short and restrained
- Music and visuals remain minimal

---

### 3.2 Ending Logic

Ending selection is based on narrative patterns, not raw stat totals.

The system evaluates flags first, then stat tendencies, then falls back to neutral outcomes.

#### Priority Rules

- Narrative flags override stats
- Absence matters more than performance
- No ending requires perfect stats
- Only one ending is selected per run

#### Stat Thresholds

Stats are assumed to range 0–100. Thresholds for evaluation:

- **High**: 70+
- **Medium**: 40–70
- **Low**: <40

#### Ending Conditions

##### The Quiet Mistake *(Highest Priority)*

Triggered if:

- `delegated_baby == true`

Notes:

- This ending ignores positive stats
- Once unlocked, no other ending can override it
- Follow-up events reinforce this path silently

---

##### It Gets Easier *(Hidden)*

Triggered if all conditions are met:

- `stayed_present == true`
- `accepted_help == true`
- `delegated_baby == false`
- No stat is at extreme high (>80) or low (<30)

Notes:

- This ending is intentionally rare
- Cannot be brute-forced through optimization
- Requires emotional balance rather than success

---

##### Present Father

Triggered if:

- `stayed_present == true`
- `delegated_baby == false`
- Bond >= Medium

Notes:

- Does not require high Bond, only consistent presence
- Small work failures are allowed
- Presence is valued over consistency

---

##### Functional Adult

Triggered if:

- Job Stability >= High
- Bond < Medium
- No presence-related flags dominate

Notes:

- Represents functional survival
- Does not imply emotional resolution
- Work is maintained at the cost of connection

---

##### On Autopilot

Triggered if:

- Confidence < Low
- No major narrative flags are set
- Choices consistently minimize immediate damage

Notes:

- A default path for disengaged play
- Often reached unintentionally
- Represents emotional shutdown

---

##### Still Awake *(Fallback Ending)*

Triggered if:

- No other ending conditions are met

Notes:

- Acts as the emotional baseline
- Represents endurance without direction
- Energy is typically consistently low

---

#### Tie-Breaking Rules

- Flag-based endings always win over stat-based endings
- Hidden ending is checked after flag safety, before stat endings
- If multiple stat endings qualify, choose the one matching the dominant stat trend

#### Design Constraints

- Ending logic is evaluated once, at the end of Day 5
- Players are not informed how endings are calculated
- No ending screen displays stats or flags
- Ending text must stand on its own without explanation

---

## 4. FLOW & BALANCE

### 4.1 Days 1–3 (Learning Phase)

Days 1–3 form a deliberately forgiving learning phase.

The goal is not to teach mechanics explicitly, but to let the player feel how choices trade comfort, presence, and control.

This phase establishes emotional expectations without committing the player to irreversible paths.

#### Emotional Goals

- Let the player feel tired, but not hopeless
- Introduce pressure without consequence escalation
- Normalize imperfection and small failures
- Build trust that the game will not punish experimentation

#### Stat Introduction Rules

Stats are introduced gradually and asymmetrically:

- Energy and Stress are immediately visible and reactive
- Bond grows slowly and subtly, without clear feedback
- Job Stability changes are small and reversible
- Confidence shifts mainly through tone and internal narration

No stat can reach a critical or extreme threshold during this phase.

#### Event Design Rules

- Events are shorter and more contained
- Most events have only 2 choices
- Choices feel inconvenient rather than dangerous
- Consequences are immediate but mild

**Examples of acceptable consequences:**

- Temporary Energy loss
- Small Stress increase
- Slight tone changes in narration

**Examples of forbidden consequences:**

- Irreversible narrative flags
- Ending path locks
- Severe stat drops

#### Choice Philosophy

During Days 1–3:

- Every choice has a cost
- No choice feels correct in hindsight
- The player is allowed to regret decisions safely

Choices should teach that:

- Rest may increase pressure elsewhere
- Working may reduce presence without disaster
- Doing nothing still costs something

#### Narrative Flag Restrictions

The following flags must **not** be set during Days 1–3:

- `delegated_baby`
- Any ending-critical or danger-path flags

Flags that **may** appear:

- `asked_for_help`
- `ignored_advice`

These flags influence tone only and do not redirect the narrative.

#### Player Failure Handling

Failure is framed as fatigue, not incompetence.

- Missed tasks are acknowledged quietly
- No explicit scolding or dramatic escalation
- The game avoids moral language

The player should feel:

*"This is hard, but I'm still allowed to continue."*

#### Transition to Day 4

By the end of Day 3, the player should:

- Understand that optimization is impossible
- Recognize recurring emotional patterns
- Feel mentally tired but not trapped

Day 4 is allowed to feel heavier because Days 1–3 were gentle.

#### Design Lock

- Days 1–3 are never allowed to trigger ending logic
- No hidden danger paths are introduced
- All pressure remains recoverable
- This phase exists to teach how the game feels, not how it ends

---

### 4.2 Days 4–5 (Pressure Phase)

Days 4–5 remove the safety net established earlier.

This phase is designed to feel narrower, heavier, and less forgiving — not because the player made mistakes, but because exhaustion accumulates.

#### Emotional Goals

- Convey long-term fatigue rather than sudden crisis
- Make absence feel tempting and reasonable
- Increase emotional weight without melodrama
- Let consequences feel inevitable rather than punitive

#### Stress & Energy Baseline

- Stress starts higher at the beginning of each phase
- Energy recovers less between phases
- Rest options are weaker or come with trade-offs

Fatigue is no longer something that can be fully managed.

#### Event Design Rules

- Events are longer and more layered
- Most events present 3 choices
- At least one choice is emotionally uncomfortable
- Safe, neutral options become rare

Some choices now trade:

- Presence for relief
- Control for stability
- Short-term ease for long-term distance

#### Introduction of Irreversible Flags

Days 4–5 are the only phase where danger-path flags may be set.

Allowed flags include:

- `delegated_baby`
- `stayed_present`

Design rules:

- Flags are never framed as dramatic turning points
- Choices leading to flags feel practical or exhausted
- The significance of flags is not explained to the player
- Once set, these flags cannot be undone

#### Choice Pressure Rules

During Days 4–5:

- Some choices are locked or unavailable due to low Energy or high Stress
- Previously safe habits begin to fail
- Inaction becomes a meaningful decision

The player is gently pushed toward:

- Delegating
- Withdrawing
- Or staying present at a personal cost

#### Narrative Tone Shift

- Internal narration becomes shorter and drier
- Humor is sparser and more muted
- Events reference earlier days indirectly

Nothing explicitly states that things are worse —
they simply stop resetting.

#### Failure & Consequence Handling

- Mistakes are acknowledged through absence or silence
- No explicit punishment screens or warnings
- The game avoids sudden escalation
- Consequences are allowed to linger without resolution

#### Preparation for Ending Evaluation

By the end of Day 5, the game ensures:

- At least one major narrative pattern is established
- The player recognizes the weight of earlier choices
- Endings feel earned, not surprising

#### Design Lock

- Days 4–5 are the only days allowed to set ending-critical flags
- The game never forces a specific ending
- Pressure increases, but agency remains

This phase exists to ask:

*"When it stopped being manageable, what did you do?"*

---

### 4.3 Replay Design

Replay value in Dad Shift comes from reinterpretation, not content volume.

Each run reveals new context for past decisions rather than new mechanics.

#### Run Length & Structure

- A single run lasts ~40–50 minutes
- The structure remains identical across runs
- No content is gated behind difficulty or progress

The player replays to understand, not to unlock.

#### Decision Echoes

- Early choices subtly affect later tone and options
- Small decisions reappear as altered dialogue or framing
- Players often recognize consequences only in hindsight

Echoes are designed to feel:

- Quiet
- Personal
- Unavoidable

#### Hidden Consequences

- Most consequences are delayed
- Players are rarely told when something "matters"
- Day 5 reveals patterns, not surprises

The game avoids:

- Explicit foreshadowing
- Pop-up warnings
- Retroactive explanations

#### Ending Recontextualization

- Endings reframe earlier events rather than summarize stats
- Players are expected to mentally replay past moments
- Different endings change the meaning of the same scenes

A second run often feels like:

*"Oh. That's what that was."*

#### Intentional Ambiguity

- The game does not confirm optimal paths
- Some outcomes remain emotionally ambiguous
- Not every question receives closure

This ambiguity encourages reflection rather than mastery.

#### Design Constraints

- Replay should never feel mandatory
- No new mechanics are introduced on repeat runs
- Content reuse is deliberate and thematically consistent

Replay exists to deepen understanding, not extend playtime.

#### Design Lock

- Each run is complete on its own
- Replay does not invalidate previous endings
- The player's interpretation is respected

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
>
> This game is not about winning.
> It is about staying present.
