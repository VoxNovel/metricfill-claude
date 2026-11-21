# Metricfill for Claude

Metricfill gives Claude live public engagement data for social media posts:
views, likes, comments, shares, authors, follower counts, and the top comments
on Reddit posts. Paste a post link or ask about a post, creator, or campaign,
and Claude looks up the current numbers through your Metricfill workspace.

This plugin contains:

- **The Metricfill connector**, a remote MCP server at
  `https://metricfill.com/mcp`.
- **The `metricfill` skill**, which tells Claude when to use Metricfill (for
  example, whenever a social media post link appears) and how to present the
  results.

## Requirements

- A Metricfill account in an active workspace. Metricfill workspaces are set up
  by Metricfill staff; contact support to get one.
- Claude access turned on for that workspace by Metricfill staff.
- Monthly credits on the workspace. Each successful lookup uses one credit;
  failed lookups are refunded.

## Setup

1. Install the plugin. In Claude Code:

   ```bash
   claude plugin marketplace add VoxNovel/metricfill-claude
   claude plugin install metricfill@metricfill
   ```

2. When Claude first uses Metricfill, sign in to your Metricfill account and
   approve the connection on the consent screen.

You can disconnect at any time under **Account settings → Connected apps** on
metricfill.com.

## What it can do

| Tool | What it does |
| --- | --- |
| `lookup_post` | Public engagement data for one post URL on TikTok, Instagram, YouTube, X (Twitter), Facebook, Reddit, LinkedIn, Threads, Bluesky, Pinterest, Rumble, Twitch, Spotify, or SoundCloud. Reddit posts include up to 25 top comments. |
| `get_workspace_status` | Credits left, monthly allowance, and running lookups. |
| `list_jobs`, `get_job` | Recent lookups and the status of one lookup. |
| `list_workspaces` | The workspaces you belong to and whether Claude access is on. |
| `cancel_job` | Cancels a queued, paused, or running lookup. |

## Example prompts

- "How's this TikTok doing? https://www.tiktok.com/@scout2015/video/6718335390845095173"
  Claude looks up the post and reports its views, likes, comments, shares, and
  author.
- "Compare these two videos for me: [TikTok link] [YouTube link]"
  Claude looks up both and shows a side-by-side table with the stronger post.
- "What are people saying in the comments on this? [Reddit post link]"
  Claude looks up the Reddit post and summarizes its top comments with their
  scores.
- "How many Metricfill credits do we have left this month?"
  Claude checks the workspace's credits and allowance.

## Data and privacy

- The plugin runs no code on your computer. It contains a skill (text
  instructions for Claude) and the address of the Metricfill connector.
- Claude sends Metricfill the post links you ask about, through the connector,
  after you sign in. Metricfill looks up the public post data and returns it to
  Claude. Nothing is sent anywhere else by the plugin.
- Claude acts with the same access your Metricfill account has, and nothing
  more.

Details: [Metricfill for Claude](https://metricfill.com/claude) ·
[Privacy policy](https://metricfill.com/privacy) ·
[Terms](https://metricfill.com/terms)

## Support

See [metricfill.com/claude](https://metricfill.com/claude#support) for help and
contact details.
