# Jot

Jot is a personal-memory iOS app that turns your scattered thoughts, notes, and events into a searchable, conversational vault — and lets you ask questions over everything you've captured.

---

## What it does

**Capture (Memo mode)** — Talk or type a thought. Jot classifies it automatically: a meal you had, a book recommendation, a bill you received, a calendar event, a contact detail. The AI reads your input and saves (or silently updates) the right kind of entry without you having to pick a category or fill a form.

**Retrieve (Ask mode)** — Ask anything over your own memory: *"What did I spend on groceries last month?"*, *"When did I last see Ben?"*, *"What was the name of that podcast Alex mentioned?"*. Retrieval happens on-device for speed and privacy, then the AI synthesises an answer from your actual entries.

That's the loop: capture freely, retrieve naturally.

---

## Core decisions

### Convex — backend and sync

The durable store is [Convex](https://convex.dev). Every entry is written to Convex first; the app keeps a local SwiftData working copy rebuilt from Convex on first launch. Live reactive subscriptions keep remote config and account state in sync without polling. Convex mutations go through a network-timeout wrapper because the Convex mobile SDK queues mutations silently when offline — without the guard, the UI would hang indefinitely.

### Anthropic + OpenAI — AI layer

The app talks to either Anthropic (Claude) or OpenAI depending on remote config. Claude is the default for both capture and retrieval; the provider and model IDs are hot-switchable server-side without a release. Cheap classification (Memo mode) uses a smaller/faster model; reasoning over the full vault (Ask mode) uses the main model.

### On-device retrieval

Ask-mode search runs against SwiftData on the device, not Convex. The trade-off is deliberate: zero round-trip latency, works offline, no server cost per character typed. The AI step that synthesises the final answer still goes through the proxy.

### Flat entry table

Entries are one wide table with sparse optional columns rather than a set of normalised tables per entry type. This keeps the data model simple to evolve and easy to query in a single pass.

### Anonymous-first auth

Users get a free daily balance immediately on install, backed by a Keychain UUID — no sign-in required. Signing in with Apple or Google merges the anonymous balance and entries into a proper account.

### Billing

Free tier: 5 AI actions per day. Jot Pro ($4.99/month): 200 actions per month via RevenueCat. Subscriptions are granted server-side via webhook — the client never self-reports subscription status.

### Privacy

Memo content is stored plaintext. This is a conscious decision disclosed in the privacy policy: encryption would break server-side AI access to the vault, which is the core product. No third-party analytics.

---

## Tech stack at a glance

| Layer | Choice |
|---|---|
| iOS app | Swift / SwiftUI, SwiftData |
| Backend | Convex (TypeScript) |
| AI — capture | Anthropic Claude / OpenAI (small model) |
| AI — retrieve | Anthropic Claude / OpenAI (switchable) |
| Auth | Anonymous UUID → Sign in with Apple or Google |
| Subscriptions | RevenueCat |
| Remote config | Convex `remoteConfig` table, live-subscribed |
