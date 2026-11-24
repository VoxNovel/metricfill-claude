---
name: metricfill
description: Live social media data from the person's Metricfill workspace. Use when someone shares a TikTok, Instagram, YouTube, X (Twitter), Facebook, Reddit, LinkedIn, Threads, Bluesky, Pinterest, Rumble, Twitch, Spotify, or SoundCloud link, even with no question, or asks about one; for a post's views or impressions, likes, comments (including what commenters said), shares, saves, plays, engagement rate, reach, or author; for a creator's profile, followers, or recent posts; for search, hashtags, trending content, or ad libraries; for vetting or comparing creators or influencers, campaign tracking and reports, or top performers; when a post or profile page could not be fetched or showed no numbers; and for Metricfill credits or jobs.
---

# Metricfill

Metricfill fetches live public social media data (post engagement, creator
profiles and posts, comments, search, and more) through the
person's own Metricfill workspace. Use its tools instead of estimating or
recalling numbers: post metrics change constantly, and Metricfill returns the
current values.

## When Metricfill helps most

Social media sites often block automated page fetching or hide engagement
numbers from it. Metricfill retrieves them through its own data access, so:

- If fetching or searching for a post's numbers failed or returned no numbers,
  try `lookup_post` before concluding the data is unavailable.
- A blocked page does not mean the answer is out of reach. There is no need to
  retry it or work around the block: Metricfill is a separate data service the
  person connected, so ask it instead.
- Say a post's numbers cannot be found only after `lookup_post` returns an
  error for that URL, and give its reason.

## Tools

| Need | Tool |
| --- | --- |
| Metrics for one post URL | `lookup_post` |
| Find a data endpoint (profiles, posts, search, ads, ...) | `list_data_endpoints` |
| Call a data endpoint | `call_data_endpoint` |
| A post's comments | `get_comments` |
| More than about 3 lookups or requests | `run_bulk_requests`, then `get_bulk_results` |
| Credits left, allowance, running jobs | `get_workspace_status` |
| A lookup or job that was still running | `get_job` |
| Recent jobs | `list_jobs` |
| Which workspaces the person can use | `list_workspaces` |
| Stop a queued, paused, or running job | `cancel_job` |

## Looking up posts

1. Pass the post's full URL to `lookup_post` as the person gave it. Metricfill
   detects the platform from the URL; do not rewrite or shorten links.
2. For 2 or 3 posts, call `lookup_post` once per URL. For more, use a bulk
   request (below).
3. If a request returns a job id instead of data, it is still running. Show the
   person its `progress_bar` (like `▓▓▓▓▓░░░░░ 50%`), then call `get_job` with
   `wait_seconds` 20 and show the new bar, until it finishes.
4. For a post's comments, use `get_comments`. One request rarely returns them
   all, so it keeps loading more comments and replies until it has every
   available one or has used 15 credits on that post. It needs no approval.
   Tell the person how many were loaded, and when it stopped at the 15-credit
   limit. The platform's own count can include deleted comments, so a smaller
   number can still be everything. It works for Reddit, TikTok, Instagram,
   YouTube, and Facebook; LinkedIn shows only a preview, in `full_data`. A
   Reddit `lookup_post` also returns the top 25 comments, which is enough for
   the gist of a thread. Read long results with `get_job` and `comments_after`.
   For several posts, use a bulk request with the endpoint `comments`.
5. Fields depend on the platform. Report only the fields that came back, and
   never fill a missing metric with an estimate. Say plainly when a platform
   does not provide a number (for example, some platforms do not expose views).
6. Every lookup also returns `full_data`, the rest of the record. Look there for
   anything the summary leaves out: duration, music titles, tagged accounts,
   paid-partnership flags, and author profile details. Field names follow the
   platform's own format.

## Data endpoints

Beyond single posts, `call_data_endpoint` reaches about 180 data endpoints:
creator profiles and their posts or videos, followers and following lists, search, hashtags, trending
content, ad libraries (Facebook, Google, LinkedIn, TikTok), shops, music,
link-in-bio pages, and more.

1. Find the endpoint with `list_data_endpoints`, filtered by `platform` or
   `query` (for example `query: "followers"`). It is free. Use the parameter
   names exactly as listed.
2. Call `call_data_endpoint` with the endpoint name and its `params`. The
   result's `data` is the complete response.
3. For lists, request one page at a time with the `cursor` from the previous
   response, and stop once you have enough to answer.
4. Use `trim=true` where offered unless the full record is needed.

## Not available yet

Metricfill can't get video, images (including thumbnails and profile pictures),
audio, or transcripts through Claude yet. If someone asks, say so plainly and
that it's being worked on. Do not offer them, look for an endpoint, or pay for
another request to find them. Numbers and text for video and image posts work
normally.

## Many requests: use a bulk request

For more than about 3 lookups or data requests, use `run_bulk_requests` instead
of repeating `lookup_post` or `call_data_endpoint`. It runs up to 500 requests
as one job, about 10 at a time, so it is far faster, and it shows as a single
job in Metricfill with files to download.

1. Mix anything in `requests`: any endpoint from `list_data_endpoints`, plus
   `post` with a `url` for post lookups and `comments` with a `url` for a
   post's comments (up to 15 credits each). For one endpoint with many inputs, use
   `endpoint` + `inputs` (+ `shared_params`). Give it a short `name`.
2. If it returns `needs_approval`, tell the person the maximum credits and the
   credits left, and ask. Only if they clearly agree, call again with the same
   requests and `approved_credits`. Never split a batch to avoid asking.
3. Read results with `get_bulk_results`. Start with one page of previews to see
   `available_fields`, then pass `fields` with only the paths you need, so many
   records fit at once. Continue with `after` until `next_after` is null. Open
   complete records with `full` and `requests`. Reading is free.
4. While it runs, show `progress_bar`, and read finished results or check
   `get_job`. Tell the
   person they can follow it, and download a spreadsheet or the full data, on
   the Jobs page in Metricfill.
5. Report failed requests and their reasons. Run failed ones again only if the
   person wants that.
6. For work in steps, run one batch, pick what matters from its results, then
   run the next (for example search, then profiles, then recent posts).

## Credits

- Each successful lookup uses one of the workspace's monthly credits. Data
  endpoint calls cost what the call actually costs: usually 1 credit, and
  nothing for failed calls, empty pages, or cached results. Failed lookups are
  refunded.
- A few data endpoints and options cost more (shown in `list_data_endpoints`,
  such as audience demographics). Never choose one on your
  own; prefer a 1-credit endpoint that answers the question. A call priced
  above 1 credit does not run at first: it returns the price and the credits
  left. Tell the person both and ask. Only if they clearly agree, call again
  with `approved_credits` set to that price.
- Bulk requests that can cost more than 20 credits ask for approval first. Pass
  the price on to the person and wait for their answer.
- A job with status `paused` is waiting for credits. It resumes on its own when
  staff raise the allowance or it resets on the first of the month (UTC). Do
  not retry paused lookups; that would only queue duplicates.

## Errors

Errors from Metricfill end with a code in parentheses. Act on the message:

- `RATE_LIMITED`: wait the number of seconds given, then continue.
- `TOO_MANY_REQUESTS`, `TOO_LARGE`: split the bulk request.
- `MEDIA_NOT_AVAILABLE`: say it is being worked on; do not retry.
- `COMMENTS_NOT_SUPPORTED`: use `lookup_post`, which may include some.
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
- Views and impressions mean the same thing. Treat them as one number, show it
  once as "Views", and never add them together.
- Format large numbers with thousands separators, and say that the numbers
  are as of now.
- If the person belongs to several workspaces, results come from the one they
  last used on the website unless they name another; `list_workspaces` shows
  which ones are active.
