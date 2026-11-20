---
name: short-form-video
description: Publish Instagram or Facebook Reels, YouTube Shorts, and Stories (Instagram, Facebook, VK, Telegram) in Onlypult. Use when the user mentions a reel, short, story, vertical video, or an AI-content label on those formats.
---

# Reels, Shorts and Stories

Each format has its own tool. Using plain `posts_create` for them posts a normal feed post by mistake.

| The user wants | Tool |
|---|---|
| Instagram or Facebook Reel | `posts_create_reels` |
| YouTube Short | `posts_create_shorts` (needs a `title`) |
| Story on Instagram, Facebook or VK | `posts_create_story` |
| Story on Telegram | `posts_create` with `platform_options.telegram.is_story: true` |

## Steps

1. `account_get` and `profiles_list`, as in the `schedule-post` skill.
2. **Upload the media with the right flag.** Call `media_get_upload_url` with `is_reels: true` for Reels or `is_story: true` for Stories, so the file is processed for that format. See the `upload-media` skill. A public video URL can go in `media_urls`.
3. Create the post with the tool from the table. It takes the same parameters as `posts_create`: `content`, `profile_ids`, `publish_at` or `publish_now` or `is_draft`, `media_ids` or `media_urls`.
4. Confirm the format and time back to the user.

## Rules that trip people up

- `is_story`, `is_reels` and `is_shorts` are mutually exclusive in one post.
- **Telegram Stories** publish only for a Telegram user or channel connected through MTProto. A bot account is rejected at publish time. Check the profile before promising it.
- **Instagram trial Reels:** pass `platform_options.instagram.graduation_strategy` as `MANUAL` (graduate in the Instagram app) or `SS_PERFORMANCE` (graduate automatically if it performs well). Leave it out for a regular Reel.
- **AI label** (AI info, Made with AI, AI disclosure): `platform_options.instagram.is_ai_generated` when `profiles_list` platform is `instagram_business` (any post type, including a Reel or Story). Facebook Page Reels: `platform_options.facebook.is_ai_generated` with `is_reels: true`. A legacy platform `instagram` and every other network return 400. Do not tell the user it was set when the API rejects it.
- **YouTube:** `platform_options.youtube.privacy` is `public`, `unlisted` or `private`. Get `category` ids from `profiles_get_youtube_categories`.
- **TikTok:** `privacy`, `disable_comment`, `disable_duet`, `disable_stitch` live in `platform_options.tiktok`.
- `posts_limits` returns the media limits for the profile and the account's maximum upload size. Check it before uploading a large video.
