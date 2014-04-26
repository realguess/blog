title: Configuration Setup and Loading Precedence
date: 2013-12-04 17:34:59
tags: [configuration, settings, environment]
---

Loading precedence (latter overrides former):

1. Load application default settings (`config/default.yml`)
2. Load environment settings
3. Load custom settings

Default settings of the application is provided by the upstream, therefore, it should not be modified.

There are four types of environments: `development`, `testing`, `staging` and `production`. Environment can be set by using the shell environment variable: `ENV`. If the environment variable is not specified, it is set to `development` as default. Environment settings override default settings.

Custom settings override environment settings. The search path for custom settings is:

1. Environment variable `SETTINGS`
2. `config/settings.yml`

If both paths are not available, that means the custom settings are not specified.

`config/settings.yml` is a custom settings, therefore, it will be ignored by git in `.gitignore` file:

    config/settings.yml

The complete directory structure regarding to the configuration setup:

    config/
    ├── default.yml
    ├── environments
    │   ├── development.yml
    │   ├── production.yml
    │   ├── staging.yml
    │   └── testing.yml
    └── settings.yml

Quick start command:

    $ node app

Fully customized start command:

    $ ENV=production SETTINGS=/path/to/settings.yml node app
