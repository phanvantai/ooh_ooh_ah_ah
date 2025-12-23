# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Dad Shift** is a narrative-driven game about the first days of caring for a newborn while managing work pressure. Built with Godot 4.2 LTS, the game focuses on emotional authenticity over mechanical optimization.

**Key Design Principles:**

- Design-first development: all narrative structure, systems, and endings are defined before implementation
- The README.md is the single source of truth for design scope
- Implementation begins only after core design sections are locked
- The game is about making imperfect choices, not winning or optimizing

## Project Status

Currently in **design-first development** phase. The project has:

- ✅ Complete narrative design and event system specification
- ✅ Full ending logic and branching paths defined
- ✅ Godot 4.2 project initialized
- ⏳ Awaiting core implementation (Sections 5-8 in README.md)

## Development Setup

**Engine:** Godot 4.2 (Forward Plus renderer)

**Project Name:** "Ooh Ooh Ah Ah" (working title, game title is "Dad Shift")

The Godot project has been initialized with:

- `project.godot` configuration file
- Default `icon.svg`
- `.gitignore` configured for Godot 4+
- No scenes or scripts created yet

**Next Steps:**
Follow the technical implementation checklist in Section 5 of README.md:

1. Create scene structure (Main loop, Event UI, Stat display, Ending scene)
2. Implement GameState singleton
3. Build event loader and flag manager
4. Implement save/load system

**Recommended Folder Structure:**

```text
/scenes/           # .tscn files
  /ui/             # UI scenes (event display, stats, menus)
  /main/           # Main game loop scene
  /endings/        # Ending scenes
/scripts/          # .gd files
  /singletons/     # Autoload scripts (GameState, etc.)
  /managers/       # Event loader, flag manager, save system
  /ui/             # UI controller scripts
/data/             # Event data files (JSON)
  /events/         # Event definitions by day/category
/assets/           # Fonts, minimal graphics if needed
```

## Step-by-Step Implementation Guide

### Phase 1: Setup and Core Systems (Foundation)

**Goal:** Build the invisible foundation that tracks game state.

**Step 1.1: Create folder structure**

- Create all the folders listed above
- This organizes your code from the start

**Step 1.2: Build GameState singleton (the brain of the game)**

This script will track everything: stats, flags, current day/phase, and handle saving.

Create `scripts/singletons/game_state.gd`:

```gdscript
extends Node

# Stats (0-100 range)
var energy: int = 70
var stress: int = 30
var bond: int = 40
var job_stability: int = 60
var confidence: int = 50

# Narrative flags
var delegated_baby: bool = false
var stayed_present: bool = false
var missed_work_deadline: bool = false
var asked_for_help: bool = false
var accepted_help: bool = false
var ignored_advice: bool = false

# Time tracking
var current_day: int = 1  # 1-5
var current_phase: String = "morning"  # morning, afternoon, night

# Game flow
signal stats_changed
signal phase_completed
signal day_completed

func next_phase():
    match current_phase:
        "morning":
            current_phase = "afternoon"
        "afternoon":
            current_phase = "night"
        "night":
            current_day += 1
            current_phase = "morning"
            day_completed.emit()

    phase_completed.emit()
    auto_save()

func modify_stat(stat_name: String, amount: int):
    match stat_name:
        "energy": energy = clamp(energy + amount, 0, 100)
        "stress": stress = clamp(stress + amount, 0, 100)
        "bond": bond = clamp(bond + amount, 0, 100)
        "job_stability": job_stability = clamp(job_stability + amount, 0, 100)
        "confidence": confidence = clamp(confidence + amount, 0, 100)

    stats_changed.emit()

func set_flag(flag_name: String, value: bool = true):
    match flag_name:
        "delegated_baby": delegated_baby = value
        "stayed_present": stayed_present = value
        "missed_work_deadline": missed_work_deadline = value
        "asked_for_help": asked_for_help = value
        "accepted_help": accepted_help = value
        "ignored_advice": ignored_advice = value

func auto_save():
    # Placeholder - implement later in Phase 2
    pass
```

**Step 1.3: Register GameState as autoload**

1. In Godot editor: Project > Project Settings > Autoload tab
2. Add `scripts/singletons/game_state.gd`
3. Name it "GameState"
4. Click "Add"

Now `GameState` is accessible from anywhere in your game!

---

### Phase 2: Event System (The Heart)

**Goal:** Load and display events with choices.

**Step 2.1: Create a sample event data file**

Create `data/events/day1_morning.json`:

```json
[
  {
    "id": "day1_morning_wake",
    "day": 1,
    "phase": "morning",
    "conditions": {},
    "text": "The baby's cry cuts through the fog of sleep. 4:47 AM.\n\nYou haven't slept more than two hours straight in three days.",
    "choices": [
      {
        "text": "Get up immediately",
        "effects": {
          "stats": {"energy": -5, "bond": 3}
        }
      },
      {
        "text": "Wait a moment. Maybe they'll settle.",
        "effects": {
          "stats": {"energy": 2, "stress": 5}
        }
      }
    ]
  }
]
```

**Step 2.2: Create EventManager**

Create `scripts/managers/event_manager.gd`:

```gdscript
extends Node

var all_events: Array = []
var event_history: Array = []  # Track which events were shown

func _ready():
    load_all_events()

func load_all_events():
    # Load all event JSON files from data/events/
    var event_files = [
        "res://data/events/day1_morning.json",
        # Add more as you create them
    ]

    for file_path in event_files:
        var file = FileAccess.open(file_path, FileAccess.READ)
        if file:
            var json_text = file.get_as_text()
            var json = JSON.new()
            var error = json.parse(json_text)
            if error == OK:
                var events = json.data
                all_events.append_array(events)
            file.close()

func get_next_event() -> Dictionary:
    # Find events matching current day/phase and conditions
    var valid_events = []

    for event in all_events:
        if event.day == GameState.current_day and event.phase == GameState.current_phase:
            if check_conditions(event.conditions):
                if not event.id in event_history:  # Don't repeat events
                    valid_events.append(event)

    if valid_events.size() > 0:
        return valid_events[0]  # Return first valid event (or randomize later)
    else:
        return {}  # No event found

func check_conditions(conditions: Dictionary) -> bool:
    # Check if stat/flag conditions are met
    if conditions.is_empty():
        return true

    # Add condition checking logic here
    # For now, return true
    return true

func apply_effects(effects: Dictionary):
    if effects.has("stats"):
        for stat_name in effects.stats:
            GameState.modify_stat(stat_name, effects.stats[stat_name])

    if effects.has("flags"):
        for flag_name in effects.flags:
            GameState.set_flag(flag_name, true)
```

Register this as autoload named "EventManager".

---

### Phase 3: User Interface (What Players See)

**Goal:** Create screens to display events and stats.

**Step 3.1: Create the Event Display scene**

1. In Godot: Scene > New Scene
2. Add a `Control` node as root, name it "EventDisplay"
3. Add child nodes:
   - `Label` (name: "EventText") - for displaying event narrative
   - `VBoxContainer` (name: "ChoicesContainer") - to hold choice buttons
   - `Label` (name: "StatsLabel") - to show current stats

Layout them in the editor (simple vertical layout is fine).

**Step 3.2: Create script for EventDisplay**

Attach script to EventDisplay node (`scripts/ui/event_display.gd`):

```gdscript
extends Control

@onready var event_text = $EventText
@onready var choices_container = $ChoicesContainer
@onready var stats_label = $StatsLabel

var current_event: Dictionary

func _ready():
    GameState.stats_changed.connect(update_stats_display)
    show_next_event()

func show_next_event():
    clear_choices()

    current_event = EventManager.get_next_event()

    if current_event.is_empty():
        # No more events, advance phase
        GameState.next_phase()
        show_next_event()
        return

    event_text.text = current_event.text

    # Create buttons for each choice
    for choice in current_event.choices:
        var button = Button.new()
        button.text = choice.text
        button.pressed.connect(_on_choice_selected.bind(choice))
        choices_container.add_child(button)

    update_stats_display()

func _on_choice_selected(choice: Dictionary):
    EventManager.apply_effects(choice.effects)
    EventManager.event_history.append(current_event.id)
    show_next_event()

func clear_choices():
    for child in choices_container.get_children():
        child.queue_free()

func update_stats_display():
    stats_label.text = "Energy: %d | Stress: %d | Bond: %d | Job: %d | Confidence: %d" % [
        GameState.energy,
        GameState.stress,
        GameState.bond,
        GameState.job_stability,
        GameState.confidence
    ]
```

**Step 3.3: Set EventDisplay as the main scene**

- Scene > Project Settings > Application > Run > Main Scene
- Select your EventDisplay.tscn

---

### Phase 4: Content Creation (Writing Events)

**Goal:** Write all the events for Days 1-5.

This is where your README.md design comes to life!

**Step 4.1: Create event files**

- `data/events/day1_morning.json`
- `data/events/day1_afternoon.json`
- `data/events/day1_night.json`
- ... (continue for all 5 days × 3 phases = 15 files)

**Step 4.2: Follow the design rules from README.md:**

- Days 1-3: Learning phase, mild consequences
- Days 4-5: Pressure phase, can set critical flags
- Follow event categories: Baby Care, Work Pressure, Family, Quiet Downtime
- Each event needs id, day, phase, conditions, text, choices, effects

---

### Phase 5: Endings (The Payoff)

**Goal:** Evaluate player's journey and show appropriate ending.

**Step 5.1: Create EndingEvaluator**

Create `scripts/managers/ending_evaluator.gd`:

```gdscript
extends Node

func get_ending() -> String:
    # Priority 1: The Quiet Mistake
    if GameState.delegated_baby:
        return "quiet_mistake"

    # Priority 2: It Gets Easier (hidden)
    if (GameState.stayed_present and GameState.accepted_help
        and not GameState.delegated_baby
        and all_stats_balanced()):
        return "it_gets_easier"

    # Priority 3: Present Father
    if GameState.stayed_present and not GameState.delegated_baby and GameState.bond >= 40:
        return "present_father"

    # Priority 4: Functional Adult
    if GameState.job_stability >= 70 and GameState.bond < 40:
        return "functional_adult"

    # Priority 5: On Autopilot
    if GameState.confidence < 40:
        return "on_autopilot"

    # Fallback: Still Awake
    return "still_awake"

func all_stats_balanced() -> bool:
    var stats = [GameState.energy, GameState.stress, GameState.bond,
                 GameState.job_stability, GameState.confidence]
    for stat in stats:
        if stat > 80 or stat < 30:
            return false
    return true
```

**Step 5.2: Create ending scene**

1. Create new scene with `Control` root, name it "EndingScreen"
2. Add `Label` for ending text
3. Add script:

```gdscript
extends Control

@onready var ending_text = $EndingText

var endings = {
    "quiet_mistake": "You don't remember when it started feeling this quiet.\n\nThe days passed. Things worked.\n\nBut something is missing that you can't name.",
    "it_gets_easier": "Day 6 arrives.\n\nYou're still tired. But something feels... lighter.\n\nMaybe it does get easier.",
    "present_father": "You were there.\n\nNot perfectly. Not without cost.\n\nBut you were there.",
    "functional_adult": "Everything kept running.\n\nThe job. The routines. The days.\n\nYou're managing. That counts for something.",
    "on_autopilot": "You made it through.\n\nOne foot in front of the other.\n\nNumb, but functional.",
    "still_awake": "Day 6.\n\nYou're still standing. Barely.\n\nThat's something."
}

func _ready():
    var evaluator = load("res://scripts/managers/ending_evaluator.gd").new()
    var ending_key = evaluator.get_ending()
    ending_text.text = endings[ending_key]
```

**Step 5.3: Trigger ending after Day 5**

In EventDisplay.gd, modify `show_next_event()`:

```gdscript
func show_next_event():
    # Check if game is over
    if GameState.current_day > 5:
        get_tree().change_scene_to_file("res://scenes/endings/ending_screen.tscn")
        return

    # ... rest of existing code
```

---

### Phase 6: Polish and Testing

**Step 6.1: Add save/load system** (optional but recommended)
**Step 6.2: Test all endings are reachable**
**Step 6.3: Balance stat changes**
**Step 6.4: Proofread all event text**

---

## Quick Start Commands

When working on this project:

- **Open in Godot**: Double-click `project.godot` or launch Godot and open this folder
- **Run game**: Press F5 in Godot editor
- **Test specific scene**: Press F6 while editing a scene
- **View errors**: Check the bottom "Output" panel in Godot

## Common Godot Concepts

- **Scenes (.tscn)**: Reusable collections of nodes (like UI screens)
- **Scripts (.gd)**: Code written in GDScript (similar to Python)
- **Nodes**: Building blocks of scenes (Label, Button, Control, etc.)
- **Signals**: Events that nodes can emit (like button.pressed)
- **Autoload**: Scripts that exist throughout the entire game (like GameState)

## Core Architecture

### Game Structure

**Time System:**

- 5 days total
- 3 phases per day: Morning / Afternoon / Night
- Auto-save per phase
- ~40-50 minutes per playthrough

**Stats System:**
The game uses 5 core stats that influence narrative tone and available choices rather than creating win/fail states:

- **Energy**: Player endurance (drops quickly, never causes game over)
- **Stress**: Accumulated pressure (builds easily, fades slowly)
- **Bond**: Emotional connection with baby (grows slowly through presence)
- **Job Stability**: Work security (reflects missed deadlines)
- **Confidence**: Self-trust as a parent (influences choices and narration)

**Narrative Flags:**
Irreversible boolean flags representing critical story moments:

- `delegated_baby`: Player handed over baby care (opens path to "The Quiet Mistake" ending)
- `stayed_present`: Player remained present despite exhaustion (prevents worst ending)
- `missed_work_deadline`: Concrete work failure
- `asked_for_help`: Player admitted needing help
- `accepted_help`: Player accepted assistance without abandoning responsibility
- `ignored_advice`: Player chose not to follow guidance

### Event System

Events are the core gameplay unit. Each event defines:

- Unique identifier
- Day and phase timing
- Conditions (stat thresholds and flag requirements)
- Narrative text
- 2-3 imperfect choices
- Stat/flag effects

**Event Categories:**

- Baby Care (most frequent, emotionally subtle)
- Work Pressure (constant background anxiety)
- Family/NPC Interaction (advice and judgment)
- Quiet Downtime (emotional pacing, rare)

**Critical Design Rules:**

- Events never solve problems permanently
- No explicit "good" or "bad" choices
- Consequences may be delayed or understated
- Days 1-3: Forgiving learning phase, no ending-critical flags
- Days 4-5: Pressure phase where danger-path flags can be set

### Ending System

**6 possible endings** determined by narrative patterns, not raw stats:

1. **The Quiet Mistake** (highest priority) - Requires `delegated_baby == true`
2. **It Gets Easier** (hidden) - Requires presence + accepting help + balance
3. **Present Father** - Requires `stayed_present == true` + medium bond
4. **Functional Adult** - High job stability, low bond
5. **On Autopilot** - Low confidence, disengaged play
6. **Still Awake** (fallback) - Endurance without direction

**Evaluation Priority:**

1. Narrative flags (override everything)
2. Hidden ending conditions
3. Stat-based endings
4. Fallback ending

**Stat Thresholds:**

- High: 70+
- Medium: 40-70
- Low: <40

## Technical Implementation Guide

When implementing in Godot, follow Section 5 of README.md:

**Required Godot Components:**

1. Scene Structure: Main loop, Event UI, Stat display UI, Ending scene
2. Data & State: GameState singleton, Event loader, Flag manager, Save/Load system

**Event Data Format:**
Events should be stored in external data files (JSON recommended) with structure:

```
{
  "id": "unique_event_id",
  "day": 1-5,
  "phase": "morning|afternoon|night",
  "conditions": {stat/flag requirements},
  "text": "narrative description",
  "choices": [2-3 choice objects],
  "effects": {stat changes, flag updates}
}
```

## Design Constraints

**What NOT to do:**

- Never create events that solve problems permanently
- Never set `delegated_baby` flag during Days 1-3
- Never display numerical scores or stat values in ending screens
- Never mark choices as "good" or "bad"
- Never explain ending logic to players
- Never use dramatic language for critical narrative moments
- Never allow stats to reach extreme thresholds during Days 1-3

**Tone Guidelines:**

- Light, dry, gently humorous
- Avoid melodrama or moral judgment
- Keep event text short and understated
- Internal narration should feel tired but human

## Content Production

When writing events and endings (Section 6):

1. Draft event text following the 4 event categories
2. Apply tone & humor pass (dry, non-judgmental)
3. Polish ending text (short, restrained, no stats shown)

**UI/UX Requirements:**

- Minimal UI design
- Subtle stat feedback (no exact numbers recommended)
- No visual overload
- Text-focused presentation

## Testing & Validation

Before finalizing (Section 7):

- Full playtest of single run
- Test different play styles (optimization vs presence vs autopilot)
- Verify emotional pacing across all 5 days
- Ensure all 6 endings are reachable
- Confirm Days 1-3 never trigger danger flags

## Common Mistakes to Avoid

1. **Implementing before design is locked**: Sections 1-3 of README.md must be complete before coding
2. **Making choices feel punitive**: Choices should feel inconvenient, not wrong
3. **Explaining too much**: Let players discover consequences through replay
4. **Breaking flag restrictions**: Never allow ending-critical flags in Days 1-3
5. **Overcomplicating the event system**: Keep it simple - conditions, text, choices, effects
