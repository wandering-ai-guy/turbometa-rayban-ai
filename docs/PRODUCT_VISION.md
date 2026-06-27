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

You speak or type an intent — *"reply to Sarah that I'll be 10 minutes late and move our 3pm to
4pm,"* or, looking at a shelf through your glasses, *"add the two items I'm out of to my grocery
list"* — and Halo understands the request, draws on what it already knows about you, decides which
tools to use, executes, and confirms what it did. The input can be voice or text. The input device
can be glasses, phone, or desktop. The output is a **completed action**, not just an answer.

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
2. **Trust is earned per action.** Speed means nothing if users don't trust the result. Default to
   *show-the-plan, confirm, then execute* for anything consequential; make confirmation frictionless
   and make **undo** always available.
3. **One brain, many surfaces.** Voice and text are equal first-class inputs. Glasses, phone, and
   desktop are *surfaces* into the same agent and the same memory — never separate products.
4. **Memory in service of action.** We remember only what makes the next action better or the next
   confirmation shorter. We are not building a journaling app first; memory is a means, not the end.
5. **Reliability over surface area.** Five integrations that work every time beat fifty that work
   sometimes. We expand the tool belt only when the existing tools are boringly reliable.
6. **Privacy is a feature, not a disclaimer.** The agent touches messages, calendars, and a camera
   feed. Users must understand what is captured, where it goes, what's retained, and be able to
   delete it. This is a precondition to the trust principle, not an afterthought.

---

## 3. What Halo *is* and *is not*

### Halo IS

- A **voice- and text-driven agent** that executes actions across a small, curated set of
  high-trust integrations.
- A **single agent across devices** — start a request on glasses, confirm on phone, see history on
  desktop.
- **Context-aware**: it uses lightweight memory (recent conversations, your people/places, your
  preferences, captured items) to fill in the blanks so you say less.
- **Multimodal at the edge**: it can use the glasses camera as an input ("what am I looking at,"
  "add this to my list") when vision changes the action.
- **Transparent and reversible**: it shows what it's about to do, asks when it matters, and lets
  you undo.

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
- ❌ **A new piece of hardware.** We build on Ray-Ban Meta + phone + (optionally) existing omi
  devices. No new firmware/device program in the first build.
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
| Hands-free voice trigger (Siri / App Intents) | turbometa | Primary "wake + speak" entry point | ✅ Yes |
| Low-latency realtime voice (Omni-Realtime / Gemini Live) | turbometa | Conversational input/output for the agent | ✅ Yes (one provider) |
| Glasses camera snap / vision | turbometa | Vision input when it changes the action | ◑ Thin slice (one flow) |
| Action gateway / tool execution (OpenClaw node + `chat.send`) | turbometa | **The execution spine** | ✅ Yes — central |
| Multi-provider model routing (OpenRouter) | turbometa | Swap models for the agent/orchestrator | ✅ Yes (config) |
| Persistent memory / "2nd brain" | omi | Context store that powers actions | ◑ Lightweight only |
| Action-item extraction | omi | Turning captured intent into tasks/actions | ✅ Yes (core concept) |
| Integrations / MCP / app ecosystem | omi | The tool belt the agent can call | ◑ 3–5 first-party tools |
| Always-on transcription + diarization | omi | Ambient capture | ❌ Deferred |
| Wearable/desktop/mobile clients | both | Surfaces into one agent | ◑ Phone + glasses first |
| RTMP streaming / nutrition / translation | turbometa | Adjacent features | ❌ Out of scope |

Legend: ✅ in first build · ◑ partial / thin slice · ❌ deferred or out of scope.

---

## 5. Feature set

### 5.1 Headline features (define the product)

1. **Intent → Action loop.** Speak or type a request; Halo parses intent, gathers context, proposes
   a plan, confirms when needed, executes via tools, and reports the outcome.
2. **The Tool Belt.** A curated set of first-party action integrations the agent can call (see
   §6 for the first set). Each tool is a typed, permissioned capability with a confirm/undo story.
3. **Trust & Control layer.** Plan preview, one-tap/one-word confirm, dry-run, undo, and a clear
   per-tool permission model. The user is always able to see and reverse what happened.
4. **Context memory.** A lightweight, user-visible store of the facts that make the next request
   shorter: people, recurring places, preferences, and a rolling window of recent
   conversations/actions. Editable and deletable by the user.
5. **Multi-surface continuity.** One identity, one memory, one action history across glasses, phone,
   and desktop. Begin by voice on glasses, confirm a consequential action on the phone.

### 5.2 Supporting features

- **Action history / receipt log.** Every executed action is recorded with inputs, the plan, and
  the result — the substrate for trust, debugging, and undo.
- **Vision-assisted actions.** When the request needs to see ("add what I'm out of," "log this
  receipt"), pull a frame from the glasses and feed it to the action.
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

We build the **spine top-to-bottom** for a **handful of actions**, on **two surfaces**, with
**trust built in** — and nothing else.

### 6.1 The slice, concretely

**Input** → **Understanding** → **Plan** → **Confirm** → **Execute** → **Confirm-back** → **Remember**

1. **Input (voice + text).** Invoke the agent hands-free on glasses (reuse the existing
   Siri/App-Intent + realtime voice path) or by text on the phone. One realtime voice provider is
   enough to start (the repo already integrates Omni-Realtime and Gemini Live).
2. **Understanding (the Orchestrator).** A single agent/orchestrator that takes the utterance +
   lightweight context and produces a **structured intent** and a **plan of tool calls**. This is
   the new core component. It builds directly on the existing OpenClaw `chat.send` → streamed
   response loop and tool-invocation pattern (`OpenClawCommandRouter`).
3. **Plan & Confirm.** For any consequential action, render a short, human-readable plan
   ("Send message to Sarah: '…'", "Create event 'X' Fri 4pm") and require a one-word/one-tap
   confirm. Trivial/read-only actions can auto-run.
4. **Execute (the Tool Belt — start with 3).** Implement a small set of typed tools with real
   side effects. Proposed first three (pick the subset that fits available APIs/credentials):
   - **Reminders / tasks** — capture and create a task/reminder (the "action item" from omi, made
     real).
   - **Calendar** — create/move/cancel an event.
   - **Messaging** — send a message/note to a person (start with the lowest-risk channel available;
     a draft-and-confirm flow before send).
   - *(Stretch, only if trivial)* **Capture-to-list via vision** — one glasses-camera flow that
     turns "add this" into a list/reminder item.
5. **Confirm-back.** Report the outcome by voice (TTS, already in repo) and in the action log.
6. **Remember (minimal).** Persist the action receipt and a tiny amount of context (e.g. "Sarah =
   this contact," "my 'list' = this list") so the next request is shorter. No full memory engine.

### 6.2 Cross-cutting must-haves for the first build

- **The tool-call contract.** A clean internal interface: `intent → [typed tool calls] → results`,
  with each tool declaring its parameters, whether it's read-only vs. consequential, and how to
  undo. This is the architectural keystone everything else hangs off. Model it on the existing
  OpenClaw node/command pattern so we reuse the gateway plumbing.
- **Trust primitives.** Plan preview, confirm, dry-run, and undo are part of v1 — not a later
  "polish" pass. They are what makes the slice usable by a real person.
- **Permissions & privacy.** Per-tool consent; clear statement of what's captured (esp. camera/mic)
  and retained; user-visible + deletable action log and context. Reuse Keychain-based secret
  storage already in the repo.
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
- **It produces a demoable, lovable unit.** "I told my glasses to reschedule a meeting and it did,
  and showed me before sending" is a complete, evaluable story.

### 6.4 First-build success criteria

We will know the first build is right when, for the chosen 3 tools:

- **Task success rate**: a high fraction of clearly-phrased requests complete correctly end-to-end
  without manual fixup.
- **Trust signals**: confirmations feel fast (not nagging); undo is rarely *needed* but always
  *works*; users report they'd let it run the next action.
- **Wrong-action rate ≈ 0** for consequential actions reaching execution (the confirm/undo layer
  catches mistakes before they matter).
- **Cross-surface continuity** works: start on glasses, confirm on phone, see it in the log.

### 6.5 Explicit non-goals of the first build

No always-on capture, no full memory browser, no >5 tools, no third-party/developer tooling, no
background autonomy, no new hardware, no streaming/nutrition/translation features. If a proposed
task isn't on the input→action spine for the first 3 tools, it waits.

---

## 7. Key risks & open questions (to resolve while building the slice)

- **Reliability of intent parsing.** Natural language is ambiguous. Mitigation: narrow the tool set,
  lean on plan-preview + confirm, and disambiguate by asking one short question rather than guessing.
- **Trust calibration.** Too many confirmations = annoying; too few = scary. Open question: which
  actions are "consequential"? Start conservative (confirm sends/changes; auto-run reads/captures).
- **Latency budget.** Voice → plan → confirm → execute must feel immediate. Open question: end-to-end
  target and where to cut round-trips (the realtime voice path helps).
- **Edge capability limits.** The Meta DAT SDK can't access the glasses camera in the background
  (documented in this repo), which constrains hands-free vision flows. Design the vision slice
  around foreground use.
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
