---
name: best-time-to-post
description: Find the best days and hours to publish on a social profile from its Onlypult analytics, and optionally schedule posts into those slots. Use when the user asks when to post.
---

# Best time to post

## Steps

1. `analytics_profiles_list` → the profile's `an_…` id. This is **not** the publishing `profile_id`.
2. `analytics_best_time` for that profile. It returns a weekday × hour engagement heatmap.
3. Pick the top 3 to 5 slots. Report them as plain times, for example "Tuesday 18:00, Thursday 12:00". Mention that the heatmap measures engagement of the existing audience, and that few posts mean a noisy result. Check `coverage` before trusting it.
4. Check the timezone. Call `account_get` and state which timezone the slots are in, because scheduling uses the account timezone.
5. If the user wants posts scheduled into those slots:
   - Use the **publishing** `profile_id` from `profiles_list` (match by platform and username).
   - Check that a slot is not already taken: `posts_list` with `status: "scheduled"` and a `publish_after` / `publish_before` window.
   - Schedule with `posts_create` and `publish_at` in `"Y-m-d H:i"`. Follow the `schedule-post` skill.

## Notes

- Available for Instagram, Facebook, LinkedIn, TikTok and Twitter/X.
- If analytics and publishing are not both connected for the same account, you can still report best times but cannot link them by id. Match on platform and username, and say you did.
