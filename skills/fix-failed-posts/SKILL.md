---
name: fix-failed-posts
description: Find Onlypult posts that failed to publish, explain why, fix the cause and retry. Use when the user says a post did not go out, a platform failed, or asks what went wrong with a post.
---

# Fix failed posts

Find what failed, say why in plain words, fix the cause, and retry only when a retry can work.

## Steps

1. **Find the failures.** Call `posts_list_failed`. Filter with `profile_id`, or `publish_after` / `publish_before` for a time window. For one post the user named, call `posts_get`.
2. **Read the reason** on each failed post and sort it:
   - **The account needs reconnecting** (login expired or permission revoked): the user must reconnect the profile in Onlypult. Check `profiles_get` for its `status`, and tell them which profile it is. A retry will fail until they do.
   - **The post broke a platform rule** (text too long, wrong or missing media, a Reel with no video): compare with `posts_limits`, fix it with `posts_update`, then retry.
   - **A temporary problem** (timeout, platform outage, rate limit): retry as is.
   - **Something to change on the platform itself** (for example an Instagram account that is not Business or Creator): tell the user exactly what to change.
3. **Retry.** `posts_retry` for one post. `posts_retry_all_failed` for many, optionally with `profile_id`. Don't retry what still needs the user to act, because it will fail the same way.
4. **Confirm.** After a short wait call `posts_get` again and tell the user what published and what still failed.

## Be careful

- `posts_retry_all_failed` retries everything in scope. Before calling it, list what it will touch and get a yes if the list includes old posts the user may no longer want.
- One retry after a fix is enough. Never loop.
- If the user would rather publish at a new time than retry now, use `posts_update` with a new `publish_at`.
