# TRITON Health Prototype: Review and Fix List

**File reviewed:** `index.html` (single-file front-end prototype)
**Purpose:** Dummy front-end mockup for the TRITON project presentation
**Data:** All data is simulated for one fictional crew member (Maya Chen) and a mission day counter
**Review basis:** Source review and a stubbed render test of every page. Not yet checked in a real browser on a phone or laptop.

---

## 1. Overall verdict

Strong as a concept demo. The story connects across pages: an incident feeds the health history, the history feeds the medical and behavioral reviews, and the emergency page ties them together. Mental health wording mostly avoids diagnosis, and disclaimers appear where needed.

It is not suitable as a real health system. Access control is cosmetic, the emergency procedures are placeholders, and the assessment logic is hardcoded rules. These need to be clearly labelled before anyone outside the team sees the file.

---

## 2. Implementation status against the spec

### Implemented (prototype level)

- Early-risk analysis with observations, contributors, confidence, and recommendation
- Behavioral pattern indicators (eight spec items)
- Cognitive assessment: reaction time, attention, memory
- Mood and stress check-ins, motivation, isolation, sleep quality
- Health passport stages and resting HR baseline
- Live telemetry vs. periodic medical data, with lab panel
- Manual incident log with entry form
- Biweekly medical review and behavioral review, with event-triggered flag
- Private women's health section with consent toggle
- Emergency scenarios A to D with checklists
- Escalation levels: MONITOR, WARNING, CRITICAL, EMERGENCY RESPONSE ACTIVE
- Timestamped emergency timeline and live elapsed time
- Shared mission state for key values, and an analysis function (`analyzeHealth`)

### Not implemented or incomplete

- AI Insights page still uses literal text, not the analysis function
- Emergency, environment, and crew pages still hold their own values
- Empty, loading, and error states
- Status levels not consistently paired with icons and text
- Event timeline chain (sleep → workload → recovery → AI flag → EVA → CO₂ → symptoms → correlation → emergency → review) not drawn as one connected timeline
- Crew availability and shared crew events not verified against the spec
- Simulation stages not checked against the spec's stage list
- Companion responds with fixed text only, not to typed input
- Map label says "EVA Zone B"; spec example uses "Lab Module, Sector B"

---

## 3. Critical problems

### 3.1 Access control is cosmetic
The profile switch hides one navigation item. All data ships to the browser, and every other page shows Maya's data regardless of the selected profile. Anyone who opens the source sees everything.

**Fix:** Label it clearly as a demo, or remove any implication of protection.

### 3.2 Clinical content has more authority than it has earned
- `analyzeHealth()` uses a hardcoded if-chain with arbitrary thresholds. The page labels its output "AI assessment," which is misleading.
- Emergency procedures are generic placeholders styled like operational checklists.
- The companion's canned replies have no path to a human.

**Fix:** Rename the assessment to "Rule-based assessment," document the thresholds, mark the procedures as placeholders, and state that real procedures need clinical review.

### 3.3 Typed input is lost on every click
Every interaction rebuilds the page with `innerHTML`. Text typed into the incident form or private notes disappears when any other control is tapped. This is the most visible defect.

**Fix:** Store form values in state, or render only changed regions.

### 3.4 Data can drift between pages
Some older constants still hold literal values. One contradiction (84 m vs. 14 m on the map) had to be fixed by hand.

**Fix:** Keep one source of truth for each value and add a basic check that displayed numbers match the mission state.

---

## 4. Important issues

- **Accessibility:** Status is shown by color alone in places. Muted text is low contrast. Many labels are 10 to 12px. Clickable `div` elements are not keyboard reachable. Some form labels are not associated with their inputs. The pulsing animation ignores reduced-motion settings.
- **No deep links:** Refreshing the page resets everything. Nothing can be bookmarked or shared.
- **No confirmation on resets:** "Reset procedure" and "reset simulation" act immediately.
- **"Mark as reviewed" is in memory only:** It is fine for a demo, but it should not read as a physician sign-off.
- **Hardcoded day and location:** "Day 143" and "EVA Zone B" appear as fixed strings in several places.
- **Responsive navigation:** Fixed for phones, but not yet confirmed on a device.

---

## 5. Code quality notes

- Inline styles dominate, which makes theming and consistency harder.
- Dead code remains: `EMSEQ` is unused, and `recDev` is defined but never read.
- Naming is inconsistent (`S.s` for the page, `cur_`, mixed casing in helpers).
- No linting, no build step, and no automated tests beyond a stubbed render check.

---

## 6. Fix list for a dummy front-end presentation

### Must fix before showing

1. **Keep typed input in state.** Prevent the incident form and private notes from clearing when other controls are tapped. Alternatively, pre-fill the fields and avoid typing during the demo.
2. **Add a demo banner:** "Prototype with simulated data. Not connected to real systems."
3. **Rename "AI assessment"** to "Rule-based assessment" or "Automated trend summary."
4. **Mark the emergency procedures as placeholders:** "Generic placeholder procedure, not an operational protocol."
5. **Run the demo end to end** on a phone and a laptop: the full simulation from start to resolved, and every navigation item.

### Should fix

6. **Add a tooltip to the profile switch:** "Demo only: switches the view, does not enforce access."
7. **Remove or wire up dead controls.** Any button that does nothing will look broken.
8. **Add a "Reset demo" button** that restores the starting state.
9. **Confirm before resetting** the procedure or simulation.
10. **Pair status colors with text or icons** on every card.

### Nice to have

11. Raise the smallest text (10 to 11px) to at least 12px for projector readability.
12. Add a short "How to use this demo" card on the first screen, listing the pages to visit in order.

### Priority if time is short

Items 1 to 5. These prevent the most serious misreadings and the most visible bug.

---

## 7. Longer-term recommendations (not for the demo)

1. Split the file into data, logic, and views.
2. Add tests that check displayed values against the mission state.
3. Accessibility pass: icons and text for status, contrast, font sizes, keyboard operation, reduced motion.
4. For any real use: server-side authorization, audit logging for health records, a proper backend, and clinically sourced protocols reviewed by qualified clinicians.

None of the longer-term items should be attempted inside this single file.

---

## 8. Limitations of this review

- Reviewed from source and a stubbed render test, not a live browser session
- Layout, touch targets, and animation timing have not been observed
- Spec coverage was checked by searching for key terms, not by a line-by-line audit
