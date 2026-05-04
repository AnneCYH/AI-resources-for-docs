(set-up-google-analytics)=
# Set up Google Analytics

This prompt builds a hostname-filtered Free Form exploration in GA4,
comprising a *Top pages* table plus a *Views over time* line chart.

## Agentic browser access

This prompt is built for Claude Code and relies on its ability to access the GA4 UI in Chrome;
see {ref}`use-claude-with-chrome` for Claude setup instructions.

If you drive the prompt from a non-Claude agent, use
[Playwright MCP](https://github.com/microsoft/playwright-mcp) instead.

For Copilot CLI, add it via `/mcp add` in the interactive prompt
(command: `npx @playwright/mcp@latest`), or by editing
`~/.copilot/mcp-config.json` directly; see
[GitHub's docs](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/add-mcp-servers).

## The prompt

Before using the prompt, fill in the `{{angle-bracket}}` placeholders:

```
I have access to a GA4 property at {{full GA4 property URL, e.g. https://analytics.google.com/analytics/web/#/analysis/aXXXXXXXXXpYYYYYYYYY}}.

Goal: build a personal Free Form exploration scoped to my docs subdomain{{optional: ", shaped like the existing Detail Report at <report URL>" — delete this slot if there is no reference report}}.

My docs live at: {{base URL or hostname, e.g. https://ubuntu.com/myproduct/docs}}
Exploration name: {{e.g. "MyProduct documentation page views"}}
My GA login: {{e.g. you@canonical.com}}
Date range: Last 28 days

Drive the GA4 web UI yourself; I am not an expert on GA's UI.
End state I want, all in one Free Form exploration:

- Variables → Dimensions: Page path and screen class, Hostname
- Variables → Metrics: Views, Active users, Views per active user,
  Average engagement time per session, Event count
  (note: "...per active user" of engagement time does NOT exist in
  Explorations; use the per-session variant — call this out to me)
- Tab 1 "Top pages": table visualization. Rows = Page path and screen
  class. Values = the 5 metrics above, in that order. Sort by Views desc.
- Tab 2 "Views over time": line chart. Granularity = Day. Breakdowns =
  Page path and screen class. Values = Views. Lines per dimension = 10.
- Filter (apply to BOTH tabs separately — filters are per-tab):
  Hostname exactly matches {{hostname only, no protocol/path; e.g. docs.example.com}}

Pitfalls to pre-empt (these cost real time on a previous attempt):

1. The filter expression box has URL autocomplete. After typing the
   hostname, press Escape BEFORE clicking Apply or it will hijack the
   value. Verify the field shows exactly what you typed.
2. After Apply, wait ~5 seconds for auto-save before doing anything else.
   Do NOT switch tabs or navigate while a "There is a newer version of
   this exploration available" banner is on screen — it will roll back
   in-flight edits.
3. To rename a tab from "Free form 1" to a real name, double-click
   directly on the tab name text. The dropdown arrow only offers
   Delete/Duplicate. The pencil icon is just an active-tab indicator.
4. To filter by hostname you must first add Hostname to Variables →
   Dimensions; the FILTERS picker only lists dimensions present in
   Variables.
5. After you finish, reload the exploration URL once and verify both
   tabs and the filter survive the reload. If they don't, redo the
   missing piece — auto-save sometimes loses the last edit.

Verification before reporting done:
- Reload the exploration. Confirm Tab 1 totals are non-zero and only
  pages from the target hostname appear; Tab 2 line chart has labeled
  lines and no error.
- Show me the totals (Views, Active users) so I can sanity-check.
```
