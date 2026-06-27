# Product Vision & First-Build Plan

> Working codename: **Halo** (placeholder — not final branding)
>
> Status: Vision + scope + plan for the first build. **This is intentionally not a roadmap.**
> It defines what we are building, why, where the line is, what we build *first* — and a
> first-principles critique of the plan itself (§9).

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
that can already trigger and execute actions**. The product is their union: an assistant that
**listens to the conversations that run your day, remembers them, and acts on them for you**.

---

## 1. Vision

**Halo is a persistent memory that takes action — it listens to your calls and chats, remembers
what matters, and gets the follow-ups and reminders done so you don't have to hold it all in your
head.**

You spend your day on phone calls and WhatsApp threads. Decisions get made, promises get exchanged,
"I'll send you that," "let's talk Tuesday," "remind me to chase the invoice." Today all of that
lives in your head, and you pay for it in dropped balls and low-grade anxiety. Halo captures those
conversations (with consent), turns them into durable memory, and — **when you ask** — drafts the
follow-up message, sets the reminder, books the calendar slot, and chases the loose ends. The input
can be voice or text. The primary device is your **phone**, extended to any Bluetooth wearable
(glasses, earbuds, band, pendant). The output is a **completed everyday action backed by memory** —
not just an answer.

### The one-sentence thesis

> **Action is the product; persistent memory is the engine.** If we can capture the conversations
> that drive someone's day, remember them reliably, and turn an intent into a trustworthy executed
> follow-up, we free up the brain capacity people currently spend remembering and coordinating.

### Who it's for (first)

An **ordinary individual** who lives on the phone and WhatsApp and plans their day and their
business around those conversations. Halo is for them when it:

- **Frees up brain capacity** — they no longer have to remember everything that was said and
  promised; Halo holds it.
- **Takes action on their behalf** — sends the reminders and follow-ups that come out of those
  conversations, so the loop actually closes.
- **Reduces the cognitive load of coordinating with people on unpleasant matters** — the chasing,
  the nudging, the awkward "just following up" — Halo drafts and (on confirm) sends it, so the
  person doesn't have to carry the discomfort of initiating it.

This person is not a "power user." They are anyone for whom *remembering and following up* is the
real daily tax — a small-business owner, a freelancer, a busy parent coordinating a household, a
salesperson juggling threads. **Action is the product, with a persistent memory underneath it.**

### Why now

- Realtime, low-latency speech-to-text and multimodal models (e.g. Qwen Omni-Realtime, Gemini Live
  — both already wired into this repo) make continuous transcription and natural voice interaction
  practical.
- Tool-calling + protocols like MCP and gateways like OpenClaw make "let the model call your tools"
  a real, composable capability rather than a demo.
- Body-worn and phone-paired capture (omi devices, Ray-Ban Meta) put an always-on listening surface
  where conversations actually happen.

---

## 2. Product principles

These are the tie-breakers when we make decisions. They are opinionated on purpose.

1. **Action is the product.** Every feature is judged by whether it helps turn a captured intent
   into a completed, correct action. "Nice transcript" is not the bar; "the follow-up is sent and
   the reminder is set" is.
2. **Memory is the engine, not the destination.** We capture and remember so the next action is
   faster and the next nudge is unnecessary. We are not building a recordings browser; memory earns
   its place only by making action better.
3. **Trust is earned per action — and per recording.** Anything that **sends or changes** the world
   requires *explicit* user confirmation. Anything we **capture or read** is summarized back to the
   user — *"here's what I understood"* — before it is stored or acted upon, so a wrong assumption
   never silently compounds. **Undo** is always available, and the user can delete any memory.
4. **Consent is the precondition for capture.** An always-on recorder that records other people is
   the most sensitive thing this product does. Capture must be consented, lawful in the user's
   jurisdiction, obvious (not covert), and minimizable (transcribe-and-discard-audio by default).
   If we cannot do capture respectfully and legally, we do not do it.
5. **Reliability over surface area.** A handful of integrations that work every time beat many that
   work sometimes. We expand the tool belt only when the existing tools are boringly reliable.
6. **One brain, many surfaces.** Voice and text are equal first-class inputs. Phone is home base;
   wearables are hands-free windows into the *same* agent and the *same* memory — never separate
   products.

---

## 3. What Halo *is* and *is not*

### Halo IS

- A **persistent memory of your conversations** — phone calls, WhatsApp chats, and ambient talk —
  captured with consent and turned into durable, searchable context.
- An **always-on ambient recorder** whose job is to *capture and remember* continuously, while
  **actions are initiated on user intent** (see "IS NOT" below) for the first iteration.
- An **action-taker**: on your request, it drafts and (on confirm) sends WhatsApp/SMS messages and
  emails, sets reminders, and reads/writes your calendar — closing the loops that came out of your
  conversations.
- A **follow-up and coordination assistant** that lowers the cognitive (and emotional) cost of
  chasing people on unpleasant matters by drafting the nudge for you.
- **Phone-first, wearable-extended**: home base on the phone, reachable hands-free from any paired
  Bluetooth device. One identity, one memory, one action history.
- **Transparent and reversible**: it shows what it's about to do, asks before anything that sends or
  changes, reiterates what it understood before storing it, and lets you undo or delete.

### Halo IS NOT (for the first iteration)

- ❌ **A caller / dialer.** Halo does **not** initiate phone or WhatsApp calls. It *listens to and
  transcribes* ongoing calls (with consent) to build context, set reminders, and execute
  follow-ups — it never places a call on your behalf.
- ❌ **Fully autonomous.** Capture is continuous, but **the agent does not act on its own**. Every
  send/change is initiated by an explicit user intent and confirmed. No background "it did things
  while you slept" in v1.
- ❌ **A covert / no-consent recorder.** Capture is consented, lawful, and obvious — never secret.
- ❌ **A general chatbot / search engine.** Open-domain Q&A is a fallback, not the headline.
- ❌ **A recordings/transcripts browser as the product.** We store transcripts to power memory and
  action, not to sell a "review your day" reading experience.
- ❌ **A platform / SDK / third-party marketplace.** We *consume* a tool protocol (MCP /
  OpenClaw-style) internally; we do not open a developer platform until our first-party tools are
  rock-solid.
- ❌ **A new piece of hardware.** We build on the **phone first**, then existing Bluetooth wearables
  (Ray-Ban Meta, earbuds, bands, pendants, omi devices). No new firmware/device program in the
  first iteration. *(But see §9 — capture feasibility may force a wearable sooner than "phone-first"
  implies.)*
- ❌ **A livestreaming / nutrition / translation tool.** turbometa's RTMP streaming, LeanEat, and
  translation are orthogonal to the thesis and out of scope.

> The discipline of this section is the point. The risk in merging two ambitious projects is
> building *everything* and shipping *nothing trustworthy*. The "is not" list is how we stay
> pointed at the thesis.

---

## 4. Capabilities map (the union of the two projects)

How the two codebases' capabilities map onto Halo, and which we pull forward into the first build.

| Capability | Origin | Role in Halo | In first build? |
|---|---|---|---|
| Always-on transcription of conversations | omi | **Core capture spine** (calls + ambient) | ✅ Yes — central |
| Call audio → transcript (phone / WhatsApp calls) | new | Context source; **never** call initiation | ◑ Feasibility-gated (see §6.1a, §9) |
| Persistent memory / "2nd brain" | omi | Durable context that powers action | ✅ Yes — central |
| Action-item / follow-up extraction | omi | Turns conversations into reminders/follow-ups | ✅ Yes |
| Action gateway / tool execution (OpenClaw node + `chat.send`) | turbometa | **The execution spine** | ✅ Yes — central |
| Low-latency realtime voice / STT (Omni-Realtime / Gemini Live) | turbometa | Transcription + voice interaction | ✅ Yes (one provider) |
| Hands-free voice trigger (Siri / App Intents) | turbometa | "Wake + speak" from phone or wearable | ✅ Yes |
| Multi-provider model routing (OpenRouter) | turbometa | Swap models for orchestrator/STT | ✅ Yes (config) |
| Integrations / MCP / app ecosystem | omi | The tool belt the agent can call | ◑ Everyday-phone tools only |
| Camera / vision | turbometa | Visual context on capable wearables | ❌ Deferred |
| Phone client | both | **Primary surface** | ✅ Yes |
| Bluetooth wearables (glasses, earbuds, band, pendant) | both | Hands-free + capture surfaces | ◑ One wearable path; may be required for call capture |
| Desktop client | omi | Additional surface | ❌ Deferred |
| RTMP streaming / nutrition / translation | turbometa | Adjacent features | ❌ Out of scope |

Legend: ✅ in first build · ◑ partial / gated · ❌ deferred or out of scope.

---

## 5. Feature set

### 5.1 Headline features (define the product)

1. **Always-on conversation capture → memory.** Continuously transcribe the user's conversations
   (phone/WhatsApp calls and ambient talk, with consent), extract who/what/when/promises, and store
   them as durable, user-visible, deletable memory.
2. **Follow-up & reminder engine.** From those conversations, surface the loose ends ("you said
   you'd send Raj the quote", "chase Acme invoice Friday") and, on the user's intent, **draft and
   send the follow-up** or **set the reminder**.
3. **The Everyday Tool Belt.** First-party integrations for the basic things people do daily:
   WhatsApp messages, SMS, Gmail + Outlook email, native calendar (read + write), reminders, and
   call/conversation capture (transcript-only). See §6.
4. **Trust & Control layer.** Explicit confirmation for anything that sends or changes; reiterate
   understanding before storing/acting on captured content; plan preview, dry-run, undo, per-tool
   permissions, and one-tap memory deletion.
5. **Phone-first, wearable-extended continuity.** One identity, one memory, one action history; the
   phone is home base and any paired Bluetooth wearable is a hands-free way in.

### 5.2 Supporting features

- **Memory & action history / receipt log.** Every captured item and executed action is recorded
  with its source and result — the substrate for trust, undo, and "what did I promise?"
- **Provider/model configuration.** Reuse the existing multi-provider key management so the
  transcription model and orchestrator model are configurable.

### 5.3 Explicitly deferred (named, so we don't quietly scope-creep)

Call **initiation** · autonomous/background action · full memory-browser product · third-party tool
marketplace/SDK · camera/vision flows · desktop client · streaming/nutrition/translation · new
hardware · team/multi-user/shared agents · unsolicited proactive nudges.

---

## 6. What we build *first* (the first vertical slice)

The first build is **not** "port everything from both repos." It is **one thin, end-to-end vertical
slice that proves the riskiest assumption**: *can we capture the conversations that run someone's
day, remember them, and turn an intent into a trustworthy executed follow-up?*

We build the **capture → memory → intent-initiated action** spine top-to-bottom for the everyday
tool belt, **phone-first** (then one wearable path), with **trust and consent built in** — and
nothing else.

### 6.1 The slice, concretely

**Capture** → **Understand & Reiterate** → **Remember** → **Intent** → **Plan** → **Confirm** →
**Execute** → **Confirm-back**

1. **Capture (always-on, consented).** Continuously transcribe the user's conversations. Start with
   the **most feasible, lawful source** (see §6.1a) — likely on-device/ambient voice capture and
   the user's own dictation — and treat full two-party call-audio capture as feasibility-gated.
   Transcribe-and-discard-audio by default; store text.
2. **Understand & Reiterate.** Extract entities, commitments, and candidate follow-ups, then
   **summarize back** what was understood (*"Sounds like you promised Raj a quote by Thursday — want
   me to remember that and remind you Wednesday?"*) before anything is stored or acted upon.
3. **Remember (persistent memory).** Store the confirmed facts/commitments as durable, user-visible,
   editable, deletable memory. This is a real first-class store, not a scratchpad — it is the
   engine.
4. **Intent (voice + text), phone-first.** The user invokes an action by phone (type/speak) or
   hands-free from one paired wearable (reuse the Siri/App-Intent + realtime voice pipeline).
5. **Plan & Confirm (per the trust policy).**
   - **Send/change** (WhatsApp/SMS message, email, create/modify reminder, create/modify calendar
     event) → render a short human-readable plan and require **explicit confirmation**.
   - **Read/capture** (scan recent Gmail/Outlook, read calendar, read memory) → **auto-run**, then
     **reiterate** before storing or building on it.
6. **Execute — the Everyday Tool Belt.** Typed tools for daily smartphone features, classified by
   trust policy:
   - **Conversation capture (read-only, context):** **phone calls** and **WhatsApp calls** →
     transcripts for context, reminders, and follow-ups. **No call initiation, ever.**
   - **Email (read-first):** **Gmail** + **Outlook** via official APIs (Gmail API / Microsoft Graph,
     OAuth) — read for context & planned activities (auto-run + reiterate); sending = confirm.
   - **Calendar (read + write):** **native calendar** — read for planning context; create/move/
     cancel events on explicit confirm.
   - **Reminders / tasks:** create and manage reminders (the follow-up made real) — create = confirm.
   - **Messaging (send):** **WhatsApp messages** + **SMS** — draft-and-confirm before send.
7. **Confirm-back.** Report the outcome by voice (TTS, already in repo) and in the action log.

> **Sequencing within the build (reliability-first, not a roadmap):** harden the **lowest-risk,
> most-feasible path first** — ambient/dictated capture → memory → email-read + calendar-read +
> reminders — then layer in the send channels (SMS, WhatsApp message, email send, calendar write).
> Treat **call-audio capture as a parallel feasibility spike** (see §6.1a) rather than a blocker for
> the rest of the spine.

### 6.1a Platform & legal realities (these shape the design)

The capture premise collides with hard platform and legal limits. Naming them up front:

- **Phone / WhatsApp call audio is largely inaccessible to third-party apps.** On iOS, apps cannot
  tap the system call audio stream; native call recording (iOS 18.1+) lives only in Apple's Phone
  app and is not exposed to third parties. WhatsApp call audio is likewise closed. On Android,
  call-recording APIs have been restricted since Android 10 to preloaded dialer apps. **Realistic
  capture paths:** (a) an always-on **wearable/ambient mic** (omi pendant, glasses, earbuds) that
  hears speakerphone/in-room audio — which is exactly why a wearable matters; (b) speakerphone +
  device mic; (c) user-side dictation. We must validate these early — see §9.
- **Recording other people is legally regulated.** Many jurisdictions require one- or all-party
  consent (and GDPR-style rules apply to storing others' voice/data). Consent, lawfulness, and an
  audible/visible indicator are **product requirements**, not options.
- **WhatsApp / SMS sending:** WhatsApp has no official personal-account send API — use deep-link
  prefill (`wa.me` / share) so the user taps send (a built-in confirm). iOS SMS prefills for the
  user to send; Android can send with permission. These constraints *reinforce* confirm-before-send.
- **Gmail / Outlook / Calendar:** robust official OAuth APIs — the most fully controllable tools,
  and the dependable backbone of context + planning. Plan for OAuth consent + app-verification
  review; start with read scopes.

### 6.2 Cross-cutting must-haves for the first build

- **The tool-call contract.** A clean internal interface: `intent → [typed tool calls] → results`,
  each tool declaring its parameters, read-vs-send classification, and how to undo. Model it on the
  existing OpenClaw node/command pattern to reuse the gateway plumbing. This is the keystone.
- **The capture & memory pipeline.** Audio/text in → transcript → extraction → reiterate → durable
  memory. With on-device-first processing where possible and audio discarded after transcription.
- **Trust & consent primitives.** Explicit confirm for send/change, reiterate-before-store for
  capture, consent + recording indicator, plan preview, dry-run, undo, and memory deletion — all v1.
- **Permissions & privacy.** Per-tool consent (sensitive scopes: mic, messages, inboxes, calendar);
  clear statement of what's captured/retained and where it's processed; user-visible + deletable
  memory and action log. Reuse Keychain-based secret storage for OAuth tokens/keys.
- **Observability.** Structured logs of capture → extraction → intent → plan → tool result so we can
  measure success/undo rates and debug failures.

### 6.3 Why this is the right first build

- **It de-risks the thesis.** Everything else only matters if capture→memory→trusted-action works.
  Prove that loop on a narrow, real, daily use case.
- **It reuses the strongest existing assets.** turbometa already has a functioning gateway/tool
  pathway (OpenClaw), hands-free voice, realtime STT, and TTS; omi proves the capture+memory model.
- **It imports omi's best idea as outcomes.** Action items / follow-ups become *executed* actions.
- **It produces a demoable, lovable unit.** "It heard me promise Raj a quote on the call, reminded
  me, drafted the WhatsApp, and sent it when I said yes" is a complete, evaluable everyday story.

### 6.4 First-build success criteria

- **Capture quality**: conversations are transcribed accurately enough that extracted commitments
  are right most of the time.
- **Action success**: a high fraction of clearly-phrased follow-up/reminder/calendar requests
  complete correctly end-to-end without manual fixup.
- **Trust signals**: send/change confirmations feel fast; the reiterate-before-store step catches
  wrong assumptions; undo and memory-delete always work; **wrong-action rate ≈ 0** for send/change
  actions, and **no silent storage of a misunderstood fact**.
- **Felt relief**: target users report they offload remembering/following-up to Halo and *choose* to
  keep using it the next day.

### 6.5 Explicit non-goals of the first build

No call initiation, no autonomous/background action, no full memory-browser product, no
tools beyond the everyday belt, no third-party/developer tooling, no camera/vision, no desktop, no
streaming/nutrition/translation, no new hardware. If a task isn't on the capture→memory→action
spine for the everyday belt, it waits.

---

## 7. Key risks & open questions

- **Capture feasibility (the existential risk).** If we cannot lawfully and technically capture the
  conversations, the whole premise weakens. The realistic path likely runs through a **wearable mic
  or speakerphone capture**, not tapping the call stream — which is in tension with "phone-first."
  Run a capture feasibility spike *before* committing the rest of the build. (See §9.)
- **Legal/consent exposure.** Recording others is regulated and varies by jurisdiction. Needs a real
  consent model, a recording indicator, and possibly geo-gating — a product and legal dependency.
- **Trust paradox of an always-on recorder.** The conversations most worth capturing (sensitive,
  unpleasant, financial) are exactly the ones users are most anxious to have recorded and stored.
- **Reiteration fatigue.** At always-on scale, "reiterate before store" could become constant
  nagging. Open question: batch confirmations, set a salience threshold, default to silent-capture
  + periodic review.
- **Extraction accuracy → wrong follow-ups.** A misheard commitment that becomes a sent message is
  worse than a dropped ball. Confirm-before-send is the backstop; extraction quality is the lever.
- **Battery / cost of continuous transcription.** Always-on STT is power- and compute-hungry; needs
  on-device or efficient streaming and clear battery expectations.
- **Differentiation vs. omi.** omi already does ambient capture + memory + action items. What is
  genuinely new? (Candidate answer: the *everyday-action follow-through* on consumer channels —
  WhatsApp/SMS/calendar — for an ordinary, non-power user. We must make that the wedge.) See §9.

---

## 8. How we'll evaluate "did we build the right thing"

- A real target user can, over a normal day of calls and chats, let Halo capture their conversations
  and then complete several genuine follow-ups/reminders by voice/text — and reports it *reduced
  what they had to hold in their head*.
- The memory + action log shows accurate capture, high action success, and near-zero wrong-actions
  on send/change, with undo/delete available.
- We can add another tool quickly because the tool-call contract held up — evidence the foundation
  is sound (and the cue that we're ready to discuss *what comes next* — the roadmap, deliberately
  out of scope here).

---

## 9. First-principles critique of this vision and plan

A product leader's job includes attacking their own plan. Here is where this vision is weakest, on
first principles — and what I'd do about it.

### 9.1 The central premise may not be buildable on a phone
"Always-on recorder of phone/WhatsApp calls" assumes we can access call audio. On first principles
we cannot, on either major mobile OS, from a third-party app. So the headline capability is, today,
**only realistically achievable via a body-worn/ambient mic or speakerphone** — which quietly
contradicts the "phone-first, wearable-second" positioning. **Implication:** either (a) the true
first product is a *wearable* capture device + phone app (the omi model), or (b) the phone-first MVP
must capture something narrower (ambient/in-room audio, the user's dictated summaries, meeting-mode
on speaker) and be honest that it does **not** record the other party on a normal handset call.
**Recommendation:** run a 1–2 week capture feasibility spike first; let the result, not the slogan,
decide the surface. Do not build the action layer on top of a capture assumption we haven't proven.

### 9.2 "Always-on capture" and "privacy/trust as a feature" are in direct tension
We simultaneously claim trust as a precondition *and* propose continuously recording conversations —
including with other people who never opted in. This isn't fatal, but it's the product's hardest
contradiction. The most valuable data (unpleasant, financial, personal) is the most dangerous to
hold. **Recommendation:** make capture *narrow, consented, and ephemeral by default*
(transcribe-then-discard-audio, on-device where possible, explicit recording indicator, aggressive
retention limits), and treat "what we *don't* keep" as a headline feature, not fine print.

### 9.3 We may be rebuilding omi
omi already ships ambient capture + memory + action items + integrations, with 300k+ users and a
hardware line. On first principles, "omi + an action layer" is not automatically a new product — it
may just be omi. **The defensible wedge here is narrower and clearer than the doc currently states:**
*everyday-action follow-through for an ordinary, non-power-user, on the consumer channels they
actually use (WhatsApp, SMS, calendar, reminders).* omi indexes knowledge for professionals; Halo
*closes loops* for normal people. **Recommendation:** sharpen the wedge to "the assistant that
actually sends the follow-up," and consider building *on* omi's open-source capture rather than
re-implementing it.

### 9.4 "Action is the product" — but the first build is mostly capture + memory
There's a mismatch between the slogan and the work. The riskiest, most expensive parts of the first
build are capture and memory; the action layer (given OpenClaw already exists) may be the *easy*
part. We should be honest that v1's hard problems are transcription quality, extraction accuracy,
and trust — not tool-calling. **Recommendation:** name capture+memory quality as the real v1
bottleneck and resource it accordingly; don't let "action is the product" hide where the risk lives.

### 9.5 The cognitive-load claim can invert
The promise is "free up brain capacity." But an always-on recorder that reiterates before storing,
and asks to confirm every send, can *add* a review burden — a stream of "is this right? confirm?"
that is its own cognitive load. **Recommendation:** design for *batched, low-frequency* touchpoints
(a short daily/post-call review, salience-thresholded prompts) rather than per-utterance
confirmation; measure "interruptions per day" as a first-class metric and keep it low.

### 9.6 Scope is still large for a "first build"
Always-on capture + persistent memory + 5 tool families + multi-surface is a lot of surface area for
a first iteration, even trimmed. **Recommendation:** pick **one** capture path, **one** killer loop
(e.g., *call/chat → reminder + WhatsApp follow-up*), and **one** surface, and refuse the rest until
that single loop is boringly reliable. Calendar/email read can be context inputs before they are
full read/write tools.

### 9.7 Monetization & willingness-to-bear-cost are unaddressed
The target user is "an ordinary individual," but always-on transcription has real per-user cost
(STT compute, storage) and asks them to wear/enable a recorder. Will an ordinary person pay for, and
consent to, that? **Recommendation:** validate willingness-to-pay and willingness-to-record with the
target segment early; the answer may reshape whether this is a consumer product, a prosumer tool, or
a wearable-anchored one.

### 9.8 What would change my mind that this is the right plan
- A capture spike shows a lawful, acceptable-quality, phone-feasible capture path (resolves §9.1).
- Target users, shown the consent/recording model, still say yes and would pay (resolves §9.2, §9.7).
- We can articulate the wedge vs. omi in one sentence a user feels (resolves §9.3).
If those three hold, the capture→memory→action thesis is worth building. If they don't, the honest
move is to pivot the surface (wearable-first) or the wedge (action-only, no ambient capture) before
writing a line of the action layer.
