# touchline

**Trace a football's journey across a match as a living line.** Pass networks, a
Pollock-style pass painting, and the ball's continuous trajectory drawn in real
time — built from StatsBomb open data, or from a synthetic possession engine when
you just want to watch the shapes.

A football match leaves a trail. Every pass, carry, and shot is a stroke; over
ninety minutes they accumulate into something between a diagram and a drawing.
`touchline` renders that trail four ways and lets you watch it being made.

---

## What's inside

**Four views of a match**

- **Pass network** — players as nodes at their average position, sized by
  involvement, connected by lines weighted by how often the ball ran between them.
  The readable, connect-the-touches view.
- **Pollock** — every completed pass as its own tapered comet stroke, both teams
  overlaid. Density *is* the information; the midfield knot draws itself.
- **Trace** — every pass as a thin line, optionally animated so the painting
  fills in minute by minute.
- **Flow** *(interactive)* — one unbroken line follows the ball as it moves:
  passes curve, carries wander, shots streak gold at goal, restarts hop. Speed
  slider (0.05×–6×), scrubber, and toggles to hide the pitch or the ball marker
  so it reads as pure ink on paper.

**Interactive pages** (open in any browser; host on GitHub Pages to share)

- `interactive/flow-live.html` — **no install, no account.** Pick a competition →
  season → match from dropdowns and it fetches the StatsBomb open data in the
  browser and draws the real game. Works on a phone or iPad.
- `interactive/balls-journey.html` — the accumulating pass painting with a
  logarithmic speed control and a per-minute density strip.
- `interactive/flow-of-the-game.html` — the live ball trace, with **Load match**
  to render a real game from a JSON exported by the scripts.

---

## Can it render a real game?

Yes — **any match in StatsBomb's free open-data catalogue.** That covers all the
men's and women's World Cups, several domestic women's leagues, selected
Champions League finals and La Liga seasons, and more. It does **not** cover
arbitrary recent fixtures (e.g. last weekend's league games) — those sit behind
StatsBomb's paid feeds, not in the open set.

Don't memorise IDs — browse what's available:

```bash
# 1. list the free competitions
python scripts/statsbomb/render_match.py --list

# 2. list the matches in one of them (competition_id + season_id from step 1)
python scripts/statsbomb/render_match.py --list --competition 43 --season 106

# 3. render your pick — by team names or by match-id
python scripts/statsbomb/render_match.py --competition 43 --season 106 \
       --home Argentina --away France --view network
python scripts/statsbomb/render_match.py --match-id 3869685 --view pollock
python scripts/statsbomb/render_match.py --match-id 3869685 --view trace --gif
```

To render a real game **in the interactive Flow page**, export it first:

```bash
python scripts/statsbomb/export_match.py --match-id 3869685
# writes data/<home>-<away>.json
```

Then open `interactive/flow-of-the-game.html`, click **Load match**, and pick that
JSON file. The exporter pulls Pass + Carry + Shot events in time order, so the
ball's path is genuinely continuous rather than a string of disconnected passes.

---

## Quick start

```bash
git clone https://github.com/bluebflatminor/touchline.git
cd touchline
pip install -r requirements.txt          # only needed for the StatsBomb scripts
```

No-network demo (synthetic data, no installs beyond matplotlib/numpy/pillow):

```bash
python scripts/synthetic/pass_network.py   # -> passnet.png
python scripts/synthetic/pollock.py        # -> pollock.png
python scripts/synthetic/animate.py        # -> match_paint.gif
```

The interactive pages need nothing installed — just open the `.html` files.

---

## Layout

```
touchline/
├── interactive/
│   ├── flow-live.html           # pick a match from dropdowns, fetches data in-browser
│   ├── balls-journey.html       # accumulating pass painting + speed/density
│   └── flow-of-the-game.html    # live ball trace + Load match (from JSON)
├── scripts/
│   ├── synthetic/               # offline demos, no network needed
│   │   ├── pass_network.py
│   │   ├── pollock.py
│   │   └── animate.py
│   └── statsbomb/               # real matches from open data
│       ├── render_match.py      # browse + render (network / pollock / trace)
│       └── export_match.py      # dump a match to JSON for the Flow page
├── data/                        # exported match JSON lands here
├── requirements.txt
└── README.md
```

---

## Notes & honesty

- **Synthetic vs real is always labelled.** The synthetic scripts and the default
  state of the interactive pages use an invented possession engine — realistic in
  shape, not real data. Anything drawn from a StatsBomb match is real.
- The coordinate frame is StatsBomb's 120 × 80 pitch throughout.
- Built collaboratively with an LLM; the data plumbing and rendering are
  conventional and inspectable — read the scripts, they're short.

## Credits & licence

- Match data: **StatsBomb Open Data**, used under their free terms — please keep
  the StatsBomb attribution if you publish renders. See
  <https://github.com/statsbomb/open-data>.
- Pitch drawing in the Python scripts: **mplsoccer**.
- Code in this repo: choose a licence before publishing (MIT or CC0 both fit a
  solo open project); add a `LICENSE` file to match.
