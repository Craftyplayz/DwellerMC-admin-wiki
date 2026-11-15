# DwellerMC Admin Wiki

Admin documentation for the DwellerMC plugins: install, config reference, troubleshooting.

Unlisted site: https://craftyplayz.github.io/DwellerMC-admin-wiki/ (public but not linked anywhere and marked noindex for search engines; anyone with the URL can read it). Players use the player wiki instead.

- Pages: Markdown files in `docs/`. Add each new page to `nav:` in `mkdocs.yml`.
- Publish: push to `main`. The GitHub Action builds and deploys in about 1 minute.
- Local preview: `pip install -r requirements.txt`, then `mkdocs serve`, then open http://127.0.0.1:8000.

Every fact here comes from the plugin code and default configs. When a plugin changes, the matching page is updated in the same session.
