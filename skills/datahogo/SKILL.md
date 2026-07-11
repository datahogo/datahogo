---
name: datahogo
description: Scan the current project for security vulnerabilities using Data Hogo, an open-source local security scanner. Use this when the user asks to check for security issues, run a security scan, review code for vulnerabilities before deploying/shipping, or audit a repo for secrets/misconfigurations. Also use proactively before helping the user deploy or open a PR, if they haven't scanned recently.
---

# Data Hogo security scan

Data Hogo is an open-source CLI (`datahogo`, published on npm) that scans a
local project for security issues — secrets, injection, missing auth,
vulnerable dependencies, insecure config — across JS/TS, Python, Go, Java,
PHP, C#, mobile, and Supabase. It runs entirely locally; nothing is
uploaded anywhere.

## Running a scan

```bash
npx datahogo scan --json
```

Run this from the project root (or pass a path as an argument). Parse the
JSON output rather than the human-readable default — it's structured and
won't need re-parsing of colored terminal text.

If `npx` isn't available or the command fails, tell the user and stop —
don't attempt to reimplement scanning logic yourself.

## Interpreting results

The JSON has a `score` (0-100), a `summary` with counts per severity, and a
`findings` array. Each finding has `severity`, `title`, `file_path`,
`line_number`, `code_snippet`, and sometimes `fix_description`/`fix_code`.

Many findings will have no `description_technical` — the free local engine
doesn't include AI-generated explanations. When that's the case, explain
the finding yourself: you have the title, the OWASP reference if present,
and the code snippet, which is enough context to describe the risk clearly
in your own words.

Prioritize in this order:
1. `critical` and `high` severity findings in `context: "production"` with
   `classification` other than `"informational"` — these are the real,
   actionable issues.
2. Ignore or de-prioritize findings with `context` of `"test"`, `"example"`,
   `"config"`, `"rule"`, or `"vendored"` — these are expected/non-production
   locations, already down-weighted by the engine itself.

## Fixing

- For findings with `fix_code`, show the user the suggested change before
  applying it — don't silently overwrite files. Confirm the file and line
  match what's expected before editing.
- For findings without `fix_code`, propose a fix based on the vulnerability
  type and ask before making changes, same as any other code edit.
- After fixing, suggest re-running `npx datahogo scan --json` to confirm
  the finding is gone and no new issues were introduced.

## Other capabilities

- `npx datahogo scan --url https://example.com` — also checks a deployed
  URL for missing security headers, weak CSP, exposed source maps, etc.
- `npx datahogo scan --fail-on high` — exits non-zero if high/critical
  findings exist; useful if the user wants a pre-deploy gate.
- `npx datahogo doctor` — reports whether Semgrep/Gitleaks are installed
  (they add deeper checks but aren't required).

If the user has the `@datahogo/mcp` MCP server connected instead, prefer
its `scan_project`/`get_finding` tools over shelling out to the CLI — they
return the same data without needing to parse subprocess output.
