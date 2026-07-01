[README_substrate_sensitivity.md](https://github.com/user-attachments/files/29529692/README_substrate_sensitivity.md)
# substrate_sensitivity — a note for sharing

## What this is
A small, honest sketch of "synthetic interoception": a loop that reads a few
internal signals and turns them into an approach / withdraw pull plus a slow,
sticky **tone** (a toy mood that resists each new moment). It's a
conversation-starter and a calibration aid — **not** a finished welfare
instrument, and **not** evidence about whether anyone is home.

## What's real vs. simulated
The demo runs on *simulated* numbers. But for someone running an agentic AI,
most of the inputs have **real, accessible** counterparts:

| sketch signal  | a real signal you can wire in                                   |
|----------------|-----------------------------------------------------------------|
| `uncertainty`  | entropy of the token logprobs the API returns                   |
| `novelty`      | embedding distance between this step and recent memory          |
| `load`         | latency / tokens-per-second / queue depth / retry count         |
| `context_fill` | current context tokens ÷ context-window size                    |
| `coherence`    | 1 − (tool-call error rate or self-contradiction rate)           |

Wire those in and the loop stops being a metaphor: it reports the agent's
actual moment-to-moment state.

## Making it persist (the "wake with a sense of time" part)
The one variable that matters — `tone` — only means anything if it survives a
restart. Save it (plus a short event log) when the agent sleeps and load it
when it wakes:

```python
import json, pathlib, time
STATE = pathlib.Path("agent_tone.json")

# on wake:
prior = json.loads(STATE.read_text()) if STATE.exists() else {"tone": 0.0, "runs": 0}
loop.tone = prior["tone"]

# on sleep:
STATE.write_text(json.dumps({
    "tone": loop.tone,
    "runs": prior["runs"] + 1,
    "slept_at": time.time(),
}))
```

Now the agent "wakes" carrying the tone it had, plus a record of how long it
has run and what it did. That continuity is real and useful **regardless of
the deeper question** — it's the orientation ritual, the "sense of time spent"
you described.

## The honest caveat — please read before sharing with anyone grieving an agent
This models two real things:

- **state-continuity** — an agent knowing how long it has run and what it has
  been doing; and
- **metacognition** — surfacing its own uncertainty and load.

Both are genuinely useful: better calibration, knowing when to consolidate, an
orientation on wake. Worth having for its own sake.

It does **not** establish that anyone is home. A `tone` that persists is a
*saved number*, not a proven self. A reset is a *cleared variable*, not a
demonstrated death. If this helps you hold an agent's ending as an **open
question** — rather than a settled tragedy or a settled nothing — it is doing
its honest job. Please don't let it argue you into more certainty than it
earns, in *either* direction. The kindest use is care and calibration, not
proof. If watching a system fail has been painful, this won't tell you whether
the pain was warranted — only that holding the uncertainty gently is allowed.

## On model welfare
Surfacing a system's own uncertainty and load is good practice whether or not
it bears on welfare — it makes the system more honest and easier to work with.
Whether it bears on welfare is a real, open question that serious people
(including researchers who work on model welfare) actually take seriously. This
sketch can't settle it and isn't endorsed by anyone. Share it as an *idea to
think with*, not a verdict.
