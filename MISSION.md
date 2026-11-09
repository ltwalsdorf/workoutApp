# Mission

**Make it effortless to know what to do in the gym today, do it, and remember how it went.**

This is a personal, phone-only workout tracker. It replaces the notes app and the mental math: open it, it tells you which workout is next in your routine, shows what you lifted last time, and lets you log today's numbers with as few taps as possible. Everything lives on the device. No accounts, no servers, no social feed.

---

## Who it's for

One person lifting on a repeating program (e.g. Push / Pull / Legs, Upper / Lower) who wants to:

- Walk into the gym and immediately know which workout is next
- See last session's weight or time for each exercise before starting it
- Log today's sets quickly, even mid-set with sweaty hands
- Look back at what they did and how often

## Guiding principles

1. **Fast in the gym.** The active-workout screen matters most. Big tap targets, minimal typing, last-known values pre-filled so the common case is "tap to confirm."
2. **Never lose a workout.** An in-progress workout persists through leaving the screen, closing the app, or the phone restarting. A workout is only finished when the user says it is.
3. **Flexible, not fussy.** Exercises can track only what makes sense for them (sets only, reps, weight, time). Nothing is required beyond the number of sets.
4. **Local and private.** All data is stored on the device. No sign-in, no network dependency. It works in a basement gym with no signal.
5. **Templates are the plan; logs are the truth.** Editing a template never rewrites history. Logged workouts record what actually happened.

---

## Core concepts

| Concept | What it is |
|---|---|
| **Exercise** | A named movement (e.g. "Leg Extension"). Remembers its most recent logged values across all workouts. |
| **Template** | A saved workout plan: an ordered list of exercises with targets, plus notes. Can be favorited and assigned a category. |
| **Category** | A label for grouping templates (Upper, Lower, Push, Pull, etc.). User-defined. |
| **Routine** | An ordered cycle of templates (e.g. Push → Pull → Legs → repeat). Drives the "next recommended workout." |
| **Workout (log)** | One performed session on a given date, created from a template (or from scratch). Holds actual sets/reps/weight/time, completion state, order, and notes. |

### Exercise tracking design

Every exercise always has **sets**. Each exercise then turns on any combination of three optional metrics:

- **Reps**
- **Weight** (lbs by default; unit is a setting)
- **Time** (duration, e.g. 0:45)

This covers every case in the spec with one model:

| Example | Sets | Reps | Weight | Time |
|---|:-:|:-:|:-:|:-:|
| Stretch circuit — 3 sets | ✓ | | | |
| Push-ups — 3 × 15 | ✓ | ✓ | | |
| Farmer carry — 3 sets @ 70 lbs | ✓ | | ✓ | |
| Leg Extension — 3 × 12 @ 105 lbs | ✓ | ✓ | ✓ | |
| Plank — 3 × 0:45 | ✓ | | | ✓ |
| Weighted plank — 3 × 0:45 @ 25 lbs | ✓ | | ✓ | ✓ |

**Targets vs. actuals**

- In a **template**, sets and reps are written as ranges, `X-Y` (e.g. `3-4 sets × 8-12 reps`). A single number is just a range where X = Y. Weight and time targets are optional.
- In a **logged workout**, each set records actual values (e.g. set 1: 12 reps @ 105, set 2: 11 reps @ 105). Filling these in is optional; checking the exercise off is enough to count it as done.
- The metrics chosen for an exercise belong to the exercise, so "Leg Extension" always shows reps + weight wherever it appears.

**Last known values**

When an exercise is logged with values, those become its "last known" values (e.g. `Leg Extension — 3 × 12 × 105 lbs`). They are shown when the exercise card is expanded during a workout and are used to pre-fill today's inputs.

---

## Features

### Templates
- Create, edit, duplicate, and delete templates
- Ordered exercise list with set/rep ranges and optional weight/time targets
- Notes on the template and on individual exercises
- Assign to a category (Upper, Lower, Push, Pull, or custom)
- Mark as favorite; favorites sort to the top

### Routines
- Build a routine as an ordered list of templates
- After a workout from the routine is finished, the next template in the routine becomes the recommendation
- The routine loops back to the start after the last template
- The user can skip ahead or start any template without breaking the routine

### Active workout
- Start from the recommended template, any template, or a past workout
- Shows the list of exercises as cards
- Tap a card to expand it: last known values, target ranges, and inputs for today's sets/reps/weight/time
- Check off an exercise when done; it turns grey and stays in place
- Reorder exercises with up/down buttons (the order you actually did them is saved)
- Add or remove exercises for this session only, without changing the template
- Workout-level notes
- Leave the workout at any time to browse or edit other things, then resume from the home screen
- Persists across app close/reopen until the user taps **Finish**

### History
- Every finished workout is saved with its date, template, exercises, values, and notes
- Past workouts can be reopened to view or edit
- A past workout can be used as the starting point for a new one
- Each template shows how many times it has been logged

---

## Home screen

From top to bottom:

1. **Resume workout** banner, if one is in progress
2. **Next up** — the next recommended template from the active routine (hidden if no routine is set), with a one-tap Start
3. **Templates** — favorites first, filterable by category, with *Add template* and *Edit* actions
4. **Past workouts** — the 3 most recent, each with its template's total log count, and a **More** button to the full history page
5. **Routine** — make or edit the active routine

---

## Requirements

- **Mobile only.** Designed for a phone screen; no desktop layout.
- **Offline, on-device storage.** All data saved locally on the phone. No backend, database server, or account.
- **Durable.** Data survives app restarts. A manual export/import (e.g. a JSON file) is a good safety net against losing the phone or clearing app data.

## Out of scope (for now)

- Accounts, cloud sync, or multi-device
- Social features, sharing, leaderboards
- Exercise video/instruction library
- Coaching or auto-progression suggestions
- Wearable integrations
