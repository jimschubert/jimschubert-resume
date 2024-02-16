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
| analyticsID        |           |

For example, if using YAML for your hugo config:

```yaml
params:
  educationMain: true
  themeColor: #ececec
```

# License

The Hugo theme itself is [licensed](./LICENSE) under Apache 2.0.

The [Orbit theme's license](https://github.com/xriley/Orbit-Theme?tab=readme-ov-file#author--license) is reproduced below:

> This Bootstrap template is made by UX/UI designer [Xiaoying Riley](https://twitter.com/3rdwave_themes) for developers and is 100% FREE as long as you **keep the footer attribution link**. You do not have the rights to resell, sublicense or redistribute (even for free) the template on its own or as a separate attachment from any of your work.
>
> If you'd like to **use the template without the attribution link**, you can [buy the **commercial license** via the theme website](https://themes.3rdwavemedia.com/bootstrap-templates/resume/orbit-free-resume-cv-bootstrap-theme-for-developers/)
