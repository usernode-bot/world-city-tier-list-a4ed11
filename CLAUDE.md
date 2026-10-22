# World City Tier List — notes for Claude Code

This app runs on **Homeroom**. The platform conventions are the single
source of truth for platform-wide behavior (auth model, `USERNODE_ENV`,
public/private tables, "don't `git push`", etc.):

**Platform conventions (authoritative, always current):**
https://app.onhomeroom.com/claude.md

**Inside Homeroom** (its dev chat, and Homeroom bot's builds), do not
fetch that URL: what applies to your change is already in your
instructions, and the `homeroom` MCP server's
`get_platform_conventions` reads any one section of the conventions
when you need it. The two sections below are for working outside
Homeroom; skip them there.

**Anywhere else** (Claude Code run against this repo locally, or another
harness), fetch it at the start of each session. The hosted copy is
updated in place when platform rules change, so fetching it gives you
today's rules, not a stale snapshot.

## Connector permission prompts (outside Homeroom)

This repo ships `.claude/settings.json`, which allows the **read-only**
Homeroom connector calls (`mcp__homeroom__get_*`,
`…__list_*`, `…__whoami`) once you accept the workspace trust dialog.
See `.claude/README.md` for the whole story, including what to do if you
are still being prompted.

## Check that this checkout is current

You may be working in a fork of this app whose `main` is behind the app's
canonical repository, and nothing in the checkout says so: `git fetch origin`
compares the fork with itself. This matters before you **read** code to answer
a question about how the app behaves now, not only before you edit it.

The canonical repository is named in `.claude/homeroom-canonical-repo`. Check against
it, not against `origin`:

```sh
git fetch "$(cat .claude/homeroom-canonical-repo)" main
git merge-base --is-ancestor FETCH_HEAD HEAD && echo current || echo behind
```

`behind` means this checkout does not contain the canonical `main`. To answer
a question, read the canonical code instead (`git show FETCH_HEAD:<path>`,
`git grep <pattern> FETCH_HEAD`). To change code, start from the exact base
commit your Homeroom work order gives, and never merge or rebase onto the
canonical `main` yourself: which commit a change is diffed against decides
what the group votes on. With the Homeroom connector, `get_checkout_status`
answers the same question.

A session-start hook (`.claude/hooks/homeroom-freshness.sh`, see `.claude/README.md`) runs
this check for you and tells you when you are behind. It is silent offline, so
its silence is not proof the checkout is current. Inside Homeroom (its dev
chat, and Homeroom bot's builds) the platform fixes the base commit, and none
of this applies.

## Starter template: City tier list

This app was created from Homeroom's ready-made **City tier list**.
A tier list of the group's favorite cities: anyone adds one, everyone drags them into tiers, and the group's ranking shows where each lands. It works as it is, and it is a starting point, not a fixed
product: keep what the people building this app want and change the rest
freely.

Where things are:

- `api.js`: this app's routes and its tables (`tier_items` and `tier_votes`).
  `server.js` mounts it after the sign-in check and runs `migrate()`
  on boot, which also seeds a few obviously fake rows in a staging preview.
- `public/index.html` and `public/app.js`: the screen.
- `live-updates.js` and `public/live-updates.js`: live updates. Every
  write to `/api/*` that succeeds tells each open screen over
  `GET /api/changes` (an event stream saying only that something
  changed), and a screen that shows what other people do fetches again
  (`LiveUpdates.watch` in `public/app.js`). A route added to `api.js` is
  covered by itself; a change made any other way (a timer, a job) calls
  `require('./live-updates').notify()`.
- `dapp.json` `tests`: the checks every proposal runs. Keep them passing,
  and change them when you change what they look for.

Update `README.md` when what the app does changes.

`{{APP_NAME}}` in `public/index.html` is not a leftover: `server.js` fills
it in with the project's name (`USERNODE_APP_NAME`, which the platform
sets) each time it serves the page, so the name follows the project when it
is renamed. Keep it wherever the name shows.

Keep the `usernode-dev-console@1` forwarder `<script>` and the bridge
`<script>` when rewriting the HTML: both are platform infrastructure.
The screen is built from the design kit, which is not placeholder either:
keep building with it, and fill in "## Design" below with the first change
that gives this app a look of its own.

The screen has a light and a dark look and follows the viewer's Homeroom
theme, switching live when they change it: the theme `<script>` right after
the bridge tag sets a `dark` class on `<html>`. Keep that script, and give
everything you build both looks (the design kit's colour tokens carry both), unless one
fixed look is the point of this app, like a game's own scene; then say so
under "## Design" below. Unless a request asks for one, add
no theme picker: the viewer's Homeroom setting is the control. "The
platform's light/dark theme inside the app frame" in the platform
conventions has the details.

If a rule below this line conflicts with the hosted conventions, the
hosted conventions win. This file is **app-specific** — write down
things about *this* app that belong in the repo: product intent,
data-model quirks, style preferences, opt-in policies (e.g. which
tables you've marked private), etc.

---

## About World City Tier List

Every city we love, ranked by the group

_(add a sentence or two more of product context here so Claude Code has a
shared understanding of what this app is for)_

## Your people's data

`usernode-user-data.js` erases a person's rows when they delete their
Homeroom account, and exports them when they ask for a copy. By default it
covers every table with a `user_id` (or `usernode_pubkey`) column. When
you add a table that keys a person's rows another way (`author_id`), add it
to `OWNED_BY`; when a table's `user_id` is not the person the row is
about, add it to `SKIP`. Keep the file and its mount in `server.js`.

## Design

This app's look. The first real version fills in the blanks; every later
change follows it, and updates it when a request changes the look on purpose.

- **Palette:** _(name the accent, any second colour and the neutrals, e.g.
  "accent: tomato red; second: basil green; neutrals: warm greys")_
- **Signature element:** _(the one thing on screen drawn from this app's
  subject, which no other app would have)_
- **Type scale:** `text-title`, `text-heading`, `text-body`, `text-small`
  _(change their sizes in `tailwind.config.js` if you must, not their number)_
- **One fixed look:** _(only for an app drawn as its own scene, such as a
  game: which look, and why. Otherwise delete this line.)_

The kit is in `styles/tailwind-input.css`: colour tokens with a light and
a dark value (named in `tailwind.config.js`), and a few components
(`page-column`, `btn-primary`, `btn-secondary`, `field`, `list`, `list-row`,
`card`, `section-label`, `skeleton`, `state-empty`, `state-error`).
Re-theme by changing the token values there, keeping every text pair at
4.5:1 or more in both looks.

- Colour comes only from the tokens (`bg-ground`, `bg-surface`,
  `text-fg`, `text-muted`, `border-line`, `bg-accent` with
  `text-on-accent`, ...): never a raw hex value or a stock palette class.
- Tap targets are at least 44 px; the buttons and fields already are.
- A field's label says what it is; its placeholder, if any, is an example
  that says so ("e.g. 5.0"), never a bare value that could pass for one
  already entered.
- Each screen sits in `page-column`, one centred column on a wide window.
- Every screen that loads data has honest loading, empty and error states.
  Never show the empty state while loading or after a failure; an error says
  what failed, what still works, and offers Retry. Loading has the content's
  own shape (its rows, with `skeleton` bars for their text).
- Seed obviously fake staging demo data so the populated screen can be seen
  ("Staging mock data" in the platform conventions).
- No cards in cards, no uppercase eyebrows, no emoji as icons.
- A header that shows the app's own mark shows the project's icon there
  (the emoji in `dapp.json`'s `icon` block) or no mark at all: never an
  empty tile, and never `brand/icon.png`, which the app does not serve.

## App-specific conventions

_(optional — e.g. "all currency values stored as integer cents, not
floats"; "the `posts` table is append-only"; "avoid adding new
dependencies"; etc.)_
