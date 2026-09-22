# User flows: Search or Ask

This document shows how people move through **Search or Ask**, as built in [`search-or-ask.html`](../search-or-ask.html). Requirement IDs (FR-x) and user stories (US-x) refer to the [PRD](PRD.md).

The diagrams are written in Mermaid, which GitHub draws automatically.

---

## 1. Entry points → panel

Every entry point ends in the same place: the island opens into the Search / Ask panel. If a live activity is showing, the panel grows out of that pill. If not, it grows out of the camera.

```mermaid
flowchart TD
    A([Home screen or any app]) --> B{How does the user start?}
    B -->|Tap camera or idle island| P
    B -->|Swipe down on camera, island or home| D[Island grows with the drag]
    B -->|Tap 'Search or Ask' pill| P
    B -->|Ctrl+K on a keyboard| P
    D -->|Dragged more than 90 px| P
    D -->|Released early| A
    P[Island opens into the panel<br/>background blurs, search box focused] --> E[Empty state<br/>Live now · Recent · Suggested apps]
    E --> M{Mode}
    M -->|Search| S[[Search flow §2]]
    M -->|Ask| K[[Ask flow §3]]
    E -->|Tap a Live now row| L[Panel closes<br/>island expands that activity]
    P -->|Esc · tap background · swipe up on header| X([Panel shrinks back into the camera])
```

| Step | What the user sees | Notes |
|---|---|---|
| Open | Black panel grows out of the camera and springs to size | Status bar hides behind it |
| Header | Search / Ask toggle left of the camera, "On-device" right of it | Tab switches mode |
| Empty state | Live activities, recent searches, suggested apps | In Ask mode: sample questions |

---

## 2. Search flow

```mermaid
flowchart TD
    A[User types] --> B[Results update on every keystroke<br/>FR-2]
    B --> C{Any matches?}
    C -->|No, and it looks like a question or command| Q[Show 'Do this' or 'Ask this' row] --> K[[Switch to Ask]]
    C -->|No| N[No matches · offer to ask instead]
    C -->|Yes| R[Top hit and quick actions<br/>then groups ordered by best match]
    R --> T{User action}
    T -->|Tap a quick action| QA[Call · Copy PNR · Copy OTP · Open]
    QA --> F[Confirmation flashes in the panel header]
    T -->|Flip a setting switch| SW[Setting changes on the spot] --> F
    T -->|Tap a result or press Enter| O[[Open result §6]]
    T -->|Keep typing| B
```

**How matching works:**
1. Exact word → prefix → digits inside a number → typo within 1–2 edits (FR-3).
2. If a word is missing, related words count as a weaker match (FR-4).
3. A per-type weight and a recency boost are applied. PNR and OTP queries push up messages that contain one (FR-7).

### Example: "Where's my ticket?" (US-1)
| # | User | System |
|---|---|---|
| 1 | Pulls the island down | Panel opens, search box focused |
| 2 | Types `pnr` | Top hit: IRCTC SMS with PNR 4521876390, plus **Copy PNR** |
| 3 | Taps **Copy PNR** | Header flashes "Copied 4521876390" |
| 4 | Taps the top hit | SMS thread opens with that message highlighted |

### Example: a setting in your own words (US-2)
| Query | Top hit | Why it matched |
|---|---|---|
| `night theme` | Dark theme | Synonym list |
| `bluetoth` | Bluetooth | 1 edit away (letters swapped) |
| `make text bigger` | Font size | Synonym "bigger text" |

---

## 3. Ask flow

```mermaid
flowchart TD
    A[User asks a question or gives a command<br/>typed or spoken] --> B[Short 'thinking' dots]
    B --> C{Is it an action?}
    C -->|Alarm, timer or reminder| CL{Time understood?}
    CL -->|Yes| AC[Do it · show an alarm switch and Undo<br/>timer starts in the island]
    CL -->|No| CQ[Ask for the missing detail<br/>e.g. 'What time should the alarm ring?']
    C -->|Turn on or off a setting| AS[Flip the setting · show its switch]
    C -->|Call, message or open| AP[Show a Call or Open button]
    C -->|No| D{Matches a known question?<br/>train, rent, OTP, bill, spend…}
    D -->|Yes| AN[Answer written out word by word<br/>key facts · buttons · source links]
    D -->|No| FB{Anything related on the phone?}
    FB -->|Yes| CM[Closest match quoted, with source]
    FB -->|No| NM[Nothing on this phone matched]
    AN --> S{User action}
    S -->|Tap a source| O[[Open result §6]]
    S -->|Pin to island or Track live| LA[[Live activity §4]]
    S -->|Copy| F[Confirmation in the header]
```

### Example: "What's my latest OTP?" (US-3)
| # | User | System |
|---|---|---|
| 1 | Taps **Ask**, types the question | Answer: "Your latest OTP is **482913** from HDFC Bank for a ₹2,450 payment at Swiggy. It arrived 9 minutes ago and expires in about **1 min**." |
| 2 | — | Lists the last 3 OTPs, with source links |
| 3 | Taps **Copy 482913** | "Copied 482913" |

### Example: "Set an alarm for 8am" (US-6)
| Input | Result |
|---|---|
| `set a alarm for 8am` | "Alarm set for **8:00 AM** tomorrow, 22 h 19 min from now." Shows a switch and Undo |
| `alarm at 7` | Set for 7:00 PM today, and says why: "I took that as 7:00 PM. Add 'am' or 'pm' to change it." |
| `wake me up at 6:30 tomorrow` | 6:30 AM tomorrow ("wake me up" is always taken as morning) |
| `set an alarm` | Asks: "What time should the alarm ring?" |
| `cancel my alarms` | Turns off every alarm that's on and lists them |

---

## 4. Live activity lifecycle (Dynamic Island)

```mermaid
stateDiagram-v2
    [*] --> Idle
    Idle --> Expanded: activity starts (shows for 3.6 s)
    Expanded --> Compact: 3.6 s pass, or tap outside
    Compact --> Expanded: tap the island
    Expanded --> Opened: tap the card body
    Opened --> Compact: back
    Compact --> Idle: activity ends
    Expanded --> Idle: End, Cancel or Unpin
    Compact --> SearchPanel: pull down, or any entry point
    SearchPanel --> Compact: close panel
    Idle --> SearchPanel: tap camera
    SearchPanel --> Idle: close panel
```

**Two activities at once:** the higher-priority one takes the pill and the other shows as a bubble. Tapping the bubble swaps them (FR-15, FR-16).

| Activity | Compact shows | Expanded controls | How it ends |
|---|---|---|---|
| Timer | Orange countdown | Pause / Resume, Cancel, progress | At 0:00 an alert shakes the island, with "+1 min" and Stop |
| Call | Duration and a sound wave | Mute, Speaker, End | End button, here or on the call screen |
| Swiggy order | Stage and minutes left | Progress bar with a moving scooter | Delivered notification |
| Train trip | Coach · seat and days to go | Route, berth, Copy PNR, Unpin | Unpin |
| Voice input | — | Live transcript | Transcript fills the search box |

---

## 5. Incoming events

```mermaid
sequenceDiagram
    participant OS as Phone (simulated)
    participant I as Island
    participant IX as On-device index
    participant U as User
    OS->>IX: New SMS (OTP 850551)
    IX-->>IX: Re-index and pull out the OTP
    OS->>I: Notification
    I->>U: Card drops in: HDFC Bank · OTP 850551 · [Copy] [Open]
    alt User taps Copy
        U->>I: Copy 850551
        I->>U: "Copied 850551"
    else User ignores it
        I-->>I: Goes away after 6.5 s
    end
    U->>I: Ask "What's my latest OTP?"
    I->>IX: Look it up
    IX-->>U: 850551, arrived just now, about 10 min left
```

### Incoming call (US-9)
```mermaid
flowchart LR
    A[Amma calling] --> B[Island stays expanded and shakes<br/>Decline · Accept]
    B -->|Accept| C[Call live activity starts<br/>timer running]
    B -->|Decline| D[Confirmation 'Declined call · Amma']
    B -->|No answer for 20 s| E[Confirmation 'Missed call · Amma']
    C -->|End| F[Confirmation 'Call ended · 0:42']
```

---

## 6. Open a result and come back

```mermaid
flowchart LR
    R[Result or source link] --> T{Type}
    T -->|Message| M[Thread, with the matching message highlighted]
    T -->|Contact| C[Contact card · Call · Text · recent messages]
    T -->|Setting| S[Settings page with a working switch]
    T -->|Screenshot| X[Full image and the text found in it]
    T -->|App| A[Placeholder app screen]
    T -->|Clock| K[Alarms and reminders]
    M & C & S & X & A & K -->|Back or Esc| P[Panel comes back as it was left<br/>query and scroll kept]
```

While a result is open, the panel hides and the island goes back to showing live activities.

---

## 7. Demo script (about 3 minutes)

The Developer panel beside the phone drives this.

1. **Search:** click `pnr`, then `night theme`, then `bluetoth`. This shows ranking, synonyms and typo tolerance.
2. **Ask:** click "When is my train?", then **Pin to island**. The trip appears in the island.
3. **Action:** "Set an alarm for 8am", then "Set a timer for 10 minutes". The timer takes the pill and the train moves to the bubble.
4. **Live event:** click **Incoming OTP**. A card drops in. Ask "What's my latest OTP?" and it returns the new code.
5. **Call:** click **Amma calls**, accept, then end it from the island.
6. **Reset** to return to the starting state.
