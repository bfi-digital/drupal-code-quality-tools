# Drupal Code Quality Tools

This project utilises [Drupal's Composer Scaffold][dcs] plugin for installing
commonly used code quality tool dependencies and placing configuration files
in desired locations inside Drupal project directories.

Once installed, this project will:

1. Copy a default [Lefthook][lh] configuration file to the project root;
2. Install [PHPStan][phpstan] and [PHP_CodeSniffer][phpcs], and place the
   relevant configuration files in the project root;
3. Update the `.gitignore` file to ignore copied scaffold files.

[dcs]: https://www.drupal.org/docs/develop/using-composer/using-drupals-composer-scaffold
[lh]: https://lefthook.dev
[phpstan]: https://phpstan.org
[phpcs]: https://github.com/PHPCSStandards/PHP_CodeSniffer/

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

Finally, require this package by running:

```shell
composer require bfi-digital/drupal-code-quality-tools --dev
```

When requested, enter your Personal Access Token.

This package will install the following packages, so you can remove them from
your dev-dependencies:

1. `dealerdirect/phpcodesniffer-composer-installer`
2. `drupal/coder`
3. `mglaman/phpstan-drupal`
4. `phpstan/extension-installer`
5. `phpstan/phpstan`
6. `phpstan/phpstan-deprecation-rules`

## Installed tools

### PHPStan

PHPStan static analysis tool is installed. The scaffold also provides an initial
configuration file (`phpstan.neon`), which is basically a config that includes
your project's `phpstan.neon.dist` file.

### PHP_CS

PHP_CodeSniffer, and fixer tool is installed, along with the `drupal/coder`
module, which provides [Drupal specific standards][drupal-coder]. The scaffold
provides a default `phpcs.xml` file.

[drupal-coder]: https://www.drupal.org/project/coder

## Lefthook for managing Git Hooks

A config file (`lefthook.yaml`) for [Lefthook][lh] is copied to your project's
root, and added to the `.gitignore` file. This file basically includes a
`lefthook-project.yaml`. Feel free to configure your Lefthook hooks in that
file.
