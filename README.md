# OEG Service Status

A website for monitoring the status of OEG services.
A minimal status page for OEG services, automatically rebuilt and published to GitHub Pages whenever the underlying services status data changes.

## How it works

An external daemon (not part of this repository) periodically checks a set of services and commits the results to [`services_status.json`](./services_status.json) in this repo. Each entry looks like this:

```json
{
    "name": "autocodemeta",
    "service_url": "https://autocodemeta.linkeddata.es",
    "status_http_code": 200,
    "timestamp": 1787841006000
}
```

Whenever that file changes (or the workflow is run manually), a GitHub Action:

1. Runs [`generate_index.py`](./generate_index.py), which reads `services_status.json`, formats each timestamp into a readable UTC date/time, and determines whether each service is up (`status_http_code` in the 2xx range).
2. Renders that data into [`index_template.html`](./index_template.html) using [pystache](https://github.com/PennyDreadfulMTG/pystache) to produce `index.html`, a simple table with a green/red indicator per service.
3. Publishes `index.html` to GitHub Pages.

## Project structure

| File                                       | Purpose                                                                                                                |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------- |
| `services_status.json`                     | Input data, updated by the external daemon                                                                             |
| `index_template.html`                      | Mustache template for the status page                                                                                  |
| `generate_index.py`                        | Reads the JSON and renders the template into `index.html`                                                              |
| `index.html`                               | Generated output (not meant to be edited by hand)                                                                      |
| `.github/workflows/update-status-page.yml` | Runs the script on every relevant commit (and manually via `workflow_dispatch`) and deploys the result to GitHub Pages |

## Running locally

```bash
pip install pystache
python3 generate_index.py
```

This generates (or overwrites) `index.html` in the same directory, using whatever `services_status.json` is present there.

## Publishing

GitHub Pages is configured to deploy from **GitHub Actions** (Settings → Pages → Source), so no manual publishing step or branch push is required beyond letting the workflow run.
