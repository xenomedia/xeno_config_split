# Xeno Media Configuration Split

## Background

Xeno Media builds tons of sites. This module allows all our sites to quickly
create a unified config split accross all our sites.

## Installation

Install the module:

```
composer require drupal/config_split
composer require drupal/config_ignore
composer require xenomedia/xeno_config_split
```

Create/update `drush/policy.drush.inc` file:

```php
<?php

/**
 * @file
 * Drush policies.
 */

/**
 * Implementation of drush_hook_COMMAND_validate().
 */
function drush_policy_config_import_validate($source = NULL, $destination = NULL) {
  // Run error if someone tries to run config-import instead of
  // config-split-import.
  return drush_set_error(dt('Per policy.drush.inc, you should run drush csim instead of drush cim.'));
}

/**
 * Implementation of drush_hook_COMMAND_validate().
 */
function drush_policy_config_export_validate($source = NULL, $destination = NULL) {
  // Run error if someone tries to run config-import instead of
  // config-split-import.
  return drush_set_error(dt('Per policy.drush.inc, you should run drush csex instead of drush cex.'));
}
```

Create split directories:

```bash
mkdir -p config/dev
touch config/dev/.keep
mkdir -p config/stage
touch config/stage/.keep
mkdir -p config/prod
touch config/mkdir -p config/prod/.keep
```

In your settings.php update/set your config directory to:

```php
$config_directories[CONFIG_SYNC_DIRECTORY] = '../config/sync';
```

On Your local or development server add the following to your
settings.local.php or settings.dev.php file:

```php
// Config split settings for development.
$config['config_split.config_split.production']['status'] = FALSE;
$config['config_split.config_split.staging']['status'] = FALSE;
$config['config_split.config_split.development']['status'] = TRUE;
```

If you are using Pantheon you can add the following to your settings.php file.

```php
if (isset($_ENV['PANTHEON_ENVIRONMENT'])) {
  // Live Pantheon environment.
  if ($_ENV['PANTHEON_ENVIRONMENT'] == 'live') {
    $config['config_split.config_split.production']['status'] = TRUE;
    $config['config_split.config_split.development']['status'] = FALSE;
    $config['config_split.config_split.staging']['status'] = FALSE;
  }
  // Dev / Test / Multi Branch Pantheon environment.
  else {
    $config['config_split.config_split.production']['status'] = FALSE;
    $config['config_split.config_split.development']['status'] = FALSE;
    $config['config_split.config_split.staging']['status'] = TRUE;
  }
}
else {
  $config['config_split.config_split.production']['status'] = FALSE;
  $config['config_split.config_split.development']['status'] = TRUE;
  $config['config_split.config_split.staging']['status'] = FALSE;
}
```

If you are not on pantheon add the following to your settings.local.php file
on your production site.

```php
// Config split settings for development.
$config['config_split.config_split.production']['status'] = TRUE;
$config['config_split.config_split.staging']['status'] = FALSE;
$config['config_split.config_split.development']['status'] = FALSE;
```

## Post-installation

This module is only meant as a starting point. Once installed you should
uninstall the module.

```bash
drush pm-uninstall xeno_config_split
composer remove xenomedia/xeno_config_split
```

There may be cases that you don't need a staging and development split since
they may be exactly the same. In that case you can just delete one of them.


## How to use it

See [Configuration Split](https://www.drupal.org/docs/8/modules/configuration-split) for full documentation.

In short, update your settings.local.php for the environment you want to make
changes to.

### Example: Add a module so it is only installed on production

Update your settings.local.php so that only `production` is `TRUE` and the
others are set to `FALSE`.

Run `drush csim -y`

Select the module in the Complete Split select.

Run `drush csex -y`

### Example: Ignore Webform settings

Navigate to `/admin/config/development/configuration/ignore`

Add `webform.webform.*` to text area

Save Configuration

Run `drush csex -y`
