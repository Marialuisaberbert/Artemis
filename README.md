# Artemis
Artemis is a mobile travel planner for groups and solo travelers. Everyone sets private limits, Artemis finds where they overlap and explains why each flight fits, and every booking passes an identity, policy and financial trust check, live against Capital One Nessie and GoDaddy ANS. Built with React Native, Expo and TypeScript.
> Hackathon prototype. Flights, hotels and payments are mock data, and no real money moves.

## Technical Stack

* **App:** React Native 0.86, React 19, Expo SDK 57, TypeScript (strict), Expo Router
* **Globe, Plane & Ticket Engine:** React Native Skia. The dot-matrix globe, the flight route and plane, and the paper ticket (perforation, torn edge, paper grain, shadows) are all drawn on Skia canvases
* **Animation & Gestures:** Reanimated 4 and Gesture Handler for the swipe-to-cut, ticket flip, itinerary drawer and screen transitions
* **State:** Zustand, with services injected into the store and saved on the device with AsyncStorage
* **Domain Layer:** pure TypeScript with no UI code: preference matching, group ranking, the trust engine, trips and itineraries, the flight-journey simulator, the ticket-cut model and time zones (own daylight-saving rules, no `Intl`)
* **Device:** expo-location, expo-haptics, expo-audio, expo-blur, expo-image, expo-linear-gradient
* **Live Services:** Capital One Nessie sandbox API (financial layer) and GoDaddy Agent Name Service test registry (identity layer)
* **Testing:** Vitest. 150 domain tests, plus 3 integration tests that run against the real services when keys are present
* **Infrastructure:** none needed. No backend of its own. Published for demos with EAS Update

## Features

### Core Capabilities

* **Cinematic Discovery:** a dot-matrix globe shows where you are and your next trip. Destinations (Rome, Lisbon, London, Paris, Cape Town and more) open as full-bleed photo pages with the essentials
* **Onboarding & Ticket Cut:** an intro, a location step, and your first paper ticket to cut
* **Group Planning:** create a trip, invite friends, and everyone sets their own preferences
* **Solo Planning:** planning alone is three steps (preferences, your best matches, book)
* **Explained Matches:** every flight says why it fits, for example "$53 under your $600 limit" and "1 stop, Sofia prefers nonstop, you're fine with it"
* **Artemis Trust Engine:** identity → policy → financial checks before any booking
* **Collectible Trip Ticket:** cut it with a swipe, keep the torn stub in My Trips, turn it over for the trip details
* **Trip Mode:** a plane follows your flight over the same globe, with a countdown and an itinerary drawer
* **Morning Greeting:** the home screen greets you differently from one day to the next
* **Saved State:** trips, bookings and tickets survive an app restart

### Group Preference Matching

* **Private Preferences:** flight budget, departure window, maximum stops, hotel budget, hotel style and area, each marked **MUST**, **PREFER** or **NICE**. Nobody sees anyone else's answers until everyone has submitted
* **Overlap View:** rows marked **SHARED**, **FLEXIBLE** or **CONFLICT**, so you can see what you agree on, where one person cares more, and where you want different things
* **Hard Limits First:** hard limits remove options. The lowest flight budget becomes the group cap, and stops are capped at the most anyone will accept
* **Soft Preferences Order the Rest:** PREFER and NICE items only rank what's left. Match percentages are scored per traveler
* **Group Ranking:** each traveler ranks the options privately, and the rankings are combined with a Borda count. Ties go to the better match
* **Decision or Trade-off:** when the group agrees, Artemis shows one clear match. When it doesn't, it shows the trade-off instead of averaging

### Solo Planning

* **Shorter Flow:** skips the overlap and ranking steps that only make sense with other people
* **Your Must-Haves:** shown as chips at the top of your matches
* **What You'd Be Trading:** flights just outside your limits are shown, so you can see what you'd gain by bending one
* **Spending Guard:** shows what your agent may book on its own and your live account balance
* **Same Trust Check:** solo bookings pass the same three-layer check

### Artemis Trust Engine

* **Identity:** is the requesting agent who it claims to be? Checked against the GoDaddy Agent Name Service registry
* **Policy:** your rules, such as a per-flight limit, a per-night hotel limit, a monthly travel cap, verified providers only, and approval for anything outside travel
* **Financial:** are the funds there and does it stay inside the cap? Read from a Capital One Nessie sandbox account
* **Clear Outcomes:** identity fails → **blocked**, with no override. Policy or financial fails → **approval required**. Otherwise **auto-approved**
* **Live or Simulated:** every layer's result shows a LIVE or SIMULATED badge, and *YOU → Prototype tools → Live services* lists all three
* **Safe Fallback:** with no keys, or if a service can't be reached, the layer falls back to a local simulation and says so
* **Logic Outside the Screens:** no screen contains decision logic. Screens render what the engine returns

### Ticket, Trip Mode & Journey

* **Paper Ticket:** drawn once and clipped in two, with grain, a perforation, a torn edge and a flip. It is called the *Artemis Trip Ticket*, and it is a keepsake, not a boarding pass
* **Swipe or Tap to Cut:** the cut plays, the stub is saved, and *Welcome aboard* appears
* **Flight Journey Simulator:** step through the phases of the trip or play the whole journey as a time-lapse
* **Local Times:** every flight time is one exact instant, shown in the local time of its airport
* **Itinerary Drawer:** swipe up in Trip Mode for departures, arrivals, connections and hotel check-in

### Prototype Tools

* **Trust Demos:** run an auto-approved booking, an over-policy one, an impostor provider and a monthly-cap case
* **Force a Trade-off:** creates the disagreement case for a group
* **Replay:** replay the onboarding, replay the morning greeting, or reset the demo data
* **Live Services:** shows which services are live in this build

## Quick Start

Needs Node 22 or newer.

```bash
npm install
cp .env.example .env      # optional: turns on the live services
npx expo start
```

Scan the QR code with **Expo Go**, or press `i` for the iOS Simulator. Expo SDK 57 asks physical iPhones to be signed in to Expo Go with the same Expo account as the CLI (`npx expo login`). The simulator doesn't need it.

```bash
npm test               # 150 domain tests
npm run typecheck
```

### Try the Demo

1. **Fresh Start:** tap LET'S FLY, choose a location and a destination, and cut your first ticket
2. **Home:** drag the globe, swipe the trip cards, and try the World, Discover, Trips and You tabs
3. **Start a Trip:** Discover → Rome → START A TRIP → choose dates → add Sofia → CREATE TRIP
4. **Plan Together:**
   * Invite Sofia and set your preferences
   * Tap FILL IN FOR SOFIA · DEMO to submit hers
   * Read the overlap, then see your best matches (TAP 93%)
   * Rank the options and use SOFIA'S PREFERENCES · DEMO
5. **Book:**
   * Tap BOOK WITH ARTEMIS and watch the three layers resolve
   * Cut the ticket, then see *Welcome aboard* and the stub saved to My Trips
6. **Fly:**
   * ENTER TRIP MODE, watch the plane on the globe, and swipe up for the itinerary
   * Trips → your trip → VIEW TICKET, then tap to see the back
7. **Plan Solo:** add nobody (or tap PLAN SOLO INSTEAD), set preferences, and book from your matches
8. **Break It on Purpose:** YOU → Prototype tools → run the Impostor provider and the Over your policy demos

## Project Layout

```
app/                 expo-router routes (thin: each file just renders a screen)
src/domain/          pure TypeScript: matching, trust engine, trips and itinerary, destinations,
                     daily greeting, ticket cut, money and time zones
src/store/           zustand store with injected services, persistence and selector hooks
src/features/        screens, by feature (booking, planning, trip, ticket, onboarding, profile, ...)
src/design/          design system and the Skia globe
src/services/        device services: haptics, audio, location
assets/              destination photos and brand images
brand-export/        logo files (SVG and PNG)
__tests__/           vitest; live.integration.test.ts uses the real services when keys are present
scripts/             publish-for-judges.sh (publishes the demo with EAS Update)
```
