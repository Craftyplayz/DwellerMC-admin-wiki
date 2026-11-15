# DwellerMC-admin-wiki

Admin wiki for DwellerMC (MkDocs Material), deployed by GitHub Actions on push to `main`. Public but unlisted: `overrides/main.html` sets noindex, and nothing players see may link here. The shared rules live in `../CLAUDE.md` on the owner's machine; this file carries the essentials for a session that has only this repo.

- Pages describe what the plugin code does now (repos DwellerCore, DwellerBiomes, DwellerStructures), with values from the repo default configs. If the live server differs, state the repo default and say so.
- One page per plugin, `docs/config-<plugin>.md`, explains every config key: default, effect, examples, interactions with other plugins, what breaks if set wrong. It changes in the same commit as the config.
- Third-party plugins get a page under `docs/plugins/`: what it does, install, every command and permission, every config key with default and effect, how it interacts with the DwellerMC plugins, common problems.
- New page: add to `nav:` in `mkdocs.yml`, link it from related pages.
- Style: a normal community wiki. No meta statements about accuracy or maintenance, no "unverified" markers (leave unsure facts out), no em dashes, no filler. Plugin pages end with a short "Links" section to official docs.
- Build before committing: `pip install -r requirements.txt`, then `mkdocs build --strict` (on the owner's Windows machine: `python3.14 -m mkdocs build --strict`). The deploy runs the same strict build, so a broken build means a broken site.
