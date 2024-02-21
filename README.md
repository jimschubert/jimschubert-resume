# jimschubert-resume

> The job search sucks, your resume shouldn't.

This is a dead-simple resume template. It wraps the free [Orbit theme](https://github.com/xriley/Orbit-Theme) by Xiaoying Riley into a Hugo template.

To use in your project, follow the Hugo instructions for initializing and referencing a theme.

For example, as a submodule:

```shell
hugo new site quickstart
cd quickstart
git init
git submodule add https://github.com/jimschubert/jimschubert-resume.git themes/jimschubert-resume
echo "theme = 'jimschubert-resume'" >> hugo.toml
cp themes/jimschubert-resume/exampleSite/data/content.yaml data/content.yaml
```

Next, edit the file at `data/content.yaml` to your liking.
Be sure to also create `assets/images/profile.png` (unless you look a lot like me).

## Parameters

The following parameters can be defined to customize the theme:

| Param              | Default   |
|--------------------|-----------|
| themeColor         | `#4B6A78` |
| textColor          | `#3F4650` |
| textColorSecondary | `#545E6C` |
| textGrey           | `#97AAC3` |
| divider            | `#e8e8e8` |
| lighterGrey        | `#ccc`    |
| darkerGrey         | `#666`    |
| smokyWhite         | `#f5f5f5` |
| sidebarPadding     | `20px`    |
| educationMain      | `false`   |

For example, if using YAML for your hugo config (be sure to wrap CSS colors in quotes!):

```yaml
params:
  educationMain: true
  themeColor: '#ececec'
```

The following parameters can be nested under `params.titles` to customize the titles displayed on the resume:

| Param         | Default               |
|---------------|-----------------------|
| education     | `Education`           |
| experiences   | `Experiences`         |
| projects      | `Projects`            |
| publications  | `Publications`        |
| skills        | `Skills & Proficiency`|
| experiences   | `Experiences`         |
| summary       | `Career Profile`      |

For example, if using YAML for your hugo config you could modify `skills` nad `experiences` like this:

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
      HUGO_VERSION: 0.122.0
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

The Hugo theme itself is [licensed](./LICENSE) under Apache 2.0.

The [Orbit theme's license](https://github.com/xriley/Orbit-Theme?tab=readme-ov-file#author--license) is reproduced below:

> This Bootstrap template is made by UX/UI designer [Xiaoying Riley](https://twitter.com/3rdwave_themes) for developers and is 100% FREE as long as you **keep the footer attribution link**. You do not have the rights to resell, sublicense or redistribute (even for free) the template on its own or as a separate attachment from any of your work.
>
> If you'd like to **use the template without the attribution link**, you can [buy the **commercial license** via the theme website](https://themes.3rdwavemedia.com/bootstrap-templates/resume/orbit-free-resume-cv-bootstrap-theme-for-developers/)
