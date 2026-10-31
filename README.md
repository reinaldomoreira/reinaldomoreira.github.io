# GitHub user-pages root (`reinaldomoreira.github.io`)

Android App Links and iOS Universal Links fetch their association files from the **host root**
(`https://reinaldomoreira.github.io/.well-known/…`), never from a project path. The Alexa redirect bridge
lives under the project site `/do-it-public/alexa-link` (`tooling/public-site/`), so the association files
must be published from the **user-pages repo** `reinaldomoreira/reinaldomoreira.github.io`, whose root
is served at the host root.

This folder is exactly that repo's root:

- `.well-known/assetlinks.json` — Android App Links for `com.doit.do_it` (release + debug SHA-256).
- `.well-known/apple-app-site-association` — iOS Universal Links. `TEAM_ID` is a placeholder until the
  Apple Developer account exists (accepted Apple-gated deferral).
- `.nojekyll` — without it Jekyll drops dot-folders and `.well-known/` is never served.

## Publish (one-time, owner)

1. Create the **public** repo `reinaldomoreira/reinaldomoreira.github.io` on GitHub (the name must match exactly).
2. Copy this folder's contents (including the dot files, excluding this README if you like) to its root,
   then commit and push to `main`:
   ```bash
   git clone https://github.com/reinaldomoreira/reinaldomoreira.github.io.git
   cp -r tooling/github-user-pages/. reinaldomoreira.github.io/
   cd reinaldomoreira.github.io && git add -A && git commit -m "Add app association files" && git push
   ```
3. Repo → Settings → Pages → *Deploy from a branch* → `main` / `(root)`.
4. Verify:
   - <https://reinaldomoreira.github.io/.well-known/assetlinks.json> returns the JSON.
   - <https://digitalassetlinks.googleapis.com/v1/statements:list?source.web.site=https://reinaldomoreira.github.io&relation=delegate_permission/common.handle_all_urls>
     lists `com.doit.do_it`.
   - On a device: `adb shell pm verify-app-links --re-verify com.doit.do_it` then
     `adb shell pm get-app-links com.doit.do_it` shows `reinaldomoreira.github.io: verified`.

Keep this folder as the source of truth; `node --test tooling/verify_public_site.mjs` checks it.
When the signing key or Apple Team ID changes, update here and re-copy.
