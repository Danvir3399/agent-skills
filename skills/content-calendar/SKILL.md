---
name: content-calendar
description: Plan a content calendar and fill it with scheduled or draft posts in Onlypult. Use when the user wants a week or month of posts, a posting plan, or to see what is already scheduled.
---

# Content calendar

## Steps

1. **See what exists.** `account_get`, `profiles_list`, then `posts_list` with `status: "scheduled"` (and `"draft"`) and a `publish_after` / `publish_before` window for the period. Show the user the gaps so you don't double-book.
2. **Learn what works.** If the profile has Analytics, call `analytics_top_posts` and `analytics_best_time` (see `analytics-report` and `best-time-to-post`) and build the plan around real winners and real slots. Without analytics, ask about topics and frequency.
3. **Draft the plan first, in the chat.** A table: date and time, profile, format (feed, Reel, Story), text, media needed. Wait for the user to approve or edit before creating anything.
4. **Create the posts** after approval. Use `is_draft: true` when the user wants to review in Onlypult, otherwise `publish_at`. Create posts one by one with `posts_create`, or `posts_create_reels` / `posts_create_story` / `posts_create_shorts` for those formats (`short-form-video` skill).
5. **Summarize.** List what was created with ids and local times, and what still needs media.

## Rules

- All times in the account timezone, `"Y-m-d H:i"`, not after `plan_end_at`.
- Don't create more than the user approved, and don't schedule over existing posts without saying so.
- Generated text must respect each platform's limits. Check `posts_limits` for each profile.
- Reuse saved hashtag sets (`hashtag-sets` skill) instead of inventing tags every time.
- If a media file is needed and the user has none yet, create the post as a draft and say that media is missing.
