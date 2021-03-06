```
__________.__          ___________.__  .__       .__        
\______   \  |__ ______\_   _____/|  | |__|__  __|__|______ 
 |     ___/  |  \\____ \|    __)_ |  | |  \  \/  /  \_  __ \
 |    |   |   Y  \  |_> >        \|  |_|  |>    <|  ||  | \/
 |____|   |___|  /   __/_______  /|____/__/__/\_ \__||__|   
               \/|__|          \/               \/          
```


Provides a replacement of the Node.js based elixir pre-packaged with Laravel Framework. Tasks are sequentially run in the order that they are declared in the configuration file. You can specify more than one task module block.

[![Latest Stable Version](https://poser.pugx.org/bluora/laravel-php-elixir/v/stable.svg)](https://packagist.org/packages/bluora/laravel-php-elixir) [![Total Downloads](https://poser.pugx.org/bluora/laravel-php-elixir/downloads.svg)](https://packagist.org/packages/bluora/laravel-php-elixir) [![Latest Unstable Version](https://poser.pugx.org/bluora/laravel-php-elixir/v/unstable.svg)](https://packagist.org/packages/bluora/laravel-php-elixir) [![Built for Laravel](https://img.shields.io/badge/Built_for-Laravel-green.svg)](https://laravel.com/) [![License](https://poser.pugx.org/bluora/laravel-php-elixir/license.svg)](https://packagist.org/packages/bluora/laravel-php-elixir)

[![Build Status](https://travis-ci.org/bluora/laravel-php-elixir.svg?branch=master)](https://travis-ci.org/bluora/laravel-php-elixir) [![StyleCI](https://styleci.io/repos/69619219/shield?branch=master)](https://styleci.io/repos/69619219) [![Test Coverage](https://codeclimate.com/github/bluora/laravel-php-elixir/badges/coverage.svg)](https://codeclimate.com/github/bluora/laravel-php-elixir/coverage) [![Issue Count](https://codeclimate.com/github/bluora/laravel-php-elixir/badges/issue_count.svg)](https://codeclimate.com/github/bluora/laravel-php-elixir) [![Code Climate](https://codeclimate.com/github/bluora/laravel-php-elixir/badges/gpa.svg)](https://codeclimate.com/github/bluora/laravel-php-elixir) 

This package has been developed by H&H|Digital, an Australian botique developer. Visit us at [hnh.digital](http://hnh.digital).

## Pre-install requirement

This package requires ext-inotify which can be installed by:

`$ pecl install inotify`

You will likely need to enable this extension. You can use the following (may not match your specific OS settings).

`$ echo "extension=inotify.so" | sudo tee /etc/php/7.1/mods-available/inotify.ini && sudo ln -s /etc/php/7.1/mods-available/inotify.ini /etc/php/7.1/cli/conf.d/20-inotify.ini`

## Install

Via composer:

`$ composer require-dev bluora/laravel-php-elixir ~2.0`

Enable the service provider by editing config/app.php:

```php
    'providers' => [
        ...
        Bluora\PhpElixir\ServiceProvider::class,
        ...
    ];
```

## Usage

Run the command with the default config file (.elixir.yml).

`$ php artisan elixir`

Run the command with a custom config file:

`$ php artisan elixir --config=***`

Run the folder watcher to automatically run elixir on file changes with the default config file (.elixir.yml).

`$ php artisan elixir:watch`

Run the folder watcher to automatically run elixir on file changes using a custom config file:

`$ php artisan elixir:watch --config=***`

## Configuration

Configuration for this package is done in the '.elixir.yml' located in your base directory or in the 'vendor/bluora/laravel-php-elixir/src/.elixir.yml.example'.

First declare the task name and the class name in the configuration file (these modules are installed by default).

```yaml
modules:
    combine:
        - Bluora\PhpElixirCombine\CombineModule
    copy:
        - Bluora\PhpElixirCopy\CopyModule
    empty:
        - Bluora\PhpElixirEmpty\EmptyModule
    exec:
        - Bluora\PhpElixirExec\ExecModule
    replace:
        - Bluora\PhpElixirReplace\ReplaceModule
    revision:
        - Bluora\PhpElixirRevision\RevisionModule
    sass:
        - Bluora\PhpElixirSass\SassModule
```

List tasks using the first level in the YAML file or, if you need to declare more than one task module in different location of the config file, simply prepend the task with a number (unique to the task) and a hash. For example:

```yaml
copy:
    xxx: yyy
1#copy:
    zzz: aaa
```

If you are testing php-elixir, you can stop tasks from running by prepending them with an exclaimation mark (!).

```yaml
!copy:
    xxx: yyy
```

### Options

* dry-run - runs the script but doesn't actually do anything.
* verbose - provides further feedback of what is happening.

```yaml
options:
    dry-run: true
    verbose: true
```
### Paths

Paths lets you declare path constants that can be used in your other tasks.

```yaml
paths:
    PATH_SASS: resources/assets/sass
    PATH_BOWER: bower_components
    PATH_PUBLIC_ASSETS: public/assets
    PATH_PUBLIC_BUILD: public/build
    PATH_RESOURCES: resources
    PATH_RES_ASSET_IMAGES: resources/assets/images
```

### Watch

Watch configuruation item is only used by the elixir:watch console command.

```yaml
watch:
    - PATH_RESOURCES?filter=!php
```

### Empty

Deletes all files and folders in the listed paths.

```yaml
empty:
    - PATH_PUBLIC_ASSETS
    - PATH_PUBLIC_BUILD
```

### Exec

Executes a given file and arguments.

```yaml
exec:
    php: artisan inspire
```

### SASS

Processes and compiles a *.scss file and outputs it to specified path.

Formatted as: {SOURCE_FILE_PATH}: {DESTINATION_FILE_PATH}

```yaml
sass:
    PATH_SASS + /app.scss: PATH_PUBLIC_ASSETS + /vendor/app.css
```

### Combine

Gets the contents of one or many files and combines it into the specified file.

Source folders can be specified with single level or multi-level lookup, and the ability to filter files by extension.

```
{DESTINATION_FILE}:
    - {SOURCE_FILE}
    - {SOURCE_FOLDER}
```

```yaml
combine:
    PATH_PUBLIC_ASSETS + /vendor/jquery-combined.min.js:
        - PATH_PUBLIC_ASSETS + /vendor/jquery/jquery.min.js
        - PATH_PUBLIC_ASSETS + /vendor/jquery-ui/jquery-ui.min.js
    PATH_PUBLIC_ASSETS + /vendor/combined.js:
        - PATH_PUBLIC_ASSETS + /vendor/**?filter=js

```

### Copy

Copies files from a file path or a folder path to a specified folder or file name.

Folder paths can be configured to get the top level directory using '/*' or for all files and folders in path by using '/**'.

Further configuration can be added using the standard query string format.

* filter - comma deliminated list of extensions.

```yaml
copy:
    PATH_BOWER + /jquery/dist/jquery.min.js: PATH_PUBLIC_ASSETS + /vendor/jquery/
    PATH_RES_ASSET_IMAGES + /**?filter=png: PATH_PUBLIC_ASSETS + /images/
```

### Replace

You can replace specific text in files or folder paths.

```yaml
replace:
    PATH_PUBLIC_ASSETS + /vendor/vendor_name/styles.css:
        - ../img
        - vendor/vendor_name
```

### Revision

Provides revisioning of files in a specified folder location.

Options that are available:

* hash_length - defaults is 8.
* minify - default is false.
* php_manifest - generates a php equivalent of the json revision file.

```
{SOURCE_FOLDER}:
    - {DESTINATION_FOLDER}
    - {REVISION_MANIFEST_FILE}
    - {QUERY_STRING_OPTIONS}
```

```yaml
revision:
    PATH_PUBLIC_ASSETS:
        - PATH_PUBLIC_BUILD
        - PATH_PUBLIC_BUILD + /rev-manifest.json
        - hash_length=12&minify=true&php_manifest=true
```

## Contributing

Please see [CONTRIBUTING](https://github.com/bluora/laravel-php-elixir/blob/master/CONTRIBUTING.md) for details.

## Credits

* [Rocco Howard](https://github.com/therocis)
* [All Contributors](https://github.com/bluora/laravel-php-elixir/contributors)

## License

The MIT License (MIT). Please see [License File](https://github.com/bluora/laravel-php-elixir/blob/master/LICENSE) for more information.
