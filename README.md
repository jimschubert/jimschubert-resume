# jimschubert-resume

> The job search sucks, your resume shouldn't.

This is a modern two-column resume theme for Hugo. Resume data lives in `data/content.yaml`, the layout is built from partials, and the styling stays in theme SCSS with no framework or runtime JavaScript.

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

Next, edit `data/content.yaml` to your liking. The bundled example file shows the full data shape, including optional `opensource`, `skills`, and `publications` sections. Contact link icons map directly to [Tabler Icons](https://tabler.io/icons) webfont class names.

Set `profile.photo` to a static path like `/img/profile.png`, or omit it and provide `assets/images/profile.png` instead.

To preview the bundled example site from this repository:

```shell
cd exampleSite
hugo server --themesDir ../.. --theme jimschubert-resume
```

## Parameters

The following parameters can be defined to customize the theme:

| Param                  | Default      |
|------------------------|--------------|
| accentColor            | `#1D9E75`    |
| backgroundColor        | `#f4f4f2`    |
| surfaceColor           | `#ffffff`    |
| surfaceMutedColor      | `#fafaf8`    |
| textColor              | `#1a1a18`    |
| mutedColor             | `#6b6b67`    |
| borderColor            | `#e0e0dc`    |
| sidebarBackgroundColor | `#2C2C2A`    |
| sidebarDividerColor    | `#444441`    |
| sidebarHeaderColor     | `#5F5E5A`    |
| sidebarIconColor       | `sidebarHeaderColor` |
| sidebarMutedColor      | `#888780`    |
| sidebarBodyColor       | `#B4B2A9`    |
| sidebarEmphasisColor   | `#D3D1C7`    |
| sidebarStrongColor     | `#F1EFE8`    |
| tagBackground          | `#3A3A38`    |
| pageWidth              | `60rem`      |
| sidebarWidth           | `13.75rem`   |
| canonicalURL           | unset        |
| educationMain          | `false`      |

For example, if using YAML for your hugo config (be sure to wrap CSS colors in quotes!):

```yaml
params:
  educationMain: true
  accentColor: '#0f766e'
  backgroundColor: '#f0fdfa'
  sidebarBackgroundColor: '#1f2937'
```

If you want a cooler gray sidebar instead of the default warmer/mocha palette, try:

```yaml
params:
  sidebarBackgroundColor: "#26282b"
  sidebarDividerColor: "#3a3d42"
  sidebarHeaderColor: "#6f767e"
  sidebarMutedColor: "#8f969e"
  sidebarBodyColor: "#bcc2c8"
  sidebarEmphasisColor: "#d8dde2"
  sidebarStrongColor: "#eef2f5"
  tagBackground: "#32353a"
```

`themeColor`, `textColorSecondary`, and `divider` are still recognized as fallbacks for older configs, but the v2 color parameters are the preferred interface.

The following parameters can be nested under `params.titles` to customize the titles displayed on the resume:

| Param         | Default               |
|---------------|-----------------------|
| contact       | `Contact`             |
| education     | `Education`           |
| experiences   | `Experience`          |
| opensource    | `Open source`         |
| publications  | `Publications`        |
| skills        | `Skills`              |
| languages     | `Languages`           |
| interests     | `Interests`           |
| summary       | `Summary`             |

For example, if using YAML for your hugo config you could modify `skills` and `experiences` like this:

```yaml
params:
  titles:
    skills: Knowledge
    experiences: Job History
```

Skill items accept `name`, `level`, and optional `percent`. The bar width uses `percent` when present, falls back to legacy `rank`, and otherwise maps common labels like `Expert`, `Advanced`, `Proficient`, and `Working knowledge` to sensible defaults.

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


## Upgrading from v1

The v2 theme uses a different `data/content.yaml` shape than the original Orbit-based theme. If your v1 resume looks like the old schema used in [`jimschubert.dev`](https://github.com/jimschubert/jimschubert.dev/blob/master/data/content.yaml), you can automate most of the upgrade with [`yq` v4](https://mikefarah.gitbook.io/yq/).

Make a backup first, then run:

```shell
cp data/content.yaml data/content.v1.yaml

yq -oy '
  def strip_markdown:
    sub("^\\*\\*"; "") | sub("\\*\\*$"; "") | sub("^\\*"; "") | sub("\\*$"; "") | sub("^_"; "") | sub("_$"; "");

  def to_tabler_icon:
    if .fontawesome == "fa-github" then "ti-brand-github"
    elif .fontawesome == "fa-stack-overflow" then "ti-brand-stackoverflow"
    elif .fontawesome == "fa-globe" then "ti-world"
    elif .fontawesome == "fa-linkedin" then "ti-brand-linkedin"
    elif .fontawesome == "fa-twitter" then "ti-brand-twitter"
    elif .fontawesome == "fa-envelope" then "ti-mail"
    elif .fontawesome == "fa-phone" then "ti-phone"
    elif .classes == "github" then "ti-brand-github"
    elif .classes == "website" then "ti-link"
    else "ti-link"
    end;

  {
    profile: {
      name: .full_name,
      title: (.tagline // .title),
      email: .email,
      links: ((.links // []) | map({
        icon: to_tabler_icon,
        label: .title,
        url: .href
      }))
    },
    meta_description: .meta_description,
    summary: .summary,
    experiences: ((.experience // []) | map({
      title: .title,
      company: .company,
      location: (if .location == null then null else (.location | strip_markdown) end),
      start: .from,
      end: (if .to == null or .to == "" then null else .to end),
      highlights: (.bullets // [])
    })),
    opensource: ((.projects.list // []) | map({
      name: .title,
      description: .description,
      url: .href
    })),
    education: ((.education // []) | map({
      degree: .degree,
      institution: .institution,
      concentration: (if .major == null then null else (.major | strip_markdown) end),
      gpa: (if .gpa == null then null else (.gpa | tostring) end),
      date: .graduation_date,
      honors: .honors
    })),
    languages: ((.languages // []) | map({
      name: .name,
      level: ((.proficiency // "") | strip_markdown)
    })),
    interests: .interests,
    skills: ((.skills.list // []) | map({
      name: .name,
      percent: .rank,
      level: (
        if .rank >= 95 then "Expert"
        elif .rank >= 85 then "Advanced"
        elif .rank >= 70 then "Proficient"
        else "Working knowledge"
        end
      )
    })),
    publications: (((.publications.list // []) + (.written // [])) | map({
      title: .title,
      date: .date,
      url: (.url // .href),
      description: (.description // .summary)
    }))
  }
' data/content.v1.yaml > data/content.yaml
```

After running the migration:

1. Add `profile.photo: /img/profile.png` if you use a static image path, or place `assets/images/profile.png` in your site and omit `profile.photo`.
2. Review the generated contact icons. The script maps common Font Awesome values to Tabler automatically, but unknown icons fall back to `ti-link`.
3. Review the generated `skills[].level` values. The old percentage-based `rank` field is also copied into `skills[].percent` so the proficiency bar keeps its original width.
4. Replace any old Orbit-era color params in your Hugo config with the v2 params documented below.

# License

This theme is [licensed](./LICENSE) under Apache 2.0.
