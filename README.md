# Drupal Code Quality Tools

This project utilises [Drupal's Composer Scaffold][dcs] plugin for installing
commonly used code quality tool dependencies and placing configuration files
in desired locations inside Drupal project directories.

Once installed, this project will:

1. Install [Lefthook][lh] and add a copy of the default configuration file to
   the project root;
2. Install [PHPStan][phpstan] and [PHP_CodeSniffer][phpcs], and place the
   relevant configuration files in the project root;
3. Place a default [PHPUnit][phpunit] configuration file in the project root;
4. Update the `.gitignore` file to ignore copied scaffold files.

Scaffolded files are only copied if they don't already exist at the
destination. If your project already has a `lefthook.yaml`, `phpcs.xml`,
`phpstan.neon`, or `phpunit.xml.dist` file, it will be left untouched.

[dcs]: https://www.drupal.org/docs/develop/using-composer/using-drupals-composer-scaffold
[lh]: https://lefthook.dev
[phpstan]: https://phpstan.org
[phpcs]: https://github.com/PHPCSStandards/PHP_CodeSniffer/
[phpunit]: https://phpunit.de

## Requirements

You should be able to [connect to GitHub using SSH][ssh].

[A `drupal/recommended-project` based Drupal project][drupal-recommended] is
required. Ensure your project has `drupal/core-composer-scaffold` and
`drupal/core-recommended` installed as well.

[ssh]: https://docs.github.com/en/authentication/connecting-to-github-with-ssh
[drupal-recommended]: https://www.drupal.org/docs/develop/using-composer/manage-dependencies#s-create-a-project

## Installation

First, you need to update your project's `composer.json` file.

Run the following to add this repository to your `repositories` section:

```shell
composer config repositories.drupal-code-quality-tools git https://github.com/bfi-digital/drupal-code-quality-tools.git
```

Add an `allowed-packages` key under `extra.drupal-scaffold` section, if it's not
there already, and list this repo, by running:

```shell
composer config --json --merge extra.drupal-scaffold '{"allowed-packages": ["bfi-digital/drupal-code-quality-tools"]}'
```

This package brings in two Composer plugins (`dealerdirect/phpcodesniffer-composer-installer`
and `phpstan/extension-installer`). Since Composer 2.2, plugins must be
explicitly allowed in your project's `composer.json`, or `composer require`
will prompt you (or fail non-interactively). Allow them by running:

```shell
composer config --no-plugins allow-plugins.dealerdirect/phpcodesniffer-composer-installer true
composer config --no-plugins allow-plugins.phpstan/extension-installer true
```

Finally, require this package by running:

```shell
composer require bfi-digital/drupal-code-quality-tools --dev
```

When requested, enter your Personal Access Token.

This package will install the following packages, so you can remove them from
your project's own `require` or `require-dev` sections:

1. `dealerdirect/phpcodesniffer-composer-installer`
2. `drupal/coder`
3. `evilmartians/lefthook`
4. `mglaman/phpstan-drupal`
5. `phpstan/extension-installer`
6. `phpstan/phpstan`
7. `phpstan/phpstan-deprecation-rules`

## Installed tools

### PHPStan

PHPStan static analysis tool is installed. The scaffold also provides an initial
configuration file (`phpstan.neon`), which is basically a config that includes
your project's `phpstan.neon.dist` file. If a `phpstan.neon` file already
exists in your project root, it is left as-is and not overwritten.

### PHP_CS

PHP_CodeSniffer, and fixer tool is installed, along with the `drupal/coder`
module, which provides [Drupal specific standards][drupal-coder]. The scaffold
provides a default `phpcs.xml` file. If a `phpcs.xml` file already exists in
your project root, it is left as-is and not overwritten.

[drupal-coder]: https://www.drupal.org/project/coder

### Lefthook

[Lefthook][lh] is a Git hooks manager that allows you to configure your Git
hooks in a YAML configuration file.

A config file (`lefthook.yaml`) is copied to your project's root, and added to
the `.gitignore` file. This file basically includes a `lefthook-project.yaml`.
Feel free to configure your Lefthook hooks in that file. If a `lefthook.yaml`
file already exists in your project root, it is left as-is and not overwritten.

If you are using containers in your development environment, remember to run
the tools inside the containers. Prepend the commands in your hooks with the
relevant command, such as `ddev exec`.

### PHPUnit

This package does **not** install PHPUnit. The Drupal testing dependencies are
provided by `drupal/core-dev`, which must match your project's version of
`drupal/core-recommended`, so it has to be installed per project:

```shell
composer require --dev drupal/core-dev:^11.2
```

Replace `^11.2` with the version constraint you use for
`drupal/core-recommended`. This package is compatible with `drupal/core-dev`
11.2 and later. Earlier versions require PHPStan 1, which conflicts with the
PHPStan 2 required by this package, so upgrade Drupal core first.

The scaffold provides a default `phpunit.xml.dist` file, which defines `unit`,
`kernel`, `functional` and `functional-javascript` test suites for tests in
your custom modules and themes. If a `phpunit.xml.dist` file already exists in
your project root, it is left as-is and not overwritten.

The environment variables in the default file (`SIMPLETEST_BASE_URL`,
`SIMPLETEST_DB` and `BROWSERTEST_OUTPUT_DIRECTORY`) assume a DDEV setup. If
your environment differs, copy `phpunit.xml.dist` to `phpunit.xml` and adjust
it; PHPUnit uses `phpunit.xml` in preference to `phpunit.xml.dist`.

Run the tests inside your container, for example:

```shell
ddev exec vendor/bin/phpunit --testsuite unit
```

To run the unit tests before pushing, add a hook to your
`lefthook-project.yaml`:

```yaml
pre-push:
  jobs:
    - name: phpunit
      run: ddev exec vendor/bin/phpunit --testsuite unit
```
