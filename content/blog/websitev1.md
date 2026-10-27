+++
date = '2026-10-28T01:32:34+05:30'
draft = false
title = 'Website v1'
+++

# how i made this website[AI]

Here's the whole build in the order it actually worked, with the fixes already applied. You could repeat it from scratch by following it.

## 1. Install the tools

Install Git, Hugo (extended), and create a GitHub account (username `nyron0`). Check:

```
git --version
hugo version
```

## 2. Create the site

```
hugo new site nyron-space
cd nyron-space
git init
```

## 3. Set the website-only commit email

Do this before the first commit, so website commits don't show on your profile graph:

```
git config user.name "Your Name"
git config user.email "website@example.com"
```

This applies to this repo only, and the email isn't linked to your GitHub account.

## 4. Add the PaperMod theme

```
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive
```

## 5. Configure `hugo.toml`

```toml
baseURL = 'https://nyron0.github.io/'
locale = 'en-US'
title = 'nyron0'
theme = 'PaperMod'

[params]
  defaultTheme = 'auto'

  [params.assets]
    favicon = '/favicon.ico'
    apple_touch_icon = '/apple-touch-icon.png'

  [params.profileMode]
    enabled = true
    title = 'Hi, I’m nyron0'
    subtitle = 'Developer. I build things and write about them.'

    [[params.profileMode.buttons]]
      name = 'Coding Math - 30 days'
      url = '/codingmathseries/'

[[menu.main]]
  identifier = 'projects'
  name = 'Projects'
  url = '/projects/'
  weight = 10

[[menu.main]]
  identifier = 'cheatsheet'
  name = 'Cheatsheet'
  url = '/cheatsheet/'
  weight = 30

[[menu.main]]
  identifier = 'blog'
  name = 'Blog'
  url = '/blog/'
  weight = 80

[[menu.main]]
  identifier = 'about'
  name = 'About'
  url = '/about/'
  weight = 100
```

The two fixes from your version are `buttons` (plural) and the title without the leftover "Your".

## 6. Add the favicon

Generate the icons at realfavicongenerator.net and put the files in `static/`: `favicon.ico`, `apple-touch-icon.png`, and optionally `favicon.svg` and `favicon-96x96.png`. The config refers to them as `/favicon.ico`, without `static` in the path.

## 7. Add content

```
hugo new content about.md
hugo new content projects/_index.md
hugo new content projects/my-first-project.md
hugo new content blog/_index.md
hugo new content cheatsheet/_index.md
hugo new content codingmathseries/_index.md
```

Open each file, write something, and remove `draft: true` so the page shows up. Preview with `hugo server` at `http://localhost:1313`.

## 8. Ignore build files

```
printf "public/\nresources/_gen/\n.hugo_build.lock\n" > .gitignore
git branch -M main
```

## 9. Create the GitHub repo and enable Pages

1. Create a **public** repo named `nyron0.github.io` with no README.
2. Go to **Settings → Pages**, and set **Source** to **GitHub Actions**.

## 10. Add the deploy workflow

```
mkdir -p .github/workflows
nano .github/workflows/hugo.yaml
```

This is the corrected version, with `mkdir -p` in place. It's a trimmed version of Hugo's official workflow, without the Sass, Go, Node and cache steps that PaperMod doesn't need:

```yaml
name: Build and deploy
env:
  HUGO_VERSION: 0.169.0
on:
  push:
    branches:
      - main
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: pages
  cancel-in-progress: false
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v7
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v6
      - name: Install Hugo
        run: |
          curl -sfL --output-dir "${{ runner.temp }}" -O "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_${HUGO_VERSION}_linux-amd64.tar.gz"
          mkdir -p "${HOME}/.local/hugo"
          tar -C "${HOME}/.local/hugo" -xf "${{ runner.temp }}/hugo_${HUGO_VERSION}_linux-amd64.tar.gz"
          echo "${HOME}/.local/hugo" >> "${GITHUB_PATH}"
      - name: Build
        run: hugo build --gc --minify --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v5
        with:
          path: ./public
  deploy:
    runs-on: ubuntu-latest
    needs: build
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v5
```

## 11. First push

```
git add .
git commit -m "Initial site setup"
git remote add origin https://github.com/nyron0/nyron0.github.io.git
git push -u origin main
```

If it asks you to log in, use `gh auth login`. Then open the repo's **Actions** tab, wait for the green check, and visit `https://nyron0.github.io`.

## 12. Add the convenience command

```
git config alias.publish '!f() { git add . && git commit -m "${1:-Update site}" && git push; }; f'
```

## Your routine from now on

1. Edit files and preview with `hugo server`.
2. Run `git status` to see what will go out.
3. Publish with `git publish "what you changed"`.

## Problems we hit and how they were fixed

- **Install Hugo step failed:** `mkdir` needed `-p` (included in the workflow above).
- **Config warning:** `languageCode` was renamed to `locale`.
- **Button not showing:** `button` needed to be `buttons`.
- **Favicon:** the config must use `/favicon.ico` and the files must live in `static/`.
