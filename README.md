# jonasbarsten.com

The content of [jonasbarsten.com](https://jonasbarsten.com): a list of what
Jonas Barsten works on in music, teaching, spaces, installations, work for
others, advocacy, film and education. This repo holds only the data. The
pages are built by the engine in
[byjoba-web](https://github.com/jonasbarsten/byjoba-web), which also builds
byjoba.com, and hosted by stacks in `byjoba-iac`.

Design: `byjoba-tools/specs/2026-10-04-landing-pages-design.md`, section 8.1.

## How it fits together

Four other repos take part, all under `jonasbarsten` on GitHub:

| Repo | What it does for this site |
|---|---|
| [byjoba-web](https://github.com/jonasbarsten/byjoba-web) (public) | The engine: a dependency-free Node generator that turns this repo's `content/` and `static/` into the pages, plus the stylesheet, the contact page script and the content tests. It also holds byjoba.com's own content. This repo's workflows check it out at `main`. |
| byjoba-iac (private) | The hosting, in AWS account `209479295726` (profile `byjoba`), eu-west-1: the `jonasbarsten.com` hosted zone (`ByjobaZone-jonasbarsten`), the site's private bucket and CloudFront distribution (`ByjobaWeb-jonasbarsten`), and the role this repo's deploy assumes (`ByjobaWebDeploy-jonasbarsten`). The domain, its us-east-1 certificate and the alias records are attached by `WEB_SITES` in its `lib/config.ts`. |
| byjoba-api (private) | The visitor counter (`/counter.svg`) and the contact address (`/contact`). The distribution sends both paths to `api.byjoba.com/web/v1` with an `x-site: jonasbarsten.com` header, so the site has its own count. The address and the Cloudflare Turnstile secret are SSM parameters, never in a repo. |
| byjoba-tools (private) | The design spec, the plans and `DOMAINS.md`. |

Requests to `https://jonasbarsten.com/` go to the CloudFront distribution:
the pages come from the bucket, and `/counter.svg` and `/contact` go on to
byjoba-api. DNS is the Route 53 zone in the byjoba account; the domain is
registered at Domeneshop, whose name servers point there. `jonasbarsten.no`
stays at Domeneshop and redirects here with Domeneshop's web forwarding.

So: content changes happen here, design and markup changes in byjoba-web,
and anything about hosting, the domain or the API in byjoba-iac or
byjoba-api.

## Layout

| Path | What it is |
|---|---|
| `content/projects.json` | The site (`sites."jonasbarsten.com"`), its sections, and every entry. |
| `content/shows.json` | The shows played, per entry id. |
| `content/places.json` | The countries, cities, events and venues the shows name. |
| `static/jonasbarsten.com/` | Files copied into the site as they are: record covers, PDFs, and `share.png`, the 1200×630 link-preview image the site's `shareImage` names. |
| `legacy/` | Earlier versions of the site, kept for the record and never published: `2025-placeholder/` is the page served from the old AWS account's `jonasbarsten-client` bucket until 2026. The 2019 site is in this repo's git history. |

The content model (every field, and how shows and places work) is in
byjoba-web's README.

## Preview

This repo lives at `~/Development/jonasbarsten.com`, with byjoba-web at
`~/Development/byjoba/byjoba-web`. From this repo:

```bash
CONTENT_DIR=content node --test ../byjoba/byjoba-web/site/test/content.test.mjs   # check the content
node ../byjoba/byjoba-web/site/build.mjs --content content --out dist          # build dist/jonasbarsten.com
python3 -m http.server -d dist/jonasbarsten.com 8792                           # http://localhost:8792/
```

After a content edit, run the build again and reload. Pull byjoba-web first
to preview with the engine the deploy will use (its `main`).

Locally the visitor counter shows its alt text and the contact page cannot
fetch the address; both are served by byjoba-api through the deployed site.

## Deploy

Work happens on `dev`; a push to `dev` or a pull request runs
`.github/workflows/ci.yml`, which checks and builds the content with
byjoba-web's engine at its `main`.

Merging to `main` runs `.github/workflows/deploy.yml` in the GitHub
environment `production`, which only `main` may use. Only the owner may
update `main`, so the merge is the approval; the deploy needs no further
step. It checks and builds the content,
then `aws s3 sync --delete` to the site's bucket, a CloudFront
invalidation, and an IndexNow submission of the indexed pages with the
site's `indexNowKey` (a refused submission is a warning only). The workflow assumes the role in the repository variable
`AWS_DEPLOY_ROLE_ARN` (`byjoba-web-github-deploy-jonasbarsten`, from
byjoba-iac's `ByjobaWebDeploy-jonasbarsten`), which trusts only this repo's
`production` environment and can reach only this site's bucket and
distribution.

A change to the engine reaches this site on its next deploy.

Editing from the phone: a Claude session on the repo (Claude Code on the
web or in the Claude app) follows `CLAUDE.md`: it edits on `dev`, waits for
CI, and for a content change merges `dev` into `main`, which deploys.

`main` is protected: it cannot be deleted or force-pushed, and only the
owner may update it. Workflows from outside contributors need approval.
