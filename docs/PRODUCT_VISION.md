# Product Vision & First-Build Plan

> Working codename: **Halo** (placeholder — not final branding)
>
> Status: Vision + scope + plan for the first build. **This is intentionally not a roadmap.**
> It defines what we are building, why, where the line is, and what we build *first*.

This document is the product brief for a project that combines the strengths of two open-source
projects into one coherent product:

- **omi** ([BasedHardware/omi](https://github.com/BasedHardware/omi)) — "a 2nd brain you trust
  more than your 1st." Always-on multimodal *capture*, real-time transcription, persistent
  *memory*, action-item extraction, and an *integration / MCP* ecosystem that lets an AI act on
  your behalf across your tools.
- **turbometa-rayban-ai** (this repo) — a full-modal AI assistant for Ray-Ban Meta glasses:
  hands-free voice (Siri / App Intents), camera vision, low-latency realtime voice models,
  TTS, and — critically — a working **action-execution spine** via the OpenClaw gateway
  (`OpenClawNodeService` exposes the glasses as a device *node*, and `chat.send` ships
  text + images to an AI that can call tools and stream back results).

One project is great at **sensing and remembering**. The other is great at **a hands-free edge
that can already trigger and execute actions**. The product is the union: an assistant you talk
or type to that **understands your context and gets things done**.

---

## 1. Vision

**Halo is a personal AI agent that turns what you say or type into actions you can trust.**

You speak or type an intent — *"WhatsApp Sarah that I'll be 10 minutes late,"* *"text Mom I landed,"*
*"call the dentist,"* or *"do I have anything planned with Raj this week?"* — and Halo understands
the request, draws on what it already knows about you (including your recent emails and messages),
decides which tools to use, executes, and confirms what it did. The input can be voice or text. The
primary device is your **phone**, extended to any Bluetooth wearable (glasses, earbuds, band,
pendant). The output is a **completed everyday action**, not just an answer.

Today's assistants mostly *talk*. omi made an assistant that *remembers*. turbometa made one that
*sees and is hands-free*. Halo's job is to **act** — to be the reliable bridge between an
instruction and a real outcome in your digital and physical life.

### The one-sentence thesis

> If we make natural-language → **trustworthy executed action** reliable for a small set of
> high-value tasks, the capture, memory, and device breadth become accelerants — not the product.

### Why now

- Realtime multimodal voice models (e.g. Qwen Omni-Realtime, Gemini Live — both already wired into
  this repo) make natural, low-latency voice the default interface.
- Tool-calling + protocols like MCP and gateways like OpenClaw make "let the model call your tools"
  a real, composable capability rather than a demo.
- Wearables (Ray-Ban Meta, omi devices) put a capture-and-act surface on the body, so intent can be
  expressed in the moment it occurs.

### Who it's for (first)

The **"busy operator"**: someone with a dense calendar, overflowing inbox/messages, and many small
recurring tasks (scheduling, follow-ups, reminders, capturing notes/items into the right place).
They lose time to *coordination overhead*, not to lack of information. They will trade a little
control for reliably offloaded micro-tasks — **if** they trust the agent not to do the wrong thing.

---

## 2. Product principles

These are the tie-breakers when we make decisions. They are opinionated on purpose.

1. **Action is the product.** Every feature is judged by whether it helps turn an intent into a
   completed, correct action. "Nice answer" is not the bar; "it's done" is.
2. **Trust is earned per action.** Speed means nothing if users don't trust the result. Anything
   that **sends or changes** the world requires *explicit* user confirmation before it runs. Actions
   that only **read or capture** can auto-run — but Halo **reiterates its understanding back to you**
   before storing that knowledge or acting on top of it, so a wrong assumption never silently
   compounds. **Undo** is always available.
3. **One brain, many surfaces.** Voice and text are equal first-class inputs. Glasses, phone, and
   desktop are *surfaces* into the same agent and the same memory — never separate products.
4. **Memory in service of action.** We remember only what makes the next action better or the next
   confirmation shorter. We are not building a journaling app first; memory is a means, not the end.
5. **Reliability over surface area.** Five integrations that work every time beat fifty that work
   sometimes. We expand the tool belt only when the existing tools are boringly reliable.
6. **Privacy is a feature, not a disclaimer.** The agent touches messages, inboxes, calls, and a camera
   feed. Users must understand what is captured, where it goes, what's retained, and be able to
   delete it. This is a precondition to the trust principle, not an afterthought.

---

## 3. What Halo *is* and *is not*

### Halo IS

- A **voice- and text-driven agent** that executes the **everyday smartphone actions** people
  already do by hand — messaging, calling, email, reminders — across a small, curated set of
  high-trust integrations.
- A **phone-first agent that extends to wearables** — run it on your phone, and reach it
  hands-free through any paired Bluetooth device (glasses, earbuds, band, pendant). One identity,
  one memory, one action history across all of them.
- **Context-aware**: it uses lightweight memory (your people, your recent emails/messages,
  preferences, planned activities) to fill in the blanks so you say less.
- **Multimodal where it helps**: voice and text are equal; on capable wearables it can use the
  camera as input when vision changes the action.
- **Transparent and reversible**: it shows what it's about to do, asks before anything that sends
  or changes, reiterates what it understood before storing it, and lets you undo.

### Halo IS NOT (for the first build)

- ❌ **An always-on ambient recorder of every conversation.** omi's continuous capture is powerful
  but is the highest-risk, highest-cost surface (privacy, battery, infra). We defer it. Capture is
  **intent-initiated** first (you invoke the agent), not 24/7 passive.
- ❌ **A general chatbot / search engine.** If a request is just "answer a question," that's a
  fallback, not the headline. We are not competing on open-domain Q&A.
- ❌ **A note-taking / second-brain app.** We will not lead with summaries, journals, or a memory
  browser. Memory exists to power actions.
- ❌ **A livestreaming / content-creation tool.** turbometa's RTMP streaming, nutrition (LeanEat),
  and translation are useful but orthogonal to the "execute actions" thesis. They are **not** part
  of the first build.
- ❌ **A platform / SDK / third-party app marketplace.** omi has an apps + MCP ecosystem. We will
  *consume* a protocol (MCP / OpenClaw-style tools) internally, but we will not open a developer
  platform until our own first-party tools are rock-solid.
- ❌ **A new piece of hardware.** We build on the **phone first**, then existing Bluetooth wearables
  (Ray-Ban Meta, earbuds, bands, pendants, omi devices). No new firmware/device program in the
  first build.
- ❌ **Fully autonomous / unattended automation.** No background "agent does things while you sleep"
  in v1. Actions are initiated by a user intent and confirmed when consequential.

> The discipline of this section is the point. The risk in merging two ambitious projects is
> building *everything* and shipping *nothing trustworthy*. The "is not" list is how we stay
> pointed at the thesis.

---

## 4. Capabilities map (the union of the two projects)

To make the scope concrete, here's how the two codebases' capabilities map onto Halo, and which we
pull forward into the first build.

| Capability | Origin | Role in Halo | In first build? |
|---|---|---|---|
| Hands-free voice trigger (Siri / App Intents) | turbometa | "Wake + speak" entry point, from phone or wearable | ✅ Yes |
| Low-latency realtime voice (Omni-Realtime / Gemini Live) | turbometa | Conversational input/output for the agent | ✅ Yes (one provider) |
| Camera snap / vision | turbometa | Vision input when it changes the action | ◑ Thin slice (capable wearable) |
| Action gateway / tool execution (OpenClaw node + `chat.send`) | turbometa | **The execution spine** | ✅ Yes — central |
| Multi-provider model routing (OpenRouter) | turbometa | Swap models for the agent/orchestrator | ✅ Yes (config) |
| Persistent memory / "2nd brain" | omi | Context store that powers actions | ◑ Lightweight only |
| Action-item extraction | omi | Turning captured intent + emails/messages into tasks/reminders | ✅ Yes (core concept) |
| Integrations / MCP / app ecosystem | omi | The tool belt the agent can call | ◑ Everyday-phone tools only |
| Always-on transcription + diarization | omi | Ambient capture | ❌ Deferred |
| Phone client | both | **Primary surface** | ✅ Yes |
| Bluetooth wearables (glasses, earbuds, band, pendant) | both | Hands-free surfaces into the same agent | ◑ One wearable path first |
| Desktop client | omi | Additional surface | ❌ Deferred |
| RTMP streaming / nutrition / translation | turbometa | Adjacent features | ❌ Out of scope |

Legend: ✅ in first build · ◑ partial / thin slice · ❌ deferred or out of scope.

---

## 5. Feature set

### 5.1 Headline features (define the product)

1. **Intent → Action loop.** Speak or type a request; Halo parses intent, gathers context, proposes
   a plan, confirms when needed, executes via tools, and reports the outcome.
2. **The Everyday Tool Belt.** A curated set of first-party integrations for the basic smartphone
   actions people already do daily — WhatsApp messages, SMS, phone calls, WhatsApp calls, Gmail and
   Outlook email, and reminders (see §6 for the first set). Each tool is a typed, permissioned
   capability with a confirm/undo story.
3. **Trust & Control layer.** Explicit confirmation for anything that sends or changes; reiterate
   understanding before storing/acting on read-or-capture results; plus plan preview, dry-run, undo,
   and a clear per-tool permission model. The user is always able to see and reverse what happened.
4. **Context memory.** A lightweight, user-visible store of the facts that make the next request
   shorter: your people, planned activities, preferences, and signals drawn from recent
   emails/messages. Editable and deletable by the user.
5. **Phone-first, wearable-extended continuity.** One identity, one memory, one action history. The
   phone is the home base; any paired Bluetooth wearable (glasses, earbuds, band, pendant) is a
   hands-free way to reach the same agent — speak from the wearable, confirm a consequential action
   on the phone.

### 5.2 Supporting features

- **Action history / receipt log.** Every executed action is recorded with inputs, the plan, and
  the result — the substrate for trust, debugging, and undo.
- **Vision-assisted actions.** When the request needs to see ("log this receipt," "what's this") and
  a camera-capable wearable is connected, pull a frame and feed it to the action.
- **Provider/model configuration.** Reuse the existing multi-provider key management so the
  orchestrator model and the realtime voice model are configurable.

### 5.3 Explicitly deferred (named, so we don't quietly scope-creep)

Always-on ambient capture · full memory browser/search · third-party tool marketplace/SDK ·
unattended/background automations · streaming/nutrition/translation · new hardware ·
team/multi-user/shared agents · proactive suggestions ("you should…") without a user request.

---

## 6. What we build *first* (the first vertical slice)

The first build is **not** "port everything from both repos." It is **one thin, end-to-end vertical
slice that proves the riskiest assumption**: *can we reliably turn a natural-language voice/text
request into a correctly executed, trusted action?*

We build the **spine top-to-bottom** for the **everyday smartphone actions**, **phone-first** (then
one Bluetooth-wearable path), with **trust built in** — and nothing else.

### 6.1 The slice, concretely

**Input** → **Understanding** → **Plan** → **Confirm** → **Execute** → **Confirm-back** → **Remember**

1. **Input (voice + text), phone-first.** The phone is the home base: type or speak to the agent.
   Then add **one** hands-free wearable path (reuse the existing Siri/App-Intent + realtime voice
   pipeline) so you can invoke the same agent from a paired Bluetooth device. One realtime voice
   provider is enough to start (the repo already integrates Omni-Realtime and Gemini Live).
2. **Understanding (the Orchestrator).** A single agent/orchestrator that takes the utterance +
   lightweight context and produces a **structured intent** and a **plan of tool calls**. This is
   the new core component. It builds directly on the existing OpenClaw `chat.send` → streamed
   response loop and tool-invocation pattern (`OpenClawCommandRouter`).
3. **Plan & Confirm (per the trust policy).**
   - **Send/change actions** (send a WhatsApp/SMS, place a call, send an email, create/modify a
     reminder) render a short, human-readable plan — *"WhatsApp to Sarah: 'running 10 min late'"*,
     *"Call dentist (+1 …)"*, *"Reminder: pick up parcel, today 6pm"* — and require **explicit
     confirmation** (a tap, or a spoken "yes/send") before anything happens.
   - **Read/capture actions** (scan recent Gmail/Outlook for context, read planned activities) can
     **auto-run**, but Halo then **reiterates what it understood** — *"Looks like you have a call
     with Raj Thursday 3pm and an unpaid invoice from Acme — want me to remember those?"* — before
     storing that knowledge or acting on top of it.
4. **Execute — the Everyday Tool Belt.** Typed tools for the basic smartphone features people use
   daily, grouped by capability. Each tool is classified by trust policy (read vs. send):
   - **Email — context & planning (read-first):** **Gmail** and **Outlook** via official APIs
     (Gmail API / Microsoft Graph, OAuth). Primary first use is *read for context and planned
     activities* (auto-run + reiterate). Sending email is a send-action (explicit confirm).
   - **Reminders / tasks:** create and manage reminders (omi's "action item" made real). Creating =
     a change → explicit confirm; the underlying capture/extraction = reiterate-before-store.
   - **Messaging (send):** **WhatsApp messages** and **SMS** — draft-and-confirm before send.
   - **Calls (initiate):** **regular phone calls** and **WhatsApp calls** — confirm before dialing.
5. **Confirm-back.** Report the outcome by voice (TTS, already in repo) and in the action log.
6. **Remember (minimal).** Persist the action receipt and a tiny amount of context (e.g. "Sarah =
   this contact," "Raj sync = Thu 3pm") so the next request is shorter — only after the
   reiterate-and-confirm step for captured knowledge. No full memory engine.

> **Sequencing within the build (reliability-first, not a roadmap):** because send/call channels
> carry the most risk and the most platform friction, harden the spine on the **lowest-risk path
> first** — email *read* for context + reminders — then layer in the send/call channels (SMS,
> WhatsApp message, phone call, WhatsApp call) once plan→confirm→execute→undo is boringly reliable.

### 6.1a Platform integration realities (and why they *help* the trust model)

These everyday channels have real mobile-platform constraints. Naming them up front, because they
shape the design — and several actually reinforce the "confirm before send" principle:

- **Phone calls:** straightforward to initiate via the OS (`tel:`); we confirm, then dial.
- **SMS:** iOS prefills a message the user sends (no silent send); Android can send directly with
  permission. The iOS hand-off *is* the confirmation step.
- **WhatsApp messages:** no official personal-account send API. Use deep-link prefill
  (`wa.me` / share) so WhatsApp opens with the drafted message and the user taps send — again, a
  built-in confirm. (WhatsApp Business Cloud API is a separate, business-number path; out of scope.)
- **WhatsApp calls:** the most constrained — no official API to place a WhatsApp call
  programmatically. Likely limited to deep-linking into the contact for a user-initiated tap; flag
  as the **highest-risk / lowest-support** tool and validate feasibility early.
- **Gmail / Outlook:** robust official APIs (OAuth) for reading context and sending — the solid
  backbone of the "context & planned activities" capability.
- **Reminders:** native task/reminder providers (e.g. EventKit on iOS) are reliable.

### 6.2 Cross-cutting must-haves for the first build

- **The tool-call contract.** A clean internal interface: `intent → [typed tool calls] → results`,
  with each tool declaring its parameters, whether it's read-only vs. consequential, and how to
  undo. This is the architectural keystone everything else hangs off. Model it on the existing
  OpenClaw node/command pattern so we reuse the gateway plumbing.
- **Trust primitives.** Explicit confirm for send/change, reiterate-before-store for read/capture,
  plus plan preview, dry-run, and undo — all part of v1, not a later "polish" pass. They are what
  makes the slice usable by a real person.
- **Permissions & privacy.** Per-tool consent (these tools touch messages, calls, and inboxes —
  sensitive scopes); clear statement of what's read/retained; user-visible + deletable action log
  and context. Reuse Keychain-based secret storage already in the repo for OAuth tokens/keys.
- **Observability.** Structured logs of intent → plan → tool result so we can measure success/undo
  rates and debug failures. We cannot improve reliability we can't see.

### 6.3 Why this is the right first build

- **It de-risks the thesis.** Everything else (ambient capture, big memory, more devices, a
  platform) only matters *if* the action loop is trustworthy. Prove that first.
- **It reuses the strongest existing asset.** turbometa already has a functioning gateway/tool
  pathway (OpenClaw) plus hands-free voice and TTS. We extend a working spine instead of inventing
  one.
- **It imports omi's best idea cheaply.** "Action items" become *executed* actions — without
  needing omi's full always-on capture + memory infrastructure on day one.
- **It produces a demoable, lovable unit.** "I told my phone to WhatsApp Sarah I'm running late and
  it showed me the draft before sending, then reminded me about the parcel it spotted in my email"
  is a complete, evaluable everyday story.

### 6.4 First-build success criteria

We will know the first build is right when, for the everyday tool belt:

- **Task success rate**: a high fraction of clearly-phrased requests complete correctly end-to-end
  without manual fixup.
- **Trust signals**: send/change confirmations feel fast (not nagging); the reiterate-before-store
  step catches wrong assumptions; undo is rarely *needed* but always *works*; users report they'd
  let it run the next action.
- **Wrong-action rate ≈ 0** for send/change actions reaching execution (confirm + undo catch
  mistakes before they matter), and **no silent storage of a misunderstood fact**.
- **Phone-first + one wearable path** both work: complete a task by text/voice on the phone, and
  invoke the same agent hands-free from a paired Bluetooth device; confirm a send-action on the
  phone.

### 6.5 Explicit non-goals of the first build

No always-on capture, no full memory browser, no tools beyond the everyday smartphone belt, no
third-party/developer tooling, no background autonomy, no new hardware, no
streaming/nutrition/translation features. If a proposed task isn't on the input→action spine for the
everyday tool belt, it waits.

---

## 7. Key risks & open questions (to resolve while building the slice)

- **Reliability of intent parsing.** Natural language is ambiguous. Mitigation: narrow the tool set,
  lean on plan-preview + confirm, and disambiguate by asking one short question rather than guessing.
- **Trust calibration.** Too many confirmations = annoying; too few = scary. Policy is set: explicit
  confirm for send/change, auto-run + reiterate-before-store for read/capture. Remaining tuning:
  make the confirm a single tap or spoken word, and keep the reiteration to one short sentence.
- **Channel feasibility (highest near-term risk).** WhatsApp has no official personal send/call API
  and SMS sending is OS-restricted; many channels can only *hand off* to the native app for the
  final tap. Validate each channel's real capability early — especially **WhatsApp calls**, which
  may not be programmatically initiable at all. Email (Gmail/Graph) is the most fully controllable.
- **OAuth & permissions friction.** Gmail/Outlook need OAuth consent and sensitive scopes (and
  Google/Microsoft app-verification review). Plan for the consent flow and review process; start
  with read scopes.
- **Latency budget.** Voice → plan → confirm → execute must feel immediate. Open question: end-to-end
  target and where to cut round-trips (the realtime voice path helps).
- **Wearable capability limits.** Bluetooth wearables differ widely (earbuds = audio only; glasses =
  camera but, per the Meta DAT SDK, no background camera access). Pick **one** wearable path for the
  first build and design around its real capabilities.
- **Memory scope discipline.** Easy to over-build memory. Guardrail: only store a fact if a concrete
  near-term action would use it.
- **Privacy/data residency.** Multiple model providers and regions are already in play; be explicit
  with users about where audio/images/text go per provider.

---

## 8. How we'll evaluate "did we build the right thing"

- A real "busy operator" can, in one sitting, complete several of their genuine recurring
  micro-tasks by voice/text — and *chooses* to keep using it the next day.
- The action log shows high success and near-zero wrong-actions on consequential tasks.
- We can add a 4th tool quickly because the tool-call contract held up — evidence the foundation is
  sound (and the cue that we're ready to talk about *what comes next* — i.e., the roadmap, which is
  deliberately out of scope for this document).
