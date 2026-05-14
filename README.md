# Inkwell Campaign Marketplace

Community-authored campaigns for [Inkwell](https://github.com/LightWraith8268/inkwell) — a solo D&D 5e companion app powered by your own AI API key. Every campaign in this repo is playable directly from the in-app marketplace.

---

## How it works

The app fetches `manifest.json` from this repo to populate the marketplace listing. When a user taps **Add**, it downloads the corresponding `campaigns/<slug>.json` and stores it locally. The campaign's `world`, `openingScene`, and `rulesNotes` fields are injected into the AI DM's system prompt at the start of every turn, giving it the context it needs to run a coherent adventure.

---

## Campaign JSON schema

Every campaign is a single JSON file at `campaigns/<slug>.json`.

```json
{
  "id": "<uuid-v4>",
  "title": "Human-readable title shown in the app",
  "summary": "One or two sentence hook shown in the marketplace listing.",
  "world": "...",
  "openingScene": "...",
  "rulesNotes": "...",
  "tags": ["tag1", "tag2"]
}
```

### Field reference

| Field | Required | Purpose |
|---|---|---|
| `id` | Yes | UUID v4. Must be unique across all campaigns. Generate at [uuidgenerator.net](https://www.uuidgenerator.net/) or with `uuidgen` in a terminal. |
| `title` | Yes | Displayed in the marketplace and in the app's title bar during play. |
| `summary` | No | Short pitch shown in the marketplace card. 1–2 sentences. |
| `world` | No | **Injected into the DM system prompt every single turn.** Describe the setting, geography, tone, history, major threats, and factions. 2–4 paragraphs is ideal — long enough to be useful, short enough not to waste context. |
| `openingScene` | No | **Injected as the initial scene context.** Describes the situation at the moment of play. 1–2 paragraphs, second-person present tense. The AI DM reads this before the player types their first action. |
| `rulesNotes` | No | **Injected as DM guidance every turn.** Use this for rules modifications, encounter-balance notes, special mechanics (e.g. resource tracking, environmental hazards), tone guidance, and NPC details. Anything you'd write in a DM prep doc goes here. |
| `tags` | No | Short lowercase strings for filtering. Common tags: `dungeon`, `mystery`, `survival`, `roleplay`, `horror`, `exploration`, `classic`, `social`, `wilderness`, `coastal`, `undead`, `post-apocalyptic`. |

---

## Writing guide

### What the AI DM knows each turn

The DM receives:
- The campaign `title`, `world`, `openingScene`, and `rulesNotes`
- A live JSON snapshot of the character (name, race, class, level, HP, AC, ability modifiers, inventory, conditions, spells)
- The full conversation history
- The current scene label and any scene flags

It does **not** know anything else unless you put it in `world` or `rulesNotes`.

### Roll protocol

The DM emits `[ROLL: XdY+Z label DC n]` tags when a check is needed. The app rolls the dice and feeds the result back. **Do not ask the DM to simulate rolls** — the protocol handles it automatically. You can reference stat blocks and DC values freely in `rulesNotes`.

### State mutation protocol

The DM emits `[STATE: ...]` tags to mutate character state:

```
[STATE: hp -7]           subtract HP
[STATE: hp +5]           restore HP
[STATE: hp set 20]       set HP to value
[STATE: inventory +rope] add item
[STATE: inventory -rope] remove item
[STATE: condition +poisoned]   add condition
[STATE: condition -poisoned]   remove condition
```

You can reference these in `rulesNotes` to tell the DM when to apply them.

### Tips for good AI DM campaigns

1. **Be specific in `world`.** "A dark forest" is weak. "The Ashwood is a centuries-old forest of black-barked trees, perpetually overcast, known for will-o'-wisps that lure travelers off the path and for the Thornblight — a magical rot that kills plants from the inside out" gives the AI something to work with.

2. **Name your key NPCs in `rulesNotes`.** Even one line per NPC (name, role, personality, secret) dramatically improves consistency.

3. **Tell the DM the tone.** "Tone: gothic horror, slow dread, no comic relief" or "Tone: swashbuckling adventure, fast-paced, witty banter" shapes every response.

4. **Define the win condition.** Without one, the DM won't know when to build toward a climax. Put it in `rulesNotes`: "The campaign ends when the player destroys the phylactery or is killed."

5. **Document special mechanics.** If your campaign has resource tracking, environmental hazards, or non-standard rules, spell them out in `rulesNotes` with exact DCs and effects.

6. **Use `openingScene` to set stakes immediately.** The first DM response is shaped by what the player types, but `openingScene` primes the AI with the in-media-res situation. Drop the player into the action.

7. **Recommended level range belongs in `rulesNotes`.** E.g. "Recommended character level: 3–5."

---

## Campaign template

Copy `campaign-template.json` as a starting point.

---

## Contributing

1. Fork this repository.
2. Copy `campaign-template.json` to `campaigns/<your-slug>.json`.
3. Fill in all fields. Generate a fresh UUID for `id`.
4. Add your campaign to the top-level `manifest.json` array.
5. Open a pull request. Include a brief description of the adventure and recommended character level in the PR body.

**Guidelines:**
- Original content only. Do not copy text from published adventures or settings.
- D&D 5e SRD mechanics (monster stat blocks from the SRD, spell descriptions, rules text) are freely usable under CC BY 4.0.
- Keep `world` and `rulesNotes` focused and actionable — the DM reads these every turn.
- All content must be appropriate for a general audience unless explicitly tagged `mature`.

---

## Importing campaigns directly

You do not need to go through the marketplace. In the Inkwell app, on the Marketplace screen:

- **Import from URL** — Paste the raw URL of any Inkwell-format campaign JSON (e.g. a raw GitHub URL from a fork or your own repo). The app downloads and saves it locally.
- **Import from File** — Pick a `.json` file from your device. Useful for testing campaigns you're writing locally before submitting a PR.
