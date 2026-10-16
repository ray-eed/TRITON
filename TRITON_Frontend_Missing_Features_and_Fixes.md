# TRITON — Frontend Gap & Improvement Specification

## Purpose

This document describes what is **still missing, incomplete, or technically weak** in the current TRITON frontend prototype.

It is intended for Claude / coding agents working on the existing prototype.

The current `index.html` already contains a substantial amount of the concept:

- Mission Overview
- My Health
- AI Insights
- Mission
- Crew
- Emergency Response
- Environment Intelligence
- Mental & Behavioral
- Incidents
- Medical Review
- Behavioral Review
- Health History
- Private Women's Health
- Mock AI/rule-based analysis
- Emergency procedures
- Demo simulation
- Shared mock mission data

The goal now is **not to add random new features**.

The goal is to make the existing system feel like **one coherent health-intelligence product**, improve the demo, fix inconsistencies, and add the missing operational UX.

---

# 1. DO NOT REBUILD THE PRODUCT

Before making any change:

- Inspect the existing `index.html`.
- Preserve the current visual identity.
- Keep the health dashboard.
- Keep existing working interactions.
- Reuse existing styling and components where sensible.
- Do not replace the project with a completely different design.
- Do not integrate real AI yet.
- Do not add a backend yet.
- Do not add authentication.
- Do not add real sensor/wearable integrations.
- Do not add real medical APIs.
- Use deterministic mock data only.

This is still a **frontend visualization/demo prototype**.

---

# 2. HIGHEST-PRIORITY PROBLEM: DEMO STATE IS NOT FULLY UNIFIED

## Current issue

The prototype has a central `MISSION` object and a `DEMO_STEPS` array, but many pages still contain fixed values that do not evolve consistently with the demo.

For example:

- Maya is stored in `CREW` with `status: critical`.
- The Mission Overview can simultaneously present the beginning of the simulation as normal.
- The Environment page already shows elevated CO₂ even though the demo introduces the CO₂ event later.
- The demo changes page/emergency/protocol state, but does not fully evolve the underlying health, mental, behavioral and environmental state.

This creates the possibility of contradictory screens.

## Required improvement

Create a single scenario-driven mock mission state.

Conceptually:

```text
SCENARIO 0
Normal

SCENARIO 1
Early physical deviation

SCENARIO 2
Mental/behavioral change

SCENARIO 3
Cognitive deviation

SCENARIO 4
AI early-risk detection

SCENARIO 5
Mission-context concern

SCENARIO 6
Environment change

SCENARIO 7
Crew correlation

SCENARIO 8
Incident

SCENARIO 9
Critical emergency

SCENARIO 10
Crew alert

SCENARIO 11
Responder assigned

SCENARIO 12
Response active

SCENARIO 13
Professional review
```

Every page must read from the same state.

If the simulation says CO₂ is normal, the Environment page must show normal CO₂.

If Maya is normal, the Crew page must not show her as critical.

If the simulation reaches an emergency, the Overview, Crew, Emergency and Timeline screens should all reflect that same state.

---

# 3. REBUILD THE DEMO AROUND THE EXPANDED CONCEPT

## Current issue

The existing 10-step demo mostly demonstrates the original concept:

```text
Normal
→ Health degradation
→ AI analysis
→ Mission context
→ Environment
→ Crew correlation
→ Emergency
→ Crew alert
→ Responder
→ Response
```

The newer features are mostly outside the main demo:

- Mental/behavioral health
- Cognitive performance
- Personal baseline/history
- AI companion
- Incidents
- Medical review
- Behavioral review
- Periodic medical data
- Women's health

## Required improvement

Keep the existing demo concept but expand the story.

Recommended sequence:

### Step 1 — Normal operations

All crew nominal.

### Step 2 — Physical trend changes

Maya's:

- sleep declines
- recovery declines
- resting HR increases

### Step 3 — Mental/behavioral changes

Maya shows:

- lower mood
- increased stress
- reduced activity/social interaction

### Step 4 — Cognitive change

Reaction time/attention slightly deviates from baseline.

### Step 5 — TRITON early-risk analysis

Show:

```text
Physical
+
Mental
+
Behavioral
+
Cognitive
+
Mission workload
↓
TRITON INTELLIGENCE
↓
Emerging health risk
```

### Step 6 — Mission context

EVA is approaching.

TRITON shows mission relevance.

### Step 7 — Environment event

CO₂ rises.

### Step 8 — Crew correlation

Multiple crew members report headaches.

### Step 9 — Incident

Maya's incident/injury gets logged.

### Step 10 — Emergency

Maya becomes unresponsive.

### Step 11 — Crew alert

Crew assistance required.

### Step 12 — Responder

Daniel identified as nearest available responder.

### Step 13 — Response

Daniel acknowledges, navigates and starts response.

### Step 14 — Professional review

The event automatically appears in:

- Medical Review
- Behavioral Review
- Health History

This makes the demo show the **whole ecosystem**, not just the original dashboard story.

---

# 4. ADD A CLEAR "EVENT" MODEL

Everything important in TRITON should be representable as an event.

Examples:

- Sleep deviation
- Mood change
- Cognitive deviation
- Environmental change
- Crew symptom cluster
- Injury
- AI insight
- Emergency
- Professional review

Each event should have a common structure:

```js
{
  id: "E-0143",
  timestamp: "14:32:55",
  type: "EMERGENCY",
  severity: "CRITICAL",
  subject: "Maya Chen",
  source: "TRITON",
  status: "ACTIVE",
  summary: "Critical physiological anomaly detected",
  actions: [],
  linkedData: []
}
```

This does NOT need a backend.

The purpose is to make the frontend coherent.

---

# 5. ADD DATA SOURCE + FRESHNESS LABELS

## Missing

Current metric cards generally show values but do not consistently communicate:

- Where the value came from
- Whether the value is live
- How old it is
- Whether it was self-reported
- Whether it is a lab result
- Whether it is AI-derived

## Add

Every important data type should have lightweight metadata.

Examples:

```text
HEART RATE
81 BPM

LIVE • 4 sec ago
Source: Wearable
```

```text
MOOD
IRRITATED

SELF-REPORTED • 2h ago
```

```text
GLUCOSE
92 mg/dL

LAB • 12 days ago
```

```text
TRITON RISK
MODERATE

Derived from simulated data
```

Also support:

```text
DATA STALE
SENSOR UNAVAILABLE
DATA DELAYED
NO DATA
```

This should be implemented visually even though the underlying values are mocked.

---

# 6. ADD A REAL ALERT CENTER

## Current state

The prototype has an "attention queue" and emergency alert states.

## Missing

A proper alert-management concept.

## Add

A compact alert center/drawer.

Example:

```text
ALERT CENTER

ACTIVE
1 Critical
1 Warning
2 Monitor

UNACKNOWLEDGED
2

14:32
E-0143
Possible incapacitation
Maya Chen
UNACKNOWLEDGED

12:40
W-0142
CO₂ deviation
Environment
ACKNOWLEDGED
```

Allow:

- Open
- Acknowledge
- Filter by severity
- Filter by affected person/system
- View timestamp
- View event details
- Open linked procedure

This does not need to become a giant separate application.

A drawer/modal is sufficient for the prototype.

---

# 7. ADD AN ACTION LIFECYCLE

## Current state

AI insights provide recommendations.

Emergency procedures provide steps.

## Missing

A consistent status lifecycle for actions.

Use:

```text
RECOMMENDED
↓
ACKNOWLEDGED
↓
ACCEPTED
↓
IN PROGRESS
↓
COMPLETED
```

For example:

```text
Recovery period recommended

[ACKNOWLEDGE]
[DEFER]
[COMPLETE]
```

Then:

```text
Recovery period
IN PROGRESS
```

Then:

```text
Recovery period
COMPLETED
```

This makes TRITON an operational system instead of a notification-only interface.

---

# 8. IMPROVE AI AUTOMATION STATE

## Current strength

The current prototype already tells the user that the AI is simulated/rule-based and not a diagnosis.

Keep this.

## Improve

Show the AI state more explicitly:

```text
TRITON INTELLIGENCE

MODE
SIMULATED / RULE-BASED

AUTOMATION
ADVISORY ONLY

CONFIDENCE
MODERATE

HUMAN REVIEW
RECOMMENDED
```

This should be consistent throughout AI-related views.

Do not make the AI sound like a doctor.

---

# 9. MAKE AI INSIGHTS MULTI-DOMAIN

## Current state

Current mock health analysis focuses heavily on:

- recovery
- resting HR
- workload
- sleep

## Required improvement

Add the newer domains into the visual analysis.

Example:

```text
PHYSICAL
Recovery ↓
Resting HR ↑

MENTAL
Mood ↓
Stress ↑

BEHAVIOR
Activity ↓
Isolation ↑

COGNITIVE
Reaction time slower

MISSION
Workload ↑
EVA approaching
```

Then:

```text
TRITON ASSESSMENT

Emerging health-risk trajectory

Confidence: Moderate
```

Then:

**Possible contributors**

- sleep disruption
- workload
- reduced recovery

Then:

**Suggested action**

- recovery
- reassessment
- professional review if persistent

Important:

Do not turn this into disease diagnosis.

---

# 10. ADD A HEALTH TRAJECTORY VISUALIZATION

The concept of early detection needs a clearer visual.

Add:

```text
HEALTH TRAJECTORY

NOW
🟡

6 HOURS
🟡

24 HOURS
🟠

7 DAYS
🟠
```

or a line/trajectory chart showing:

- baseline
- current trend
- projected risk direction

Label it as:

**TRITON-derived trajectory**

or

**Prototype risk trajectory**

Do not present it as a clinically validated prediction.

---

# 11. IMPROVE PERSONAL BASELINE PRESENTATION

The current Health History already contains a baseline comparison.

Make the comparison more visible throughout the application.

Instead of only:

```text
Heart Rate
81 BPM
```

show:

```text
RESTING HEART RATE

Pre-flight      71 BPM
Recent baseline 73 BPM
Current         81 BPM

+11% vs recent baseline
```

Do the same where meaningful for:

- sleep
- recovery
- reaction time
- attention
- mood
- activity
- stress

The central principle:

> Compare the astronaut against their own history, not simply generic "normal" values.

---

# 12. ADD A PROPER HEALTH PASSPORT VIEW

The current prototype has Health History.

Expand it into a more coherent longitudinal record:

```text
HEALTH PASSPORT

PRE-FLIGHT
TRAINING
LAUNCH
MISSION DAY 1
MISSION DAY 90
CURRENT
```

Show selected trend comparisons.

Do not turn this into an enormous medical-record application.

Keep the prototype visually focused.

---

# 13. COMPLETE THE MEDICAL REVIEW WORKFLOW

## Current state

Medical Review exists and supports:

- biweekly review
- event-triggered review
- AI summary
- lab data
- review request

## Missing

A complete workflow.

Add:

```text
TRITON FLAGGED
↓
REVIEW QUEUE
↓
ASSIGNED TO PHYSICIAN
↓
UNDER REVIEW
↓
PHYSICIAN NOTE
↓
FOLLOW-UP
↓
RESOLVED
```

Prototype controls can include:

- Assign review
- Mark reviewed
- Add note
- Set follow-up
- Close review

Everything can be mocked.

---

# 14. COMPLETE BEHAVIORAL-HEALTH REVIEW WORKFLOW

Apply the same pattern to Behavioral Review.

```text
TRITON FLAGGED
↓
BEHAVIORAL REVIEW QUEUE
↓
PROFESSIONAL REVIEW
↓
SUMMARY / NOTE
↓
FOLLOW-UP
↓
CLOSED
```

The AI companion summary should feed this queue.

Example:

```text
PRIVATE COMPANION SUMMARY

3 stress-related conversations
2 low-mood check-ins
Sleep disruption
Reduced social interaction

AI summary:
Persistent behavioral changes observed.

Professional review:
RECOMMENDED
```

The professional still makes the judgement.

---

# 15. ADD COMMUNICATION / GROUND SUPPORT STATUS

The current top bar has a simple:

```text
COMMS NOM
```

Improve it.

Add a small communications status panel:

```text
GROUND LINK
● NOMINAL

Last sync
14:21:08

Next sync
14:42:00

Queued reports
2

Pending medical review
1
```

Support a simulated delay state:

```text
GROUND LINK DELAYED

Medical report stored locally.
Awaiting transmission window.
```

This makes the medical-review concept much more convincing for a space mission.

Still no real communications backend.

---

# 16. ADD PRIVACY-AWARE ROLE VIEWS

The current role switch is clearly labeled as demo-only.

Keep that disclaimer.

Improve the UI conceptually so the different roles have appropriate information.

## Astronaut

Can see:

- personal health
- personal mental health
- own history
- companion
- own private health

## Crew

Can see:

- crew status
- emergency state
- responder information
- only necessary health information

## Physician

Can see:

- physical health
- medical history
- labs
- injuries
- relevant AI summaries

## Behavioral-health professional

Can see:

- mental/behavioral trends
- cognitive data
- relevant summaries

Do not implement real permissions yet.

This is a UX prototype only.

---

# 17. ADD MEDICATION / TREATMENT TRACKING

This is currently missing from the health ecosystem.

Add a lightweight mock module:

```text
MEDICATIONS

Medication A
08:00 ✓
20:00 ○

Medication B
14:00 ✓

Adherence
94%
```

Also show:

- active treatment
- treatment start/end
- follow-up date
- treatment response

Keep it secondary to the main dashboard.

---

# 18. ADD CUMULATIVE EXPOSURE CONTEXT

The current Environment page includes radiation as an instantaneous metric.

Add a secondary concept:

```text
RADIATION

Current rate
0.2 mSv

Mission cumulative
XX.X mSv

7-day trend
↑ 4.2%
```

This is optional polish, not a core demo feature.

---

# 19. ADD FAILURE / EDGE STATES

A strong prototype should show that the system knows when data cannot be trusted.

Implement demo-ready states:

### SENSOR OFFLINE

```text
SpO₂
—
SENSOR UNAVAILABLE
```

### DATA STALE

```text
CO₂
0.8%

STALE
Last update: 03:42 ago
```

### AI UNAVAILABLE

```text
TRITON ANALYSIS

AI SERVICE UNAVAILABLE

Using deterministic safety rules.
```

### COMMUNICATION DELAY

```text
GROUND LINK DELAYED
Report stored locally.
```

### CONFLICTING DATA

```text
⚠ DATA CONFLICT

Wearable HR: 121
Manual check: 92

Confirmation required.
```

These are highly useful for a technical judge demonstration.

---

# 20. IMPROVE EMERGENCY RESPONSE UI

The current emergency system is already substantial.

Keep:

- emergency levels
- emergency procedure selection
- checklist
- responder
- navigation
- event timeline
- action tracking

Current emergency code already supports scenario-specific procedures, including:

- Astronaut unresponsive
- Physiological anomaly
- Environmental emergency
- Injury

and it labels procedures as generic placeholders rather than clinical protocols.

Preserve that behavior.

## Improve visual hierarchy

During emergency mode:

```text
🚨 EMERGENCY RESPONSE ACTIVE

WHAT?
Astronaut unresponsive

WHO?
Maya Chen

WHERE?
EVA Zone B

WHO IS RESPONDING?
Daniel Kim

WHAT NOW?
Initial assessment

PROGRESS
3 / 6 actions completed
```

This should be the dominant layout.

---

# 21. MAKE EMERGENCY PROCEDURES CONTEXTUAL

The emergency page already supports four procedure types.

Improve it so the UI clearly separates:

### Current emergency

What is happening now.

### Procedure

What the crew is following.

### Current action

What should happen now.

### Completed actions

What has already happened.

### Next action

What comes next.

Do not show all six steps as equally visually important.

Highlight the current step.

---

# 22. ADD EMERGENCY EVENT RESOLUTION → HEALTH SYSTEM

When an emergency is resolved, make the system propagate the event.

Example:

```text
EMERGENCY RESOLVED
        ↓
Incident created
        ↓
Health History updated
        ↓
Medical Review requested
        ↓
Behavioral Review considered
        ↓
Mission Timeline updated
```

This connection is important.

An emergency should not simply disappear when the procedure completes.

---

# 23. IMPROVE ENVIRONMENT ↔ HEALTH CORRELATION

The Environment page already shows:

- CO₂
- environmental trends
- crew symptom correlation

Keep the existing logic.

Improve the visual connection.

For example:

```text
12:40
CO₂ ↑

12:52
Sarah — Headache

12:54
Daniel — Headache

12:56
Maya — Headache

13:00
TRITON:
Possible shared environmental correlation
```

Then provide:

**Evidence**

and:

**Recommended investigation**

Do not claim causation.

---

# 24. IMPROVE MENTAL-HEALTH UI

The current Mental & Behavioral page is already present and contains:

- wellbeing check-ins
- early-risk analysis
- behavioral pattern
- cognitive performance
- mood
- Health Passport
- private companion

Keep all of this.

What is missing is stronger separation between:

### Self-reported data

What the astronaut explicitly reports.

### Behavioral indicators

Observed or derived trends.

### Cognitive indicators

Assessment results.

### AI interpretation

What TRITON infers from those signals.

### Professional review

What the clinician decides.

Visually separate these categories.

---

# 25. MAKE THE AI COMPANION MORE CLEARLY PRIVATE

The current companion is already labeled as predetermined/simulated.

Keep this.

Improve its UI:

```text
PRIVATE COMPANION

CONFIDENTIALITY
SIMULATED

Purpose:
Supportive conversation + check-in

AI STATUS:
SCRIPTED DEMO

PROFESSIONAL CARE:
AVAILABLE THROUGH REVIEW WORKFLOW
```

The prototype should never imply that the companion itself is a psychologist.

---

# 26. ADD PRIVATE DATA INDICATORS

Sensitive information should visibly show its privacy scope.

Examples:

```text
🔒 PRIVATE
Visible only to astronaut
```

```text
🔒 PROFESSIONAL ONLY
Available to behavioral-health team
```

```text
CREW VISIBLE
Operational information only
```

This is especially important for:

- mental health
- AI conversations
- menstrual health
- medical history

---

# 27. IMPROVE WOMEN'S HEALTH UI

The current Women's Health page already includes:

- private record
- cycle tracking
- symptoms
- observations
- notes
- share with physician

Keep it.

Do not expose these details on the general Crew screen.

Improve the language around sharing:

```text
PRIVATE RECORD

Sharing with physician:
OFF

Nothing is shared automatically.
```

This should remain clearly separated from crew situational awareness.

---

# 28. ADD "SOURCE OF TRUTH" LABELS

For every major decision/insight, make it clear whether the information is:

**MEASURED**

**SELF-REPORTED**

**DERIVED**

**AI/Rule-based**

**PROFESSIONAL**

Example:

```text
81 BPM
MEASURED

Mood: Irritated
SELF-REPORTED

Emerging recovery risk
TRITON-DERIVED

Physician assessment
PROFESSIONAL
```

This prevents the prototype from blurring observed data and AI interpretation.

---

# 29. DO NOT USE A GENERIC "HEALTH SCORE"

If any new UI proposes:

```text
Health = 72%
```

do not add it unless the metric has a clearly defined meaning.

Prefer:

- Recovery
- Deviation from baseline
- Risk trajectory
- Active concerns
- Readiness context
- Trend status

The prototype should not pretend that it has a clinically validated universal health score.

---

# 30. IMPROVE THE HOME DASHBOARD HIERARCHY

The Mission Overview should prioritize:

### 1. What needs attention?

### 2. Who is affected?

### 3. Why?

### 4. What mission impact exists?

### 5. What should happen next?

Raw metrics can remain below that.

Do not make the home screen a wall of equal-weight cards.

---

# 31. ADD A MISSION ATTENTION QUEUE

A persistent lightweight queue would help.

Example:

```text
ATTENTION

🔴 1  Crew assistance required
🟠 1  Emerging health risk
🟡 1  Environmental correlation
🟡 1  Professional review pending
```

Clicking each item opens its event/details.

This is more useful than a generic notification bell.

---

# 32. ADD AN EVENT DETAIL VIEW THAT LINKS EVERYTHING

For major events, create one reusable detail panel.

Example:

```text
EVENT E-0143

TYPE
Possible incapacitation

OBSERVED
HR ↑
Movement ↓
Communication lost

MISSION
EVA active

ENVIRONMENT
Nominal

CREW
Daniel available — 14 m

TRITON ACTION
Crew assistance alert

CURRENT STATUS
Response active

LINKED RECORDS
Health History
Incident
Medical Review
Behavioral Review
```

This is one of the strongest ways to make the product feel integrated.

---

# 33. IMPROVE THE DEMO CONTROLS

The existing simulation has:

- Run simulation
- Next
- Finish
- Exit
- Reset

Keep these.

Add:

- Current scenario state
- Timestamp
- Event count
- Current active alert count

Example:

```text
MISSION SIMULATION

STEP 7 / 14
MISSION TIME 14:32

EVENTS
14

ACTIVE ALERTS
1
```

The demo should be deterministic.

---

# 34. MAKE THE 4-MINUTE VIDEO STORY THE PRIMARY DEMO

The video should not become a tour of all pages.

Use the website to tell one story:

```text
NORMAL
↓
PHYSICAL CHANGE
↓
MENTAL/BEHAVIORAL CHANGE
↓
COGNITIVE CHANGE
↓
AI EARLY-RISK DETECTION
↓
MISSION CONTEXT
↓
ENVIRONMENT EVENT
↓
CREW CORRELATION
↓
INCIDENT
↓
EMERGENCY
↓
CREW RESPONSE
↓
PROFESSIONAL REVIEW
```

After the story, briefly show additional capabilities such as:

- Women's Health
- Lab Data
- Incident History
- Health Passport

Do not force every feature into the main dramatic path.

---

# 35. TECHNICAL REFACTORING

The current application puts the entire frontend into one very large `index.html`.

That is acceptable for an early mock.

It should not continue growing indefinitely.

Once the design direction is stable, refactor toward:

```text
src/
├── data/
│   ├── mission
│   ├── health
│   ├── mentalHealth
│   ├── behavior
│   ├── cognitive
│   ├── medical
│   ├── incidents
│   ├── environment
│   └── scenarios
│
├── state/
│   └── missionState
│
├── components/
│   ├── MetricCard
│   ├── Alert
│   ├── Insight
│   ├── Timeline
│   ├── CrewCard
│   ├── Procedure
│   └── EventDetail
│
├── views/
│   ├── Overview
│   ├── Health
│   ├── Mental
│   ├── Insights
│   ├── Mission
│   ├── Crew
│   ├── Environment
│   ├── Emergency
│   ├── MedicalReview
│   └── BehavioralReview
│
└── app
```

Do not over-engineer this.

The goal is simply to make future changes safer.

---

# 36. DO NOT ADD THESE RIGHT NOW

Do not spend prototype time on:

- Real LLM integration
- Real ML training
- Disease diagnosis
- Facial emotion recognition
- Real wearable hardware
- Real spacecraft telemetry
- Real medical API integration
- Authentication infrastructure
- Full 3D spacecraft simulation
- Complex navigation maps
- Massive datasets
- Dozens of new physiological metrics

The current prototype has enough features already.

---

# 37. PRIORITY ORDER

## P0 — Fix before adding polish

1. Unified scenario/mission state
2. Consistent cross-page data
3. Expanded demo storyline
4. Event model
5. Alert lifecycle
6. Action lifecycle
7. Data freshness/source indicators

## P1 — Major product UX

8. AI multi-domain analysis
9. Health trajectory
10. Professional review workflow
11. Communication/ground status
12. Privacy-aware role views
13. Emergency workflow improvements
14. Event detail linking

## P2 — Supporting features

15. Medication/treatment
16. Failure states
17. Cumulative exposure
18. Enhanced women's health
19. More cognitive metrics

## P3 — Final polish

20. Typography
21. Motion
22. Responsive behavior
23. Empty states
24. Accessibility
25. Final visual consistency

---

# 38. FINAL PRODUCT TEST

Before considering the prototype complete, ask:

### Can a judge answer these within 30 seconds?

**What is TRITON?**

**What does it monitor?**

**Why is AI useful here?**

**How does it detect problems early?**

**How does mental health fit into the same system?**

**How does mission context change the interpretation?**

**What happens when multiple crew members are affected?**

**What happens when an astronaut becomes unable to respond?**

**How does the crew respond?**

**How does professional medical/psychological review happen?**

If the interface cannot answer those questions without explanation from the team, the UI still needs work.

---

# FINAL SUCCESS CRITERIA

The finished frontend should communicate:

> **TRITON continuously builds a picture of an astronaut's physical, mental, behavioral, cognitive, environmental, and mission state.**

Then:

> **It compares changes against personal history, surfaces emerging risks, explains the evidence, and connects those risks to the mission.**

Then:

> **It helps the astronaut, alerts the crew when necessary, coordinates emergency response, and prepares information for human medical and behavioral-health review.**

The prototype should demonstrate this with a single connected mock mission.

---

# IMPORTANT IMPLEMENTATION REMINDER

This version is still a **dummy frontend**.

All:

- sensor data
- AI analysis
- predictions
- mental-health conversations
- reports
- medical reviews
- emergency procedures
- communications
- environmental events

are simulated.

Do not integrate real AI yet.

Do not use API keys.

Do not build backend infrastructure.

Make the prototype architecture clean enough that real services can be added later.

The goal right now is:

**Make the idea believable.**
**Make the workflow coherent.**
**Make the 4-minute demo compelling.**
**Do not over-engineer the prototype.**
