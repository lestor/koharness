koharness - create clean Kohana containers for testing your modules
===================================================================

koharness is a very simple package to help you provide a clean testing environment for your Kohana
modules. Once enabled, it will generate a basic Kohana application containing your module and any
dependencies, which you can then use as the basis for running tests with your favourite tools.

Unlike the standard Kohana test bootstrappers, koharness allows you to control module loading order,
which may be vital for modules that are designed to extend other modules.

## Adding it as a project dependency

The easiest way to add koharness to your project is with [composer](http://getcomposer.org). Create
a composer.json in the root of your module's folder like so:

```json
{
	"require-dev": {
		"ingenerator/koharness" : "*"
	}
}
```

Ensure your .gitignore file includes the following lines:

```
/vendor
/modules
```

Run `composer install --dev` in your module's root directory.

## Configuring module dependencies

Your module will require at least the Kohana core and the Kohana unittest module to function - and
it may require other modules too. We recommend tracking these dependencies in your composer.json too
- currently the core Kohana repository doesn't define composer packages so you should use the
inGenerator forks:

```json
{
	"require": {
		"kohana/core":     "dev-ingenerator-master",
		"kohana/unittest": "dev-ingenerator-master"
	},
	"repositories": [
		{"type": "vcs", "url": "https://github.com/ingenerator/kohana-core"},
		{"type": "vcs", "url": "https://github.com/ingenerator/kohana-unittest"}
	]
}
```

Alternatively, you could define custom packages in your module's composer.json that track the
specific git revision in the main Kohana repositories.

Or, add a shell script to your project to clone the required repositories and checkouts to your
local disk.

However you get your dependencies to your machine, you then need to configure koharness to include
them in your Kohana installation's active module list. You do this with a koharness.php file in the
repository root:

```php
// {my-module-root}/koharness.php
return array(
	'modules' => array(
		'my-module' => __DIR__,
		'unittest' => __DIR__.'/modules/unittest' // Or any other way you want to specify the path to this module
	),

	// You can specify where to look for Kohana core - the default is {my-module-root}/vendor/kohana/core
	'syspath' => '/some/path/to/kohana/core',

	// You can specify where to create the harness application - the default is /tmp/koharness
	'temp_dir' => '/home/me/testing'
);
```

## Building your harness

To build your harness, from your module root directory just run `vendor/bin/koharness` (presuming
you have left your composer `bin-dir` property at default. This will:

* Wipe out your specified temp directory
* Create a standard generic Kohana directory structure in the temp directory
* Link the kohana core directory to {temp}/system
* Link each module to {temp}/modules/{name}
* Link your module's vendor path to {temp}/vendor
* Customise the generic application bootstrap with your module list and output it to {temp}/application/bootstrap.php

## Running tests

Once you have built your harness, you can run tests using whatever tool you prefer. For example, you
could use phpunit:

```shell
cd /tmp/koharness
vendor/bin/phpunit --bootstrap=modules/unittest/bootstrap.php modules/unittest/tests.php
```

## License

koharness is copyright (c) 2014 Kohana Team and distributed under a [BSD License](LICENSE.md).

It was contributed to the Kohana project by [inGenerator Ltd](http://www.ingenerator.com).