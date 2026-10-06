# jonasbarsten.com

The content of jonasbarsten.com. The engine that builds it is
`jonasbarsten/byjoba-web` (see its README for the content model); this repo
holds data only. Read `README.md` for the layout, preview and deploy.

## Changing content

- Add, change or remove an entry by editing `content/projects.json`; shows go in `content/shows.json`, and every event, venue, city and country they name in `content/places.json`.
- After every change, from this repo (`~/Development/jonasbarsten.com`; byjoba-web is at `~/Development/byjoba/byjoba-web`): `CONTENT_DIR=content node --test ../byjoba/byjoba-web/site/test/content.test.mjs` and `node ../byjoba/byjoba-web/site/build.mjs --content content --out dist`. The build refuses invalid content.
- To change where entries appear, edit the section's `order` in `sites."jonasbarsten.com".sections` (a list of ids shown first, in that order); don't move entries in the file. Entries it doesn't name follow in file order. Within Music, current engagements come before past ones.
- Only shows Jonas played. A calendar entry is not proof of that; check before adding.
- An entry that belongs on byjoba.com (software and hardware Jonas makes on his own initiative) goes in byjoba-web's content instead. Nothing is listed on both sites.

## Copy rules

The pages are purely informational: no voice, no pitch, no personality.

- An entry says what the thing is, what Jonas's part in it was, and when. Nothing else.
- The `summary` is one short phrase for the card face, e.g. "Drummer" or "Musical director". Concerts, venues and other details go in `about`. The build rejects a summary with a second sentence.
- In Music, the summary is only Jonas's role: "Drummer", "Musical director", "Drums and electronics", "Composer". What the act or record is, who it was with, and where it played all go in `about`; never drop them when shortening. Other sections may describe the thing itself.
- Several roles always come in this order: composer, producer, musical director, drums/drummer, percussion, keyboards, then the rest (samples, electronics, live effects, programming, tracks, technical setup, sound recording). E.g. "Composer, producer and drummer", "Drums, percussion and electronics".
- No praise, ranking or scale words (famous, biggest, leading, popular, global).
- No audience figures, revenue or user counts.
- Facts that locate the work are fine in `about`: a tour, a festival, a venue, a collaborator, a year.
- No calls to action. Label links by what they are: site, source, profile, pdf.
- A fact found online is used only when the source is the artist, the venue, the label or an institution.

## Disclosure

This repo is public.

- The entry named "1:1 (working title)" is a very early start-up. Keep it neutral: never add its purpose, partners' plans, market or domain.
- Huba is co-owned and proprietary: one neutral line, no links.
- No email address, postal address or phone number goes in any page or file here. The contact address lives in an SSM parameter.
- Only dates, venues, cities, roles, act names and subjects come from email, calendars or files: no addresses, phone numbers, private persons' contact details, fees or quoted mail.

## Rules

- Nothing in the Music section has a `url`: videos and tracks go in `media`, other pages in `links`.
- Work on `dev`. Never push to `main`; the owner merges and approves the deploy.
- Keep the README up to date.
