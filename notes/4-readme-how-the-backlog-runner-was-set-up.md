# How the backlog runner was set up

This note covers how the Claude backlog runner was set up on this Windows PC. The runner uses leftover Claude usage to work on low-priority GitHub issues.

## Plugin

- Plugin: `backlog-runner`, from the marketplace at https://github.com/rohanchoudhary5210/claude-backlog-runner
- It is enabled in `~/.claude/settings.json`.

## Config

The config is kept outside the plugin cache so that plugin updates don't wipe it:

`C:\Users\CT_USER\.claude\backlog-runner\config.json`

| Setting | Value |
|---|---|
| owner | `prateekroonwal-capsitech` |
| repos, notes_repo, log_repo | `prateekroonwal-capsitech/test` |
| timezone | Asia/Kolkata |
| Usage floors | 80% weekly, 95% near reset, 85% 5-hour |
| Max tasks per run | 3, or 1 when there is no usage data |

## Tools

- **GitHub CLI** 2.105.0 is installed at `C:\Program Files\GitHub CLI\gh.exe` and signed in with `gh auth login` (keyring). The runner only makes REST `gh api` calls.
- **Python:** on this PC `python3` is only the Microsoft Store stub, so run the runner commands as `py -3 -m runner ...` from the skill folder.

## Usage data for the gate

`jq` isn't installed, so the plugin's `statusline_capture.sh` was ported to Node as `~/.claude/backlog-runner/statusline_capture.js` and set as Claude Code's `statusLine` command. The script writes `~/.claude/backlog-runner/usage.json`. Without that file the gate has no usage data and limits the run to 1 task.

## Repo prep (`test`)

- An initial README commit, to create the `main` branch.
- Labels: `backlog`, `safe`, `ask-first`, `approved`, `runner:done`, `runner:failed`.
- An issue titled `runner-log` (#1), where run summaries are posted.
- Optional: an issue titled `tonight`.

## Picking rules

An issue gets picked if it has:

- `backlog` + `safe`, or
- `backlog` + `ask-first` + `approved`.

Issues opened by anyone else also need `approved`.

## Test run

- Issue #2 produced draft PR #3. The issue was labelled `runner:done` and the summary was posted on #1.
- A second run skipped #2.

## How to run

```
/backlog-runner:claude-backlog-runner config: C:\Users\CT_USER\.claude\backlog-runner\config.json
```

## Windows gotcha

Git Bash ignores `TZ=Asia/Kolkata`. Get the summary time from PowerShell instead:

```powershell
[TimeZoneInfo]::ConvertTimeBySystemTimeZoneId([DateTime]::UtcNow,'India Standard Time').ToString('yyyy-MM-dd HH:mm')
```
