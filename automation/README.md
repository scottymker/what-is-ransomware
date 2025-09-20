# Quarterly newsletter automation

This folder contains a lightweight workflow for generating quarterly "Stay informed" briefings without hand-writing every email. It focuses on three ideas:

1. **Curated content library.** `newsletter_content.json` stores drills, threat intel notes, habit refreshers, and shareable resources you can reuse.
2. **Deterministic generator.** `generate_newsletter.py` rotates through the library so each quarter produces a complete, ready-to-send draft you can tweak and approve.
3. **Scheduled hand-off.** A GitHub Actions workflow (defined in `.github/workflows/quarterly-newsletter.yml`) can run on a timer, drop the latest draft in an issue for review, and upload the Markdown file as an artifact.

## 1. Generate a draft locally

```bash
python automation/generate_newsletter.py \
  --output newsletter/latest.md \
  --metadata-output newsletter/latest.json
```

- `latest.md` becomes the body you can paste into your email provider.
- `latest.json` captures the quarter label, subject line, and which library items were used.
- Use `--date 2024-07-01` to preview a different quarter or `--seed-offset 1` to shift the rotation without editing the data file.

## 2. Review, tweak, and send

1. Open `newsletter/latest.md` in any Markdown viewer.
2. Apply edits if you want to localize tone or link to organisation-specific assets.
3. Paste the content into your email service (Buttondown, Mailchimp Free, Brevo, etc.).
   - Subject and preheader are listed at the top of the Markdown file for easy copying.
   - Most tools accept Markdown or HTML—switch to "HTML" view if the preview looks off.
4. Schedule or send the campaign to your subscriber list.

## 3. Keep the library fresh

The generator rotates through entries in `newsletter_content.json`. Update the text or add new entries whenever you publish fresh drills or resources. The script never deletes old items, so your history stays intact.

Recommended workflow:

- Add a new object to the relevant array (for example, a new `intel_briefs` entry).
- Commit the change. The next scheduled run will automatically pick it up.
- Use descriptive `id` values—these surface in the metadata file for auditing.

## 4. Automate quarterly drafts with GitHub Actions

The repository includes a workflow triggered every quarter. It:

1. Runs `generate_newsletter.py` to create the latest Markdown and metadata files.
2. Uploads the Markdown as an artifact named `quarterly-newsletter`.
3. Opens a GitHub issue containing the draft so you can review/approve directly in GitHub.

### Before enabling the workflow

- Ensure the repository has the `newsletter-draft` label (the workflow creates it if missing).
- Optionally add the `NEWSLETTER_REVIEWERS` environment variable (comma separated GitHub usernames) if you want the issue auto-assigned.

### Send-out integration (optional)

If you use [Buttondown](https://buttondown.email/) or another service with an API:

1. Create an API token and store it as a GitHub secret (for example `BUTTONDOWN_TOKEN`).
2. Extend the workflow with an extra step that triggers a campaign once you close the draft issue. A small helper script can read `newsletter/latest.md`, convert it to HTML, and call the provider API.
3. Gate the send step on manual approval using the `workflow_dispatch` trigger or an environment protection rule so nothing ships without your review.

## 5. Troubleshooting tips

- If the workflow cannot open an issue (rate limits, permissions), you will still get the Markdown file via the artifact download.
- When adding new content entries, validate the JSON with `python -m json.tool automation/newsletter_content.json`.
- Use `--seed-offset` to force a different combination if a quarter pulls a similar mix to a previous message.

This setup keeps the "Stay informed" section active with minimal effort while letting you stay in control of the final approval step.
