---
name: hashtag-sets
description: Manage a profile's saved hashtag sets in Onlypult and reuse them in posts. Use when the user wants to see, create, rename or delete hashtag groups or tags, or to add saved hashtags to a post.
---

# Hashtag sets

Each publishing profile has saved hashtag sets ("groups"). Reuse them instead of retyping.

## Tools

| Goal | Tool |
|---|---|
| Show sets and their tags | `hashtags_list` (needs `profile_id`) |
| New set | `hashtag_groups_create` (`profile_id`, `title`) |
| Rename a set | `hashtag_groups_update` (`profile_id`, `group_id`, `title`) |
| Add a tag | `hashtags_create` (`profile_id`, `tag`, optional `group_id`) |
| Delete a tag | `hashtags_delete` (`profile_id`, `tag_id`) |
| Delete a set and all its tags | `hashtag_groups_delete` (`profile_id`, `group_id`) |

## Steps

1. `profiles_list` for the `profile_id`. Hashtag sets belong to a profile, so ask which one when it is unclear.
2. `hashtags_list` first. It gives the integer `group_id` and `tag_id` values the other tools need.
3. Add tags **without** the `#`. If you omit `group_id`, the default set is used, or created if none exists.
4. To use a set in a post, read it with `hashtags_list` and put the tags into `content` as `#tag` text. There is no separate hashtag field on a post.

## Be careful

- `hashtag_groups_delete` removes the set and every tag in it. Confirm with the user first.
- Check `posts_limits` for the hashtag count allowed on the platform before pasting a big set.
- Sets are per profile. A set from the Instagram profile does not appear on the LinkedIn one.
