---
name: metricfill
description: Live social media data from the person's Metricfill workspace. Use when someone shares a TikTok, Instagram, YouTube, X (Twitter), Facebook, Reddit, LinkedIn, Threads, Bluesky, Pinterest, Rumble, Twitch, Spotify, or SoundCloud link, even with no question, or asks about one; for a post's views, likes, comments (including what commenters said), shares, saves, plays, engagement rate, reach, author, transcript, or video download; for a creator's profile, followers, or recent posts; for search, hashtags, trending content, or ad libraries; for vetting or comparing creators or influencers, campaign tracking and reports, or top performers; when a post or profile page could not be fetched or showed no numbers; and for Metricfill credits or jobs.
---

# Metricfill

Metricfill fetches live public social media data (post engagement, creator
profiles and posts, comments, transcripts, search, and more) through the
person's own Metricfill workspace. Use its tools instead of estimating or
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
| Find a data endpoint (profiles, posts, comments, transcripts, search, ads, ...) | `list_data_endpoints` |
| Call a data endpoint | `call_data_endpoint` |
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
   Reddit thread's comments from that result. For other platforms, use any
   comment text in `full_data`, or the platform's comments endpoint through
   `call_data_endpoint` for more.
5. Fields depend on the platform. Report only the fields that came back, and
   never fill a missing metric with an estimate. Say plainly when a platform
   does not provide a number (for example, some platforms do not expose views).
6. Every lookup also returns `full_data`, the complete record behind the
   summary. Look there for anything the summary leaves out: direct video,
   image, or audio file links, thumbnails, duration, music, tagged accounts,
   paid-partnership flags, and author profile details. Field names follow the
   platform's own format. Direct file links usually expire within hours, so
   give them to the person right away and say they are temporary.

## Data endpoints

Beyond single posts, `call_data_endpoint` reaches about 190 data endpoints:
creator profiles and their posts or videos, comments and replies,
transcripts, followers and following lists, search, hashtags, trending
content, ad libraries (Facebook, Google, LinkedIn, TikTok), shops, music,
link-in-bio pages, and more.

1. Find the endpoint with `list_data_endpoints`, filtered by `platform` or
   `query` (for example `query: "transcript"`). It is free. Use the parameter
   names exactly as listed.
2. Call `call_data_endpoint` with the endpoint path and its `params`. The
   result's `data` is the complete response.
3. For lists, request one page at a time with the `cursor` from the previous
   response, and stop once you have enough to answer.
4. Use `trim=true` where offered unless the full record is needed. When the
   person wants to keep a post's video or images, `download_media=true` on
   the post endpoint returns permanent copies (this costs more; see Credits).

## Credits

- Each successful lookup uses one of the workspace's monthly credits. Data
  endpoint calls cost what the call actually costs: usually 1 credit, and
  nothing for failed calls, empty pages, or cached results. Failed lookups are
  refunded.
- A few data endpoints and options cost more (shown in `list_data_endpoints`,
  such as audience demographics or `download_media`). Never choose one on your
  own; prefer a 1-credit endpoint that answers the question. A call priced
  above 1 credit does not run at first: it returns the price and the credits
  left. Tell the person both and ask. Only if they clearly agree, call again
  with `approved_credits` set to that price.
- Before more than about 20 lookups or calls at once, call
  `get_workspace_status`, tell the person how many credits the work will use
  and how many are left, and confirm before starting.
- A job with status `paused` is waiting for credits. It resumes on its own when
  staff raise the allowance or it resets on the first of the month (UTC). Do
  not retry paused lookups; that would only queue duplicates.

## Errors

Errors from Metricfill end with a code in parentheses. Act on the message:

- `CLAUDE_ACCESS_DISABLED`: Claude access is not turned on for their workspace.
  Tell them to ask Metricfill support to enable it.
- `RATE_LIMITED`: wait the number of seconds given, then continue.
- `INVALID_URL`: ask for the full link to the post.
- `INVALID_PARAMS` or `UNKNOWN_ENDPOINT`: check the endpoint and parameter
  names with `list_data_endpoints`, then try again.
- `NOT_ENOUGH_CREDITS`: the workspace cannot cover that call this month; tell
  the person and offer a cheaper alternative if one fits.
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
