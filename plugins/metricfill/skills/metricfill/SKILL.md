---
name: metricfill
description: Live engagement numbers for social media posts from the person's Metricfill workspace. Use when someone shares a TikTok, Instagram, YouTube, X (Twitter), Facebook, Reddit, LinkedIn, Threads, Bluesky, Pinterest, Rumble, Twitch, Spotify, or SoundCloud link, even with no question, or asks about one; for a post's views, likes, comments (including what Reddit commenters said), shares, saves, plays, engagement rate, reach, followers, author, or creator; for vetting or comparing creators or influencers, campaign tracking and reports, or top performers; when a post page could not be fetched or showed no numbers; and for Metricfill credits or jobs.
---

# Metricfill

Metricfill fetches live public engagement data for social media posts through
the person's own Metricfill workspace. Use its tools instead of estimating or
recalling numbers: post metrics change constantly, and Metricfill returns the
current values.

## When Metricfill helps most

Social media sites often block automated page fetching or hide engagement
numbers from it. Metricfill retrieves them through its own data access, so:

- If fetching or searching for a post's numbers failed or returned no numbers,
  try `lookup_post` before concluding the data is unavailable.
- Say a post's numbers cannot be found only after `lookup_post` returns an
  error for that URL, and give its reason.

## Tools

| Need | Tool |
| --- | --- |
| Metrics for one post URL | `lookup_post` |
| Credits left, allowance, running jobs | `get_workspace_status` |
| A lookup or job that was still running | `get_job` |
| Recent jobs | `list_jobs` |
| Which workspaces the person can use | `list_workspaces` |
| Stop a queued, paused, or running job | `cancel_job` |

## Looking up posts

1. Pass the post's full URL to `lookup_post` as the person gave it. Metricfill
   detects the platform from the URL; do not rewrite or shorten links.
2. For several posts, call `lookup_post` once per URL. Independent lookups can
   run in parallel.
3. If a lookup returns a job id instead of data, it is still running. Tell the
   person, then check it with `get_job` before reporting results.
4. Reddit lookups also return the post's top comments (up to 25, highest score
   first, with author, score, text, and date), so answer questions about a
   Reddit thread's comments from that result. For other platforms Metricfill
   returns comment counts, not comment text; say so plainly.
5. Fields depend on the platform. Report only the fields that came back, and
   never fill a missing metric with an estimate. Say plainly when a platform
   does not provide a number (for example, some platforms do not expose views).

## Credits

- Each successful lookup uses one of the workspace's monthly credits. Failed
  lookups are refunded.
- Before more than about 20 lookups at once, call `get_workspace_status`, tell
  the person how many credits the work will use and how many are left, and
  confirm before starting.
- A job with status `paused` is waiting for credits. It resumes on its own when
  staff raise the allowance or it resets on the first of the month (UTC). Do
  not retry paused lookups; that would only queue duplicates.

## Errors

Errors from Metricfill end with a code in parentheses. Act on the message:

- `CLAUDE_ACCESS_DISABLED`: Claude access is not turned on for their workspace.
  Tell them to ask Metricfill support to enable it.
- `RATE_LIMITED`: wait the number of seconds given, then continue.
- `INVALID_URL`: ask for the full link to the post.
- `ACCOUNT_SUSPENDED`, `NO_ACTIVE_WORKSPACE`, `WORKSPACE_INACTIVE`: the person
  needs to contact Metricfill support; do not retry.
- A failed lookup (for example a deleted or private post) is not retried
  automatically; report which link failed and why.

## Presenting results

- One post: a short summary with the key numbers, the author, and the post
  date.
- Several posts: a table with one row per post (platform, author, post date,
  views, likes, comments, shares), then the top performers and anything
  notable, such as an unusually high comment-to-like ratio.
- Format large numbers with thousands separators, and say that the numbers
  are as of now.
- If the person belongs to several workspaces, results come from the one they
  last used on the website unless they name another; `list_workspaces` shows
  which ones have Claude access.
