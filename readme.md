# Twitter Bootstrap plugin for CakePHP 3.X

This plugin includes both [lessjs](http://lesscss.org/#client-side-usage-browser-options) and [less.php](https://github.com/oyejorge/less.php#lessphp) compilers and allows you to easilly deploy applications using twitter bootstrap.

With a component and some helpers it automatically replaces cakePHP's elements like form inputs and flash messages to be displayed with twitter bootstrap.

## Dependencies

- [oyejorge/less.php](https://github.com/oyejorge/less.php)

## Installation

After adding the plugin remember to load it in the bootstrap file:

```php
Plugin::load('Bootstrap');
```

### Using composer

As this plugin is not in packagist (yet) you'll need to manually add the repository url to your `composer.json` file:

```json
{
    "repositories": [
        {
            "type": "vcs",
            "url": "http://gitlab.casajuana.eu/cms/bootstrap.git"
        }
    ],
    "require": {
        "cms/bootstrap": "3.0.*"
    }
}
```

And...

```bash
composer update
```

### As a git submodule

```bash
git submodule add http://gitlab.casajuana.eu/cms/bootstrap.git plugins/Bootstrap
git submodule add https://github.com/oyejorge/less.php.git vendor/oyejorge/less.php
```

## Configuration

After adding the plugin you can add the desired utilities:

```php
// AppController.php
public $components = [
    'Bootstrap.Bootstrap'
];

public $helpers = [
    'Bootstrap.Less',
    'Bootstrap.Form'
];
```

Next, **create a `styles.less`** file on your `webroot/less` folder (also create that folder!) containing this line:

```less
@import '/bootstrap/less/bootstrap.less';
```

Finally, you can use the template included with this plugin as a theme (for previewing):

```
// AppController
public function beforeFilter(Event $event)
{
    $this->theme = 'Bootstrap';
}
```

But I suppose you want your custom template. To do so, simply use the [LessHelper](#LessHelper):

```php
echo $this->Less->less('styles.less');
```

And that's it :)

## Utilities

### Component

The **BootstrapComponent** replaces all flash messages (from session key `Flash.Flash`) to automatically load the bootstrap flash message template located at `src/Template/Element/flash.ctp`.

### Helpers

#### LessHelper

Used on your template or view to load the compressed CSS.

By default it will compress files using the php parser with cache enabled. This will fill your css folder with a bunch of files starting with `lessphp_` used for the cache. I recommend you adding these files to your `.gitignore` file in order to prevent commiting them:

    lessphp_*

Basically, you give the helper a less file to be loaded (from `/less` directory) and it returns the html link tag to the compiled CSS:

```php
echo $this->Less->less('styles.less');
// will result in something like...
<link rel="stylesheet" href="/css/lessphp_8e07b9484a24787e27f9d71522ba53443d18bbd2.css" />
```

You can compile multiple files if you pass an array:

```php
echo $this->Less->less(['myreset.less', styles.less']);
// They will be compiled in the same file, so the result will be the same as the previous one
<link rel="stylesheet" href="/css/lessphp_e0ce907005730c33ca6ae810d15f57a4df76d330.css"/>
```

And you can pass any option to both lessjs and less.php parsers:

```php
echo $this->Less->less('styles.less', [
    'js' => [
        // options for lessjs (will be converted to a json object)
    ],
    'parser' => [
        // options for less.php parser
    ],
    // The helper also has its own options
]);
```

If you want to use the less.js parser directly, instead of a fallback, or you want to use the [#!watch](http://lesscss.org/usage/#using-less-in-the-browser-watch-mode) method, you can do it so by setting the js parser to development:

```php
echo $this->Less->less('styles.less', ['js' => ['env' => 'development']]);
```

This will output all the links to the less files and the needed js files to parse the content only using the less.js parser.

##### LessHelper Options

Beside the options for [lessjs](http://lesscss.org/#client-side-usage-browser-options) and [less.php](https://github.com/oyejorge/less.php#lessphp) parsers you can set three options to the helper:

+ `cache`: default's to true. If disabled, the output will be raw css wrapped with `<style>` tags.
+ `tag`: default's to true. Whether or not return the code with its proper tag.
+ `less`: default's to `/bootstrap/js/less.min`. You can use this var to set a custom lessjs file.

```php
// Get the link to the resulting file after compressing
$css_link = $this->Less->less('styles.less', [
    'tag'   => false
]);

// Get the compiled CSS (raw)
$compiled_css = $this->Less->less('styles.less', [
    'cache' => false,
    'tag'   => false
]);
```

#### FormHelper

Automatically adds some css classes to your form elements. Some of the input replacements can be found at `src/Config/forms.php`, but many need to be done directly from the FormHelper.

The elements that automatically have their classes loaded are: inputs, labels and buttons*.

> \* Buttons always have the `.btn` class added. If you want to remove the class you can pass an additional `btnClass` param set to `false` to the button's `$options`.

## License

    Copyright 2013-2014 Òscar Casajuana (a.k.a. elboletaire)

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

       http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    imitations under the License.
