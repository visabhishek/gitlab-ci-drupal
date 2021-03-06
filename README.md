# Gitlab CI with Drupal 8

[![pipeline status master](https://gitlab.com/mog33/gitlab-ci-drupal/badges/master/pipeline.svg)](https://gitlab.com/mog33/gitlab-ci-drupal/commits/master)
[![pipeline status testing](https://gitlab.com/mog33/gitlab-ci-drupal/badges/master/pipeline.svg)](https://gitlab.com/mog33/gitlab-ci-drupal/commits/testing)
[![pipeline status master](https://gitlab.com/mog33/gitlab-ci-drupal/badges/master/pipeline.svg)](https://gitlab.com/mog33/gitlab-ci-drupal/commits/master)

<img src="https://www.drupal.org/sites/all/themes/drupalorg_themes/blueprint/images/logo-d8.svg"  width="120" height="120"> +
<img src="https://about.gitlab.com/images/ci/gitlab-ci-cd-logo_2x.png"  width="120" height="120">

[Gitlab CI](https://docs.gitlab.com/ee/ci/README.html) for a [Drupal 8](https://www.drupal.org) project. Include **Build**,
**Unit testing**, **Code quality**, **metrics** and **deploy** samples.

A lot of help and inspiration from those wonderful projects:

- [https://github.com/AcroMedia/commerce-demo](https://github.com/AcroMedia/commerce-demo)
- [https://github.com/Lullabot/drupal8ci](https://github.com/Lullabot/drupal8ci)
- [https://gitlab.com/Lullabot/d8cidemo/tree/gitlab](https://gitlab.com/Lullabot/d8cidemo/tree/gitlab)
- [https://github.com/manumilou/gitlab-ci-example-drupal](https://github.com/manumilou/gitlab-ci-example-drupal)

**Table of contents**

- [Prerequisites](#prerequisites)
- [Quick how to](#quick-how-to)
- [Basic usage](#basic-usage)
  - [Drupal 8 code](#drupal-8-code)
  - [Rules for linting / Code standards / QA](#rules-for-linting--code-standards--qa)
  - [PHPunit tests for Drupal 8](#phpunit-tests-for-drupal-8)
- [Workflow proposed](#workflow-proposed)
  - [Branch master](#branch-master)
  - [Branch testing](#branch-testing)
- [Included tools](#included-tools)
- [Running the jobs locally with Docker](#running-the-jobs-locally-with-docker)
- [Openstack runner](#openstack-runner)
- [Testing your jobs with gitlab-runner](#testing-your-jobs-with-gitlab-runner)
- [Advanced usage](#advanced-usage)

## Prerequisites

- Gitlab CI with a [runner that support docker](https://docs.gitlab.com/runner/)
- Minimal understanding of [Gitlab CI](https://about.gitlab.com/features/gitlab-ci-cd/) and [Gitlab CI Yaml](https://docs.gitlab.com/ee/ci/yaml)

## Quick how to

Copy those files in the root of your Drupal project (same level as `web/` folder):

```shell
.gitlab-ci/
.eslintignore
.phpmd.xml
.phpqa.yml
.sass-lint.yml
gitlab-ci.yml
RoboFile.php
```

Put some code in you Drupal `modules/custom` and `themes/custom` folders or use
included demo code in `web/`
[Run a pipeline from Gitlab UI](https://docs.gitlab.com/ee/ci/pipelines.html#manually-executing-pipelines) or push to master!

## Basic usage

**Note**: The `.gitlab-ci.yml` file is a bit big, this is meant to be a starting
point for working jobs with [Drupal 8](https://www.drupal.org), feel free to
cherry pick what you need but be careful about dependencies between some jobs.

If your commit message contains **[ci skip]** or **[skip ci]**, using any
capitalization, the commit will be created but the pipeline will be skipped.

### Drupal 8 code

There is currently 2 ways to use this Gitlab-ci sample, there is some tags in the
`.gitlab-ci.yml` for sections to edit.

For any scenario you need to configure / adapt or delete the deploy part, search
`[DEPLOY]` sections in `.gitlab-ci.yml`.

#### (Default) No composer.json file and base project is Drupal composer template

You are using this project side of your custom modules / themes **WITHOUT** a
composer.json file for Drupal. Then the ci process will install latest version of
[Drupal composer template project](https://github.com/drupal-composer/drupal-project)
for you.

This is a good way to only test your custom code or theme.

- Push something to master, create a _testing_ branch, check the CI pipeline!

#### Included composer.json file for your project

You are using this project side of your _custom modules / themes / profile_
**WITH** a composer.json file, preferably based on [Drupal composer template project](https://github.com/drupal-composer/drupal-project)
but can work with other distributions.

- You need to switch the CI action that manage the installation of Drupal search
  `[COMPOSER.JSON]` sections.
- Optional: Set properly `WEB_ROOT` if not `./web/`

### Rules for linting / Code standards / QA

All rules match mostly a Drupal 8 project.

To adapt some rules, first look at `.phpqa.yml`, `.phpmd.xml` and `.sass-lint.yml` files with `.gitlab-ci.yml`.

More options see:

- [Phpqa configuration](https://github.com/EdgedesignCZ/phpqa#advanced-configuration---phpqayml)
- [Phpqa .phpqa.yml](https://github.com/EdgedesignCZ/phpqa/blob/master/.phpqa.yml)

Eslint is based on the official [Drupal 8 eslintrc.passing.json](https://git.drupalcode.org/project/drupal/raw/HEAD/core/.eslintrc.passing.json)

Stylelint is based on the official [Drupal 8 stylelintrc.json](https://git.drupalcode.org/project/drupal/raw/HEAD/core/.stylelintrc.json)

[Sass-lint](./.sass-lint.yml) is based on [Wolox](https://github.com/Wolox/frontend-bootstrap/blob/master/.sass-lint.yml)

### PHPunit tests for Drupal 8

The pipeline in this project support Unit, Kernel, Functional, [Functional Javascript](https://www.drupal.org/docs/8/phpunit/phpunit-javascript-testing-tutorial)
tests in Drupal 8, see [Type of tests in Drupal 8](https://www.drupal.org/docs/8/testing/types-of-tests-in-drupal-8).

For Drupal 8 (since 8.6) [Nightwatch.js](https://www.drupal.org/docs/8/testing/javascript-testing-using-nightwatch)
is working.

## Workflow proposed

Sample workflow used in this file, based on Git branches or tags.

By default a new tag or branch _testing_ trigger the build, unit tests,
security, qa, lint, manual deploy to test.
A branch _master_ trigger qa, lint, manual deploy

You can adapt _only_ and _except_ for your own workflow, see
[Gitlab documentation](https://docs.gitlab.com/ee/ci/yaml/#only-and-except-simplified)

### Branch master

[![pipeline_master](https://gitlab.com/mog33/gitlab-ci-drupal/uploads/d2fdb3c36bb89243d4d763290bfef77c/pipeline_master.png)](https://gitlab.com/mog33/gitlab-ci-drupal/pipelines/47581470)

### Branch testing

[![pipeline_testing](https://gitlab.com/mog33/gitlab-ci-drupal/uploads/612e6445d2e4af2e8235d8515fdada08/pipeline_testing.png)](https://gitlab.com/mog33/gitlab-ci-drupal/pipelines/47487091)

## Included tools

All tools are included in a specific [docker image](https://gitlab.com/mog33/drupal8ci).

Nothing could be done without a bunch of awesome humans building awesome tools.

- [Robo](https://robo.li)
- [Eslint](https://eslint.org/)
- [Sass-lint](https://github.com/sasstools/sass-lint)
- [Stylelint](https://github.com/stylelint/stylelint)
- [Nightwatch.js](https://www.drupal.org/docs/8/testing/javascript-testing-using-nightwatch)
- [PHPunit](https://phpunit.de)

Code quality is done using the wonderful Phpqa, a tool that integrate other Php
quality and analysis tools:

- [Phpqa](https://github.com/EdgedesignCZ/phpqa)
  - [Phploc](https://github.com/sebastianbergmann/phploc)
  - [Phpcpd](https://github.com/sebastianbergmann/phpcpd)
  - [PHP_CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer)
  - [Phpmd](https://github.com/phpmd/phpmd)
  - [PHP-Parallel-Lint](https://github.com/JakubOnderka/PHP-Parallel-Lint)
  - [Security-checker](https://github.com/sensiolabs/security-checker)
  - [Pdepend](https://pdepend.org/)
  - [Phpmetrics](https://www.phpmetrics.org)

## Running the jobs locally with Docker

You can perform most of the tests locally (on `*Unix`) without installing any tool or Drupal code using included [docker-compose.yml](docker-compose.yml) file in this project, require:

- [Docker engine 18+](https://docs.docker.com/install)
- [Docker compose 1.23+](https://docs.docker.com/compose/install)

Default scenario is to have only your custom code modules / themes in a `/web` folder, from there run

```bash
docker-compose up -d
```

If you include your own `composer.json` file to include _Drupal_, you must first install the Drupal code (`composer install`) and edit the [docker-compose.yml](docker-compose.yml) to use an image without Drupal and fix the code paths.

An helper bash script can help you run the tests using docker, this is a copy
of the jobs from the [.gitlab-ci.yml](.gitlab-ci.yml) file.

The script will copy configuration files from this folder and ensure folders to run the tests properly.

```bash
test/run-tests-ci-locally.sh all
```

## Openstack runner

If you have access to _openstack_ you can use the cloud configuration script in `openstack/` to quickly set-up a VM for a Gitlab runner.

Get your runner token on Gitlab (_>> Settings >> CI / CD >> Runners settings_)

Create the instance on openstack, ssh and run:

```bash
sudo gitlab-runner register -n \
  --executor docker \
  --description "My first runner" \
  --docker-image "docker:stable" \
  --docker-privileged \
  --url https://MY_GITLAB_URL \
  --registration-token YOUR_RUNNER_TOKEN_ON_GITLAB
```

## Testing your jobs with gitlab-runner

If you have access to a runner, you can run a single job (if don't need a build)

```bash
sudo gitlab-runner exec docker 'code quality'
```

More information on the documentation:

- [Gitlab-runner-exec](https://docs.gitlab.com/runner/commands/#gitlab-runner-exec)

In the same time using the variable `CI_DEBUG_TRACE` in any job can help you.

## Advanced usage

If you want to test your Drupal from an existing configuration, you have to create a
`./config/sync` folder with your configuration and set _SETUP_FROM_CONFIG_ in `.gitlab-ci.yml`.

----

Want some help implementing this on your project? I provide Drupal 8 expertise as a freelance, just [contact me](https://developpeur-drupal.com/en).
