# World City Tier List

> **Ready-made.** This repo was scaffolded by Homeroom as its ready-made
> **City tier list**. It works as it is; make it your own.

A tier list of the group's favorite cities: anyone adds one, everyone drags them into tiers, and the group's ranking shows where each lands.

What it already does:

- **Add cities**: anyone in the project adds one; a name already on the list is not added twice.
- **Your ranking**: drag each one into S, A, B, C, D or F. Change your mind any time.
- **The group's ranking**: the same board, with every item in the tier its average lands in (a tie goes up). One tap switches between the two.
- **Want to go**: a row below F for cities you have not got to yet. It counts toward no tier, and the group's board shows how many people want to go, for each one.
- **Who ranked it what**: tap any of the cities, on either board, to see the group's tier and the tier each person gave it, live as people rank.

And what every Homeroom app gets:

- **Sign-in**: the server verifies the platform-issued user token (an
  RS256 JWT) on every request, so the app already knows who is using it.
- **Database**: the app has its own Postgres database. Its tables are
  created on boot by `api.js`.
- **Styling**: Tailwind CSS, precompiled by `npm run build` during image
  creation, following the platform's light or dark theme.

## Changing it

To change this app, ask Homeroom bot: open the app on Homeroom, tap the
Homeroom icon in the header, then **Suggest an improvement**, and describe
what you want in plain English. You can also run Claude Code against this repo directly; start with
`CLAUDE.md`, which carries the app-specific notes and points at the
platform rules.
