---
name: schedule-post
description: Create, schedule, publish or draft a social media post in Onlypult. Use when the user wants to post something, schedule it for a time, save a draft, publish now, edit or delete a scheduled post, or asks whether a publishing option exists (AI label, poll, pin, Story, Reels, first comment, auto-delete).
---

# Schedule a post

Turn the user's request into one Onlypult post, in the right mode, on the right profiles.

## Steps

1. **Get the account context.** Call `account_get`. It returns `timezone`, `plan_end_at` and `publish_at_format`. Times the user gives are in that timezone unless they say otherwise.
2. **Pick the profiles.** Call `profiles_list`. Match the user's words ("my Instagram", "the company LinkedIn") to profile `id`s. If two profiles share a platform, or the match is unclear, ask which one. Never guess between accounts.
   If the user asks what networks are supported, call `profiles_platforms_list`.
3. **Check limits when the text is long or has media.** Call `posts_limits` with a `profile_id` and compare text length and media count. This is cheaper than finding out from a 422 error.
4. **Choose the mode from what the user said:**
   - "draft", "save for later", "don't publish" → `posts_create` with `is_draft: true`
   - "now", "immediately" → `posts_publish_now`
   - a date or time → `posts_create` with `publish_at`
5. **Format the time.** `publish_at` is always `"Y-m-d H:i"` in the account timezone, for example `"2026-07-07 14:00"`. Convert "tomorrow at 2pm" yourself using today's date. It must be in the future and not later than `plan_end_at`.
6. **Add media if there is any.** See the `upload-media` skill. A public image or video URL goes in `media_urls` (up to 10).
7. **Create the post, then confirm.** Tell the user the profiles, the local time it goes out, and the post id. Offer `posts_get` if they want to check it.

## If they ask whether something is possible

Answer from this list. Do not say a listed option is unsupported. Put per-platform settings in `platform_options.{platform}`, never as top-level fields. An unknown key, an unknown field, or a field on the wrong platform returns HTTP 400 and is not applied — tell the user, do not claim it was set.

`profiles_list` platform `instagram_business` (Facebook or Instagram Login) uses the `instagram` key. A legacy platform value `instagram` cannot set `is_ai_generated`.

- AI label (AI info, Made with AI, AI disclosure): `platform_options.instagram.is_ai_generated` (any post type) or `platform_options.facebook.is_ai_generated` together with `is_reels: true` (Page Reels). Rejected everywhere else.
- Story: `is_story` on instagram, facebook, vkontakte, telegram (MTProto user or channel). See the `short-form-video` skill.
- Reel and trial Reel: `is_reels`; `graduation_strategy` `MANUAL` or `SS_PERFORMANCE` on instagram only.
- YouTube Short: `is_shorts`, plus `privacy` and `category`.
- TikTok: `privacy`, `disable_comment`, `disable_duet`, `disable_stitch`.
- Comments off: `disable_comment` on instagram, tiktok, vkontakte.
- Pin after publish: `pin_message` on telegram and max.
- Native poll: `platform_options.telegram.poll` or `vkontakte.poll`, or top-level `poll`.
- Pinterest: `board_id` from `profiles_get_pinterest_boards`, and `link`.
- Google Business Profile: `eventEnabled`, `startDateTimeAt`, `endDateTimeAt`, `callToActionEnabled`, `callToActionType`, `callToActionUrl`.
- `first_comment` posts a follow-up comment on Instagram, Facebook and LinkedIn. On other platforms the API accepts it and drops it — say it will not be posted.
- `delete_at_enable` with `delete_at_offset_value` and `delete_at_offset_unit` (60, 3600, 86400 or 604800 seconds) removes the post after it publishes. Every platform.
- `title` is required for YouTube and recommended for Pinterest.

## Editing and deleting

- `posts_update` changes a draft or scheduled post only. A published post cannot be edited. Say that plainly instead of retrying.
- `media_ids` in `posts_update` replaces existing media. `media_urls` is merged.
- `posts_delete` removes drafts and scheduled posts. Confirm with the user first, because it cannot be undone.

## Errors

A 422 carries `code`, `message` and `retryable`. If `retryable` is `false` (for example `publish_date_after_plan`), do not send the same request again. Explain the problem and offer a fix, such as an earlier date.
