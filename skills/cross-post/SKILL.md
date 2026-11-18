---
name: cross-post
description: Publish or schedule the same content to several social profiles at once in Onlypult, adapting it to each platform's limits. Use when the user says cross-post, post everywhere, or names two or more networks for one message.
---

# Cross-post

Send one piece of content to two or more profiles without breaking a platform's rules.

## Steps

1. Call `account_get` (timezone, `plan_end_at`) and `profiles_list`. Resolve each network the user named to one profile `id`. Ask when a platform has more than one profile.
2. Call `posts_limits` for **every** target profile. Compare the text length, hashtag and mention counts, and media count against each platform.
3. If one text does not fit everywhere, don't truncate silently. Pick one:
   - Write a shorter version for the strict platforms and make **separate** `posts_create` calls per group of profiles.
   - Or keep one text and drop the platforms that cannot take it, after telling the user.
4. When the text fits everywhere, call `posts_cross_post` with at least 2 `profile_ids`. Use `publish_now: true` or `publish_at` (`"Y-m-d H:i"`, account timezone).
5. Platform-specific needs go in `platform_options`: YouTube needs a `title`, Pinterest a `board_id`, TikTok a `privacy` value.
6. Report per profile what was created and when it goes out.

## Watch for

- Instagram, TikTok, YouTube and Pinterest need media. A text-only cross-post to them fails.
- `first_comment` only applies to Instagram, Facebook and LinkedIn.
- Hashtags that suit Instagram often look wrong on LinkedIn. Offer to trim them.
- If some profiles fail, use the `fix-failed-posts` skill. Don't resend the whole cross-post.
