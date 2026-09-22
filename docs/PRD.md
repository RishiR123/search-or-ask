# PRD: Search or Ask

| | |
|---|---|
| **Status** | Prototype (v0.3) |
| **Owner** | [@RishiR123](https://github.com/RishiR123) |
| **Last updated** | 22 Sep 2026 |
| **Prototype** | [`search-or-ask.html`](../search-or-ask.html) · [User flows](USER_FLOW.md) |

## 1. Summary

**Search or Ask** is one entry point on an Android phone for finding anything on the device and getting direct answers about it. It lives in a **Dynamic Island** around the front camera. You pull the island down and it opens into a panel with two modes:

- **Search**: instant, typo-tolerant results across apps, contacts, settings, messages, notifications and screenshots.
- **Ask**: a direct, sourced answer to a question ("When is my train?") or a completed action ("Set an alarm for 8am").

Everything is indexed and answered **on the device**. The same island also shows **live activities**, such as a running timer, an active call or a food delivery, so ongoing tasks and search share one familiar surface.

## 2. Problem

People in India run their lives through SMS and notifications: train tickets, OTPs, bank debits, bills, deliveries and doctor reminders. Finding one of these today means:

- **Guessing which app has it.** Is the PNR in Messages, the IRCTC app, or a screenshot in Gallery?
- **Scrolling long threads.** Bank and delivery SMS pile up by the hundreds.
- **Reading the text yourself.** The information exists, but nothing pulls out the one fact you need: the seat number, the amount, the due date.
- **Settings search that fails on everyday words.** It misses synonyms ("night mode" vs "Dark theme") and typos.

Existing launcher and settings search matches text but doesn't *understand* it, and assistant-style tools often send personal data to a server.

## 3. Goals and non-goals

### Goals
1. **One gesture, everything.** A single entry point covers apps, people, settings, messages, notifications and screenshots.
2. **Answers, not just links.** Pull out the key fact (PNR, OTP, amount, date) and show it with its source.
3. **Private by default.** Indexing, search and answers all run on the device. Nothing is sent to a server.
4. **Fast.** Results update on every keystroke.
5. **Actions where you already are.** Toggle a setting, set an alarm, start a timer or place a call right from the result or answer.
6. **One home for ongoing tasks.** Timers, calls and deliveries live in the same island that search opens from.

### Non-goals (this phase)
- Web search or anything else outside the device.
- Taking actions inside third-party apps (paying through GPay, replying in WhatsApp) beyond opening them.
- Answers that need a language model to write free-form text. The prototype uses deterministic intents (see §8).
- Tablets, foldables and landscape layouts.

## 4. Target users

**Primary:** Android users in India, aged 18–40, whose phone is their main device, and who rely on SMS and notifications for travel, banking, food and utilities.

| Persona | Context | Job to be done |
|---|---|---|
| **Aarav, 27, software engineer (Bengaluru)** | Travels home by train monthly, shares a flat, orders food often | "Find my PNR and seat without opening three apps." "How much do I owe Rahul?" |
| **Priya, 34, design lead** | Heavy work notifications, many OTPs a day | "Copy the OTP that just arrived." "What's on today?" |
| **Suresh, 52, small-business owner** | Uses the phone mostly in Hindi and English; settings search doesn't work for him | "Turn on the thing that makes the screen dark." "Make the text bigger." |

## 5. User stories

| # | As a… | I want to… | So that… | Priority |
|---|---|---|---|---|
| US-1 | commuter | type "pnr" and see my ticket first | I can board without hunting through SMS | P0 |
| US-2 | any user | find a setting with my own words or a typo | I don't need to know its official name | P0 |
| US-3 | shopper | ask "What's my latest OTP?" and copy it in one tap | I finish checkout before it expires | P0 |
| US-4 | flatmate | ask "How much rent did Rahul ask for?" | I get the exact amount and its source | P0 |
| US-5 | any user | search the text inside screenshots | I find the Wi-Fi password I screenshotted | P1 |
| US-6 | any user | say "Set an alarm for 8am" | the action happens without opening the Clock app | P1 |
| US-7 | foodie | see my order's progress in the island | I don't keep reopening the delivery app | P1 |
| US-8 | any user | know my data stays on the phone | I trust the feature with my messages | P0 |
| US-9 | caller | answer or decline an incoming call from the island | I don't lose my place in what I'm doing | P2 |

## 6. Experience overview

For step-by-step flows and diagrams, see [USER_FLOW.md](USER_FLOW.md).

### 6.1 Entry points
Every entry point opens the same panel, which grows out of the island from whatever shape it has at that moment:
- Tap the camera or island.
- Swipe down on the camera, the island or the home screen.
- Tap the **Search or Ask** pill on the home screen.
- Press Ctrl+K on a keyboard.

### 6.2 The panel
- **Header row:** the Search / Ask toggle sits left of the camera and an "On-device" badge sits right of it. While the panel is open, confirmations flash in this badge.
- **Search box:** focuses automatically. It has a clear button and a mic button.
- **Empty state:** Live now (running activities), Recent searches, Suggested apps and a privacy note. In Ask mode, sample questions.
- **Height:** the panel is as tall as its content, up to nearly full screen, then scrolls.

### 6.3 Search mode
- A **top hit** with quick actions: Call, Copy PNR, Copy OTP, Open.
- **Groups** ordered by their best match: Apps, Contacts, Settings, Messages & notifications, Screenshots.
- **Highlighted matches.** **Key facts** (PNR, OTP, amount, upcoming date) shown as chips.
- **Inline switches** for toggle settings.
- An **"Ask this" / "Do this"** row when the query looks like a question or a command.

### 6.4 Ask mode
Each answer is one of four kinds:

| Kind | Example | Output |
|---|---|---|
| **Answer** | "When is my train?" | Plain-language answer, key facts, action buttons, source links |
| **Summary** | "How much did I spend this week?" | Total, a bar chart by merchant, the source messages |
| **Action** | "Turn on dark mode", "Set an alarm for 8am" | Carries out the action, shows a switch to undo it |
| **Clarify / fallback** | "Set an alarm" (no time) | Asks for what's missing, or shows the closest match |

Every answer says where it came from. Sources can be tapped, so users can check the answer.

### 6.5 Dynamic Island
| State | When | Content |
|---|---|---|
| **Idle** | Nothing live | Only the camera. Tap it to search |
| **Compact** | An activity is running | Glyph and status on the left, value on the right (e.g. timer 4:59). A second activity shows as a bubble |
| **Expanded** | Tap on compact, or a new activity (for 3.6 s) | Full card with controls |
| **Alert** | A confirmation or notification | A pill (for example "Copied ✓") or a notification card with actions. Leaves on its own |
| **Search panel** | Any entry point | The island becomes the Search / Ask panel |

**Live activities:** timer, call, incoming call, food delivery, pinned train trip, voice input, ringing alarm, charging.

## 7. Functional requirements

### Indexing and search
| ID | Requirement | Priority |
|---|---|---|
| FR-1 | Index apps, contacts, settings (with synonyms), SMS, notifications and screenshot text (OCR) on the device | P0 |
| FR-2 | Update results on every keystroke. No submit button in Search mode | P0 |
| FR-3 | Tolerate typos: 1 edit for words of 4+ characters, 2 edits for 7+, and swapped letters count as one edit | P0 |
| FR-4 | Match synonyms and related words (e.g. `train` ↔ `pnr`, `irctc`, `berth`) | P0 |
| FR-5 | Match numbers regardless of formatting (`2450` = `₹2,450.00`) | P1 |
| FR-6 | Pull out PNR, OTP, amounts and dates from messages and show them as chips | P0 |
| FR-7 | Rank by match quality, a weight per result type, and recency. Order groups by their best hit | P0 |
| FR-8 | Add new messages to the index as soon as they arrive, so they're searchable at once | P0 |

### Ask
| ID | Requirement | Priority |
|---|---|---|
| FR-9 | Recognise these kinds of question: train, rent, OTP, bill, spending, doctor, delivery, Wi-Fi password, recharge, salary, movie | P0 |
| FR-10 | Carry out these actions: toggle a setting, call or message a contact, open an app, set or cancel an alarm, start a timer, create a reminder | P1 |
| FR-11 | Understand natural times: "8am", "6:30", "noon", "in 20 minutes", "tomorrow", "on friday", "every weekday". State any assumption made (a bare "7" becomes the next 7 o'clock) | P1 |
| FR-12 | Link every answer to at least one source, or say plainly that nothing matched | P0 |
| FR-13 | Ask for missing details (such as an alarm with no time) instead of guessing | P1 |

### Island and live activities
| ID | Requirement | Priority |
|---|---|---|
| FR-14 | The island moves smoothly between idle, compact, expanded, alert and search-panel states | P0 |
| FR-15 | Show the two highest-priority activities, one in the pill and one as a bubble. Tapping the bubble swaps them | P1 |
| FR-16 | Priority order: incoming call > alarm > voice > call > timer > delivery > train | P1 |
| FR-17 | Route every confirmation through the island. While the panel is open, show it in the panel header | P1 |
| FR-18 | While an incoming call or alarm is ringing, keep the island expanded and shaking. Mark a call missed after 20 s | P2 |

### Other
| ID | Requirement | Priority |
|---|---|---|
| FR-19 | Tapping a result opens it. Back returns to the panel as it was left | P0 |
| FR-20 | Full keyboard support: Ctrl+K, Tab, ↑/↓, Enter, Esc | P1 |
| FR-21 | Voice input shows a live transcript in the island. Questions and commands switch to Ask automatically | P1 |

## 8. Non-functional requirements

| Area | Requirement |
|---|---|
| **Privacy** | No network calls for indexing, search or answers. Personal data never leaves the device. A visible "On-device" badge |
| **Latency** | Search under 50 ms per keystroke on a mid-range phone (the prototype measures under 1 ms on a desktop). Ask answers under 500 ms |
| **Offline** | Works fully in airplane mode |
| **Accessibility** | Visible keyboard focus. Screen readers announce island changes. Motion respects reduced-motion settings. Tap targets at least 40 px |
| **Battery** | Index updates in small batches, triggered by events such as a new message, not by polling |
| **Explainability** | Answers show their sources. A debug mode shows match scores (Developer panel → "Show match scores") |

### Prototype vs production
| Part | Prototype | Production direction (proposed) |
|---|---|---|
| Data | Fixed mock dataset, demo clock fixed at 22 Sep 2026, 9:41 AM | Real on-device index over the SMS, notification, contact and settings providers |
| Search | Hand-written scoring with typo tolerance, synonym groups and number matching | The same approach plus on-device vector search |
| Ask | Fixed patterns and deterministic handlers | An on-device language model that must cite sources, with the same answer / action / clarify kinds |
| Screenshot text | Pre-written OCR text | On-device OCR when a screenshot is saved |
| Island | Built in the browser | System-level surface (SystemUI) |

## 9. Success metrics

| Metric | Target (first 90 days of a pilot) |
|---|---|
| Searches ending in an open or action (search success rate) | ≥ 70% |
| Median time from open to tapping the result | ≤ 4 s |
| Ask answers where the user opens a source and then re-asks | ≤ 10% |
| Weekly active users of the feature / eligible users | ≥ 40% |
| Settings reached through search vs by browsing the menus | +25% |
| Privacy trust score in surveys ("I trust this with my messages") | ≥ 4.2 / 5 |

**Guardrails:** no rise in battery drain above 1% per day; no crashes in SystemUI caused by the island.

## 10. Risks and mitigations

| Risk | Mitigation |
|---|---|
| A wrong answer (for example the wrong OTP) causes harm | Always show the source and when it arrived. For OTPs, show time left before expiry. Make clarifying questions the default when unsure |
| Privacy worries about indexing SMS | Off until the user opts in. Choose sources one by one. The "On-device" badge. A clear "Delete index" option |
| Too many live activities at once | Priority order with at most two visible |
| Regional languages and mixed Hindi-English text | Test with mixed-language data. Map synonyms across languages (e.g. "bijli ka bill") |
| OEM differences in camera cut-out placement | Island geometry configurable per device |

## 11. Open questions
1. Should Ask learn from corrections (for example "not that alarm, the gym one")?
2. How far back should the index go by default: 90 days of SMS, or everything?
3. Should app-provided live activities (Swiggy, Uber) use a public API, and how are they kept consistent?
4. Where do regional-language queries route when there's no mapping for them?

## 12. Milestones

| Phase | Scope | Status |
|---|---|---|
| **M0: Prototype** | Search, Ask, island, live activities, mock data, developer panel | ✅ Done |
| **M1: Usability study** | 8–10 participants across the three personas, tasks based on US-1–US-7 | Next |
| **M2: Real index** | SMS, contacts and settings providers; screenshot OCR | Planned |
| **M3: On-device model for Ask** | Answers that must cite sources, plus evaluation sets | Planned |
| **M4: Pilot** | Opt-in beta and measuring the §9 metrics | Planned |
