---
name: upload-media
description: Attach images or videos to an Onlypult post, choosing between a public URL, a browser upload, or a direct upload from a local file path. Use whenever a post needs a photo, video or file.
---

# Upload media

How the file gets to Onlypult depends on where the file is and how Onlypult is connected. Pick the first row that fits.

| Situation | What to do |
|---|---|
| The file already has a public `http(s)` URL | Put it in `media_urls` of `posts_create` or `posts_update` (up to 10). No upload tools needed. |
| Local MCP (`@onlypult/mcp` over stdio) and you know the file's path | `media_get_upload_url` → `media_upload_file` with `file_path` and the `token` → use the returned file `id` in `media_ids` |
| Hosted MCP (`mcp.onlypult.com`), Claude.ai, ChatGPT, or a chat attachment with no disk path | `media_get_upload_url` → ask the user to open the link and drop the file → `media_check_status` → use the ids in `media_ids` |

## Browser flow in detail

1. `profiles_list`, then call `media_get_upload_url` with the **target profile's** `profile_id`. The profile decides transforms and watermarks, so use the same one as the post.
2. Pass `is_story: true` or `is_reels: true` if the post will be a Story or a Reel.
3. Give the user the `upload_url`. The page needs no login and the token lives **10 minutes**. Several files can be dropped in one session.
4. When the user says "done", call `media_check_status` with the token. It returns the file ids.
5. Use those ids in `media_ids` for `posts_create`, `posts_update`, or the Story, Reels and Shorts tools.

## Notes

- `media_upload_file` exists only in the local stdio package. If the tool is missing, you are on hosted MCP. Use the browser flow and don't try to read the user's disk.
- After `media_upload_file` the file id is returned at once. `media_check_status` is not needed.
- Files over 5 MB are uploaded in parts automatically.
- `posts_update` with `media_ids` **replaces** the post's media. Include every file you want to keep.
- If the token expired, call `media_get_upload_url` again. Don't reuse the old one.
