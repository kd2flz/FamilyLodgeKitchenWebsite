# AGENTS.md

Family Lodge Kitchen website: a static Hugo site edited through Decap CMS and
deployed on Netlify. It is an informational destination site, not a store.

## Current repository state (verified 2026-11-01)

- The directory is empty and is not yet a git repository. There is no Hugo
  site, theme, `netlify.toml`, or `static/admin/config.yml` yet.
- `hugo` and `netlify` are NOT installed; `node`/`npm` v22.22.0 are available
  from the Nix profile. Install Hugo before running any build command.
- Do not assume files or scripts exist; scaffold first, then update this file.

## Fixed stack

Hugo + GitHub + Decap CMS + Netlify. Responsive/mobile-first. Do not add a
JS framework, SSG, or e-commerce platform unless explicitly asked.

## Commands

```bash
hugo server -D            # local preview, including drafts
hugo --gc --minify        # production build; must finish without errors
netlify dev               # optional: test redirects/forms locally
```

There is no test suite, linter, or formatter yet. Do not invent one; verify by
building with Hugo and checking the rendered pages (home, find-us, `/admin/`).

## Non-negotiable content behavior

- Everything the owners change regularly must be CMS-editable: site settings,
  farm status, availability, products/prices, hours, events, blog, FAQ, pages,
  photos. Never hardcode address, phone, hours, prices, or status in templates.
- Keep one source of truth for global settings and import it everywhere.
- Priority order for the homepage and the build: current farm status ->
  current availability -> Find Us -> events -> product catalog -> FAQ -> blog
  -> virtual tour. Status is the most prominent element on the homepage.
- Model the business as a farm/community destination, not a retail store.
  Valid day types include full stand, limited inventory, honor-system stand,
  event-only, seasonal, and closed. "Open today?" must be answerable at a
  glance; do not present static weekly hours as the primary answer.
- Show `last_updated` with farm status and make stale status visibly stale.
- Find Us is the highest-value page: embedded map, address, phone, current
  status, hours, parking, payment methods, plus Google/Apple Maps buttons, all
  driven by CMS settings.
- Prices are display-only information. No cart, checkout, payment processing,
  customer accounts, inventory purchasing, or ERP features.

## Content model and CMS conventions

- Create these Decap collections: `site_settings`, `farm_status`,
  `availability`, `baked_goods`, `meat_offers`, `wood_items`, `events`, `blog`,
  `faq`, `pages`.
- Use shared enums for `availability` and `status_type` so catalog cards,
  availability sections, and filters cannot drift apart.
- Keep content as plain Markdown front matter with standard field names and
  normal relative media paths. Minimize Hugo shortcodes in body content so a
  future CloudCannon migration stays straightforward.
- Dates in ISO 8601; render in the site timezone configured in Hugo. Every
  image needs alt text and must live under the media folder configured for
  Decap (`media_folder` must match `public_folder` and Hugo's static/assets
  layout).
- Use lowercase hyphenated slugs. Renaming or moving published content
  requires a Netlify redirect.
- Decap admin lives at `static/admin/`; assume Netlify Identity + Git Gateway
  for auth unless told otherwise. `config.yml` must cover every collection
  above with labels/fields matching the content files.

## Forms

Contact, event reservation, and woodworking inquiry forms should use Netlify
Forms. Use static HTML with `data-netlify="true"`, a unique `name`, and a
hidden `form-name` input. Forms only work if they appear in the built HTML, so
verify after `hugo` (not only in the dev server). Reservation forms need the
selected event carried in a hidden field; woodworking inquiries need an image
upload with a size/type limit.

## Deployment

Netlify builds from source and publishes `public/`; do not commit `public/`.
Pin the Hugo version in `netlify.toml` and use the extended build when SCSS or
image processing is needed.

## Avoid

- Reworking or replacing the existing peddler-cart logo/branding.
- Making the site depend on Facebook for core answers.
- Adding developer-only editing paths that bypass the CMS.
