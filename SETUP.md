# Unicorn's Hideout: setup (5 steps, ~5 minutes)

Do these in the Firebase Console for project **unicorn-hideout**, in this order.

1. **Authentication → Sign-in method → Anonymous → Enable.**
   The app signs every visitor in anonymously and uses that uid as their identity.
2. **Storage → Get started** (if you haven't already). Then open the **Rules** tab and paste in `storage-rules.txt`, then Publish.
   Note: newer Firebase projects may require the Blaze (pay-as-you-go) plan to use Storage. There's a free usage tier.
3. **Realtime Database → Rules** and paste in `firebase-rules.json`, then Publish.
4. **Optional but recommended: clear old data.** Old posts/profiles belong to the old `client_...` ids, which no longer match any signed-in user, so nobody can delete or edit them. Realtime Database → Data → delete the `posts`, `profiles`, `follows`, `conversations` and `messages` nodes (or leave them; they still display).
5. **Upload `Unicorns-Hideout.html`** wherever you host it.
   Then in Authentication → Settings → **Authorized domains**, make sure your site's domain is listed.

## Hardening the public API key (recommended)
The Firebase `apiKey` in the page is public by design. Protect it by:
- Google Cloud Console → APIs & Services → Credentials → restrict the browser key to your site's domain (HTTP referrers).
- Enabling **App Check** (reCAPTCHA v3) for Realtime Database and Storage.

## What changed
- Anonymous Auth replaces the localStorage `clientId`; the uid can't be spoofed.
- Rules: you can only create/delete your own posts, edit your own profile/follows, send messages as yourself, read only your own conversations and follow list. Likes/reports are per-user; field lengths and server timestamps are validated.
- Images (posts, avatars, chat) are resized, uploaded to Storage under `users/<uid>/...` (5 MB, images only), and only the URL is stored in the database. Deleting a post deletes its image.
- Posts load 50 at a time with a "Load older posts" button.
- Bug fixes: names/avatars on posts and comments now update when someone changes their profile (they used to be frozen copies); the 60-second refresh no longer wipes a half-typed comment (timestamps update in place, and drafts survive re-renders); double-submit protection with "Posting…" states; 3+ reports hides a post for everyone but its author.

## Known limits
- Anonymous accounts live in the browser. Clearing site data or switching devices creates a new identity (profile name is re-entered). To make accounts portable later, add email/Google sign-in and link it to the anonymous account.
- Chat images are readable by any signed-in user who has the (unguessable) URL; message text itself is private to the two participants.
- Anyone can message anyone; there's no block feature yet.
