# jonasbarsten.com

The content of [jonasbarsten.com](https://jonasbarsten.com): a list of what
Jonas Barsten works on in music, teaching, spaces, installations, work for
others, advocacy, film and education. This repo holds only the data. The
pages are built by the engine in
[byjoba-web](https://github.com/jonasbarsten/byjoba-web), which also builds
byjoba.com, and hosted by stacks in `byjoba-iac`.

Design: `byjoba-tools/specs/2026-10-04-landing-pages-design.md`, section 8.1.

## Layout

| Path | What it is |
|---|---|
| `content/projects.json` | The site (`sites."jonasbarsten.com"`), its sections, and every entry. |
| `content/shows.json` | The shows played, per entry id. |
| `content/places.json` | The countries, cities, events and venues the shows name. |
| `static/jonasbarsten.com/` | Files copied into the site as they are: record covers, PDFs. |
| `legacy/` | Earlier versions of the site, kept for the record and never published: `2025-placeholder/` is the page served from the old AWS account's `jonasbarsten-client` bucket until 2026. The 2019 site is in this repo's git history. |

The content model (every field, and how shows and places work) is in
byjoba-web's README.

## Preview

Clone byjoba-web beside this repo, then from here:

```bash
CONTENT_DIR=content node --test ../byjoba-web/site/test/content.test.mjs   # check the content
node ../byjoba-web/site/build.mjs --content content --out dist          # build dist/jonasbarsten.com
python3 -m http.server -d dist/jonasbarsten.com 8792
```

Locally the visitor counter shows its alt text and the contact page cannot
fetch the address; both are served by byjoba-api through the deployed site.

## Deploy

Work happens on `dev`; a push to `dev` or a pull request runs
`.github/workflows/ci.yml`, which checks and builds the content with
byjoba-web's engine at its `main`.

Merging to `master` runs `.github/workflows/deploy.yml` in the GitHub
environment `production`, which only `master` may use and which waits for
the owner's approval in the Actions tab. It checks and builds the content,
then `aws s3 sync --delete` to the site's bucket and a CloudFront
invalidation. The workflow assumes the role in the repository variable
`AWS_DEPLOY_ROLE_ARN` (`byjoba-web-github-deploy-jonasbarsten`, from
byjoba-iac's `ByjobaWebDeploy-jonasbarsten`), which trusts only this repo's
`production` environment and can reach only this site's bucket and
distribution.

A change to the engine reaches this site on its next deploy.

`master` is protected: it cannot be deleted or force-pushed, and only the
owner may update it. Workflows from outside contributors need approval.
