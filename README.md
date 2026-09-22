# Search or Ask

A clickable prototype of a universal, on-device search feature for Android phones, inspired by the redesigned Spotlight in iOS 27. It runs entirely in a desktop browser inside a simulated phone. There's no backend, no login and no API keys.

Search and Ask live inside a **Dynamic Island** that grows out of the punch-hole camera. The same island also shows live activities: timers, calls, a food order, a train trip, voice input and alarms.

## Docs

- [Product requirements (PRD)](docs/PRD.md): problem, goals, personas, requirements, metrics, risks, milestones
- [User flows](docs/USER_FLOW.md): entry points, the Search and Ask flows, live activities, incoming events, and a 3-minute demo script

## Run it

Open `search-or-ask.html` in any modern desktop browser. Chrome is recommended, because it supports real voice input.

The only thing loaded from the internet is two Google Fonts. Offline, the page falls back to system fonts.

## Open Search or Ask

Any of these opens the panel:

- Tap the camera.
- Swipe down (drag with the mouse) on the camera, the island or the home screen.
- Tap the **Search or Ask** pill.
- Press **Ctrl+K** (**⌘K** on a Mac).

| Key | Action |
|---|---|
| Tab | Switch between Search and Ask |
| ↑ / ↓ | Move through results |
| Enter | Open the selected result |
| Esc | Go back or close |

## What it does

**Search**
- Shows a top hit, then groups for apps, contacts, settings, messages and screenshots.
- Handles typos (`bluetoth`), synonyms (`night theme` finds Dark theme) and related words (`train ticket` finds PNR messages).
- Matches amounts with or without commas (`₹2,450`) and finds screenshots by the text inside them.
- Lets you toggle settings like Wi-Fi straight from the results.

**Ask**
- Gives direct answers built from the phone's data, each with tappable links to its sources: next train, rent owed, latest OTP, bill due dates, weekly spending, doctor appointment, Wi-Fi passwords.
- Runs actions: "Turn on dark mode", "Call Amma", "Set an alarm for 8am", "Set a timer for 10 minutes", "Remind me to pay rent on 5 Oct".

**Dynamic Island**
- Shows live activities, with a second one as a bubble beside the pill.
- Drops in notifications for incoming events.
- Shows confirmations like "Copied" and "Alarm set".

**Developer panel** (beside the phone)
- Clickable demo queries.
- A match-score toggle.
- Buttons that simulate incoming events: an OTP, a WhatsApp message, a call, Swiggy tracking, a timer, an alarm and a charger.
- Reset.

## Mock data

All data is fictional and set in India:
- 13 apps
- 15 contacts with +91 numbers
- 20 settings with synonyms
- 26 SMS messages and notifications: IRCTC tickets, bank OTPs and debit alerts, a rent message from Rahul, Swiggy and Zomato orders, an Amazon delivery, an electricity bill and a doctor appointment
- 10 screenshots

The demo clock is fixed at **Tue, 22 Sep 2026, 9:41 AM**, so relative dates stay the same between demos.

## Notes

- Ask works by matching question patterns against the mock data, not by calling a language model, so everything stays offline. Unusual questions get the closest-match reply rather than a real answer.
- Everything is in one self-contained HTML file: HTML, CSS and JavaScript.
