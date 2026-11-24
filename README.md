# laughingmeme
the personal blog

## Setup

```
bundle install
ln -s ../../bin/pre-commit .git/hooks/pre-commit
```

The pre-commit hook validates YAML front matter in `_posts/*.md` files using [yq](https://github.com/mikefarah/yq).

## Development

```
bundle exec jekyll serve
```

## Bluesky comments

A post gets a comment section when its front matter points at a companion
Bluesky post — the post you share the article from. Replies and likes on that
Bluesky post *are* the blog's comments:

```yaml
---
title: My Post
bsky_thread: https://bsky.app/profile/kellan.bsky.social/post/<rkey>
---
```

To create one: share the post on Bluesky (a real post written by you, not just
a bare link — bare link posts read as spam and get ignored), then paste the
post's URL from your browser bar into the post's front matter and push. Posts
without `bsky_thread` render no comment section at all — the feature is opt-in
per post.

Two modes, set by `bsky_comments_mode` in `_config.yml`:

- **`readonly` (current default)** — renders the thread read-only. Readers
  who want to participate click through and reply/like on Bluesky itself,
  where they're already signed in. The blog talks to nothing but Bluesky's
  public, unauthenticated AppView API and avatar CDN.
- **`auth`** — readers can sign in with their Bluesky account and reply/like
  without leaving the page. This is a one-line flip, but before enabling it,
  understand what it adds:
  - Sign-in runs through the **svebcomponents bridge**
    (`atproto.svebcomponents.dev`): a free, single-maintainer, beta community
    service. It holds your readers' Bluesky OAuth grants server-side and can
    create posts/likes/reposts as them. Its consent screen names
    "svebcomponents/atproto", not laughingmeme.org. Stats when this was
    written: 7 embedding sites, 21 lifetime sign-ins. If that custody
    bothers you (or your readers), the same MIT code self-hosts on a single
    small container (Node 22 + SQLite); point the `service` attribute at it.
  - Readers see a consent screen requesting narrow-but-real write scopes
    (`create posts`, `create/delete likes`, `create/delete reposts`) — the
    post grant is broader than "reply to this post" (the bridge self-limits
    to replies, but the token itself isn't scoped that narrowly).
  - Comment sections on a personal blog get little traffic; an empty
    sign-in wall can read deader than a link-out. Flipping back to
    `readonly` is as easy as flipping on.

The widget is [svebcomponents/atproto-comments](https://github.com/svebcomponents/atproto)
(MIT), vendored at `assets/js/atproto-comments-0.9.0.js` — a pinned copy, so
an npm or CDN compromise can't inject code into the site. To upgrade:
download the new version's `dist/client/index.js` from the npm tarball, diff
it against the vendored copy, and commit it under the new version name, then
update the two `src=` references in `_includes/bsky-comments.html`.

A deleted or renamed Bluesky anchor post orphans that article's comments
(replies still exist on commenters' accounts but the widget shows an error).
If you delete an anchor, remove the `bsky_thread` key from the post's front
matter too.
