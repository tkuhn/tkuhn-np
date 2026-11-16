# tkuhn-np

A second personal website for Tobias Kuhn, built entirely from the
[nanopublication network](https://nanopub.net/) with
[nanopub-hugo](https://github.com/Nanopublication/nanopub-hugo).

**Status: work in progress.** The live site remains at
[www.tkuhn.org](https://www.tkuhn.org/) (repo: `tkuhn/tkuhn.github.com`).
This one is staged at <https://www.tkuhn.org/tkuhn-np/> until it is ready to
take over the domain. (A GitHub Pages *project* site inherits the custom domain
of the user site, so it appears under www.tkuhn.org rather than github.io.)

## How it works

There are no content files for the sections. Every page under `/publications/`,
`/talks/`, `/posts/` and `/activity/` is generated from a query against the
nanopublication network, declared in `hugo.toml`:

| Section | Mode | View nanopublication |
|---|---|---|
| Publications | static | `papers-for-author-view` → `get-papers-for-author` |
| Talks | static | `presentations-view` → `get-presentations-by-speaker` |
| Posts | static | `user-posts-view` → `get-posts-by-user` |
| Activity | live | `latest-nanopubs-by-user-view` → `get-latest-nanopubs-by-user` |

The name, avatar, nanopub count and signing keys in the header are read from the
network too, via the module's `nanopub/profile.html` partial. Nothing about the
author is hand-maintained here except the links and the one-line highlights.

**Publishing a nanopublication is how you add content to this site.** The
nightly workflow rebuilds and it appears — no commit needed.

### What runs in the browser

Two things are fetched on every visit rather than at build time, both by
[nanopub-elements](https://github.com/Nanopublication/nanopub-elements) (the
version is pinned by `elementsVersion` in `hugo.toml`):

- **The Activity list** — `layouts/_partials/live-list.html`. It names no query
  endpoint, so the component moves on to the next known instance when one is
  down. Set `liveEndpoint` under `[params.nanopub]` to pin it to one instead.
- **The abstract and "See also" links on talk pages** —
  `layouts/_partials/live-details.html`. They are read from each talk's own
  nanopublication, because the talks query does not return them. A section opts
  in with `details = 'live'`; the partial explains where that works.

## Local development

Requires [Hugo extended](https://gohugo.io/) ≥ 0.146 and a Go toolchain (the
theme is a Hugo module).

```bash
hugo server    # http://localhost:1313/tkuhn-np/
hugo --gc      # one-off build into ./public
```

To move the theme forward:

```bash
hugo mod get -u github.com/Nanopublication/nanopub-hugo
```

## Deployment

`.github/workflows/deploy.yml` builds and deploys to GitHub Pages on push to
`main`, nightly at 04:23 UTC, and on manual dispatch. The nightly run is what
picks up newly published nanopubs; `[caches.getresource] maxAge = '24h'` in
`hugo.toml` is what makes it actually re-query rather than serve a cached copy.

## Cutover plan

When this replaces the current site: move the `CNAME` file to this repo, change
`baseURL` to `https://www.tkuhn.org/`, and carry over the static assets that
published papers link to (`/pub/*.pdf`, `/talk/*.pdf`, `cv.pdf`) so those URLs
do not break.

## Licence

Layouts and stylesheet are derived from the `exampleSite` of nanopub-hugo,
MIT-licensed — see `LICENSE.nanopub-hugo`.
