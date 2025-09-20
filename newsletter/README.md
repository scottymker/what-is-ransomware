# Newsletter output

Generated drafts from `automation/generate_newsletter.py` land in this folder. The GitHub Actions workflow uploads the files as artifacts so they do not need to be committed—`newsletter/latest.md` and `newsletter/latest.json` are ignored by Git.

If you want to archive past sends inside the repository, create dated Markdown files here (for example `2025-q1.md`) and commit them manually.
