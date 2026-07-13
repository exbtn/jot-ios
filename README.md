# Hosting the Jot privacy policy on GitHub Pages (free)

This folder's contents (`privacy/index.html`) are meant to become the root of a GitHub repo named
`jot` under your account (`exbtn`), so the policy ends up at the URL already referenced in
`PaywallView.swift`:

```
https://exbtn.github.io/jot/privacy/
```

## 1. Create the repo

Go to https://github.com/new and create a repo named exactly `jot` under `exbtn` (public — GitHub
Pages is free for public repos). Don't initialize it with a README/license/gitignore.

If you already have (or plan to have) a `jot` repo for something else (e.g. the app's marketing
site), just copy the `privacy/` folder into that repo instead of creating a new one — same result,
as long as Pages is enabled for it.

## 2. Push this folder to it

From inside this directory:

```bash
git init
git add privacy README.md
git commit -m "Add Jot privacy policy"
git branch -M main
git remote add origin https://github.com/exbtn/jot.git
git push -u origin main
```

## 3. Enable GitHub Pages

In the repo on GitHub: **Settings → Pages**.
- Source: "Deploy from a branch"
- Branch: `main`, folder: `/ (root)`
- Save

GitHub will publish the policy at:

```
https://exbtn.github.io/jot/privacy/
https://exbtn.github.io/jot-ios/privacy/
```

(may take 1–2 minutes on first deploy; note the trailing slash)

## 4. Already wired into the app

`PaywallView.swift`'s `privacyURL` has been updated to this exact URL, so once the page is live,
the in-app "Privacy Policy" link on the paywall will work with no further code changes.

Also set this same URL as the "Privacy Policy URL" in App Store Connect's app listing before
submission.
