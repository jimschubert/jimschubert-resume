# jimschubert-resume

> The job search sucks, your resume shouldn't.

This is a modern single-column portfolio resume theme for Hugo. It is designed to stay lightweight: custom SCSS, no Bootstrap dependency, and no runtime JavaScript.

To use in your project, follow the Hugo instructions for initializing and referencing a theme. These assume you're not on Windows (I don't use it, sorry).

## Use: as a Hugo module

This is the step I prefer.

```shell
hugo new site quickstart
cd quickstart
hugo mod get github.com/jimschubert/jimschubert-resume
cp $(go env GOMODCACHE)/github.com/jimschubert/jimschubert-resume*/exampleSite/data/content.yaml data/content.yaml
```

## Use: as a submodule

If you don't want to reference the theme via Hugo modules, follow these steps instead.
This is the step you often see on themes. I don't like git's submodules functionality, but you do you.

```shell
hugo new site quickstart
cd quickstart
git init
git submodule add https://github.com/jimschubert/jimschubert-resume.git themes/jimschubert-resume
echo "theme = 'jimschubert-resume'" >> hugo.toml
cp themes/jimschubert-resume/exampleSite/data/content.yaml data/content.yaml
```

## Make it yours

Next, edit the file at `data/content.yaml` to your liking.
Be sure to also create `assets/images/profile.png` (unless you look a lot like me).

To preview the bundled example site from this repository:

```shell
cd exampleSite
hugo server --themesDir ../.. --theme jimschubert-resume
```

## Parameters

The following parameters can be defined to customize the theme:

| Param             | Default    |
|-------------------|------------|
| accentColor       | `#5b67f1`  |
| accentColorStrong | `#4338ca`  |
| backgroundColor   | `#f3f6fb`  |
| surfaceColor      | `#ffffff`  |
| surfaceMutedColor | `#f8faff`  |
| textColor         | `#0f172a`  |
| mutedColor        | `#475569`  |
| borderColor       | `#d8e1ee`  |
| tagBackground     | `#eef2ff`  |
| pageWidth         | `980px`    |
| educationMain     | `false`    |

For example, if using YAML for your hugo config (be sure to wrap CSS colors in quotes!):

```yaml
params:
  educationMain: true
  accentColor: '#0f766e'
  accentColorStrong: '#115e59'
  backgroundColor: '#f0fdfa'
```

`themeColor`, `textColor`, `textColorSecondary`, and `divider` are still recognized as fallbacks for older configs, but the new color parameters are the preferred v2 interface.

The following parameters can be nested under `params.titles` to customize the titles displayed on the resume:

| Param         | Default               |
|---------------|-----------------------|
| education     | `Education`           |
| experiences   | `Experiences`         |
| projects      | `Projects`            |
| publications  | `Publications`        |
| skills        | `Skills & Proficiency`|
| languages     | `Languages`           |
| interests     | `Interests`           |
| summary       | `Profile`             |

For example, if using YAML for your hugo config you could modify `skills` and `experiences` like this:

```yaml
params:
  titles:
    skills: Knowledge
    experiences: Job History
```

# Deploying

Check out https://gohugo.io/hosting-and-deployment/hugo-deploy/

To host for free to GitHub Pages, add the following to `.github/workflows/hugo.yaml`:

```yaml
name: Deploy Hugo site to Pages

on:
  push:
    branches:
      - main
      - master
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: "pages"
  cancel-in-progress: false

defaults:
  run:
    shell: bash

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      HUGO_VERSION: 0.161.1
    steps:
      - name: Install Go
        uses: actions/setup-go@v4
        with:
          go-version: 'stable'
      - name: Checkout
        uses: actions/checkout@v4
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v4
      - name: Install Node.js dependencies
        run: "[[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true"
      - name: Build with Hugo
        env:
          HUGO_ENVIRONMENT: production
          HUGO_ENV: production
        run: |
          CGO_ENABLED=1 go install -tags extended github.com/gohugoio/hugo@v${HUGO_VERSION}
          hugo version
          hugo \
          --gc \
          --minify \
          --baseURL "${{ steps.pages.outputs.base_url }}/"
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v2
        with:
          path: ./public
  deploy:
    needs: build
    permissions:
      pages: write
      id-token: write
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v3
```

This differs a bit from the example on Hugo's docs. I found that `go.mod` is sometimes updated different on Mac than on Linux with certain versions of Go and Hugo. The above works consistently.

## Custom CNAME

See GitHub's doc on [Managing a custom domain for your GitHub Pages site](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site).

If you already have a custom domain associated with one GitHub repository, you can associate another domain by following the same steps for the apex domain but adding a `CNAME` record for `www` which points to your `{user}.github.io`.

Here are my settings for a second domain of mine:

| Host name | Type |  TTL  | Data |
|:---------:|:----:|:-----:|:-----|
| | A | 3600 | `185.199.108.153` |
| | A | 3600 | `185.199.109.153` |
| | A | 3600 | `185.199.110.153` |
| | A | 3600 | `185.199.111.153` |
| | AAAA | 3600 | `2606:50c0:8000::153` |
| | AAAA | 3600 | `2606:50c0:8001::153` |
| | AAAA | 3600 | `2606:50c0:8002::153` |
| | AAAA | 3600 | `2606:50c0:8003::153` |
| www | CNAME | 3600 | `jimschubert.github.io.` |

# License

This theme is [licensed](./LICENSE) under Apache 2.0.
