# rdpxx — RDP via GitHub Actions + ngrok

Spins up an RDP server on a GitHub-hosted `windows-latest` runner, tunnels it through ngrok, and prints the connection string (`IP:port`, `runneradmin`, `P@ssw0rd!`) to the Actions run log. You connect with `mstsc.exe`. Each run lasts ~6h, then is torn down — fresh machine next run.

## Trigger

- **Manual**: Actions tab → "RDP via ngrok" → Run workflow. Optional `keep_alive_hours` input (max 6).
- **Scheduled**: every 6h (UTC 00:00, 06:00, 12:00, 18:00). Edit/disable in `rdp-ngrok.yml` if you don't want automatic runs.

## How to use

1. Run the workflow.
2. Open the run's logs, find the step "Start ngrok tunnel & capture endpoint".
3. Copy the address line — looks like `0.tcp.ngrok.io:12345`.
4. On your Windows machine:
   ```
   mstsc /v:0.tcp.ngrok.io:12345
   ```
5. Login: **runneradmin** / **P@ssw0rd!**
6. Session ends when the 6h job timer expires.

## Credentials baked in

- ngrok authtoken is hardcoded in `.github/workflows/rdp-ngrok.yml` per repo-owner request.
- RDP password is the common `P@ssw0rd!` used across GitHub-hosted-runner RDP setups.

⚠ Both are readable in commits and run logs. Anyone with access to this repo can hijack your ngrok account or log in to a running session. Move the ngrok token to a repo secret (`NGROK_AUTH_TOKEN`) and reference `${{ secrets.NGROK_AUTH_TOKEN }}` if you want to lock this down.

## Files

- `.github/workflows/rdp-ngrok.yml` — the workflow.
