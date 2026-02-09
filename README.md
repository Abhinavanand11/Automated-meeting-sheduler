# Automated-meeting-sheduler
# Automated Interview Scheduling Chatbot (n8n + OpenAI + Google Calendar)

This project implements an **AI-powered interview scheduling chatbot** using **n8n**, **OpenAI (GPT-4o-mini)**, and **Google Calendar**.

The chatbot converses naturally with candidates, understands human-friendly date expressions (e.g. *“next Tuesday”*), checks interviewer availability, avoids double-booking, schedules a 30-minute interview, and sends confirmation — fully automated.

---

## Features

- Natural language chat-based scheduling
- Google Calendar availability checking
- Automatic 30-minute slot generation
- Time zone aware (Eastern Time, EST/EDT)
- Context-aware multi-turn conversation
- Prevents double booking
- Does not reveal interviewer calendar details
- Auto-creates calendar events and confirms with the candidate

---

## Architecture Overview

The workflow is composed of **three main layers**:

1. **Conversation & Reasoning (LLM Agent)**
2. **Calendar Availability Engine**
3. **Booking & Confirmation**


---

## Core Components

### 1️⃣ Chat Trigger
**Node:** `When chat message received`

- Entry point for user messages
- Exposes the chatbot via webhook
- Passes messages to the LLM agent

---

### 2️⃣ Memory Buffer
**Node:** `Window Buffer Memory`

- Stores last 10 chat turns
- Enables multi-step conversations
- Prevents re-asking already collected details

---

### 3️⃣ Interview Scheduler Agent
**Node:** `Interview Scheduler`

Powered by **GPT-4o-mini**, this agent:

- Collects:
  - Email
  - Phone number
  - Preferred date
  - Preferred time
- Negotiates availability
- Enforces rules:
  - Interviews are **30 minutes**
  - Do not double book
  - Do not expose calendar details
- Calls tools instead of guessing

#### Available Tools
| Tool | Purpose |
|----|----|
| `get_availability` | Fetch interviewer’s free time slots |
| `check_days` | Resolve phrases like “next Tuesday” |

---

## Availability Engine (`get_availability`)

This sub-workflow computes free interview slots.

### Steps:
1. Fetch all Google Calendar events
2. Split busy events into **30-minute blocks**
3. Generate all possible business-hour slots:
   - Weekdays only
   - 9 AM – 5 PM ET
   - Next 7 days
4. Merge busy slots with possible slots
5. Remove blocked times
6. Return only **available slots**


