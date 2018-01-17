# Server side rendering JavaScript in your Laravel application

[![Latest Version on Packagist](https://img.shields.io/packagist/v/spatie/laravel-server-side-rendering.svg?style=flat-square)](https://packagist.org/packages/spatie/laravel-server-side-rendering)
[![Build Status](https://img.shields.io/travis/spatie/laravel-server-side-rendering/master.svg?style=flat-square)](https://travis-ci.org/spatie/laravel-server-side-rendering)
[![SensioLabsInsight](https://img.shields.io/sensiolabs/i/xxxxxxxxx.svg?style=flat-square)](https://insight.sensiolabs.com/projects/xxxxxxxxx)
[![Quality Score](https://img.shields.io/scrutinizer/g/spatie/laravel-server-side-rendering.svg?style=flat-square)](https://scrutinizer-ci.com/g/spatie/laravel-server-side-rendering)
[![Total Downloads](https://img.shields.io/packagist/dt/spatie/laravel-server-side-rendering.svg?style=flat-square)](https://packagist.org/packages/spatie/laravel-server-side-rendering)

Making server side rendering a bit less hard in Laravel.

```blade
<html>
    <head>My server side rendered app</head>
    <body>
        {!! ssr('js/app.js') !!}
    </body>
</html>
```

This package is a Laravel bridge for the [spatie/server-side-rendering](https://github.com/spatie/server-side-rendering) library. Before getting started, dig through the readme to learn about the underlying concepts and caveats.

Vue and React example apps are available at [spatie/server-side-rendering-example](https://github.com/spatie/server-side-rendering-example) if you want to see it in action.

## Installation

You can install the package via composer:

```bash
composer require spatie/laravel-server-side-rendering
```

The service provider and `Ssr` alias will be automatically registered.

You can optionally publish the config file if you want to tweak things.

```bash
php artisan vendor:publish --provider="Spatie\Ssr\SsrServiceProvider" --tag="config"
```

## Usage

### Prerequisites

First you'll need to pick an engine to execute your scripts. The server-side-rendering library ships with V8 and Node engines. By default, the package is configured to use node, since you probably already have that installed on your system.

Set up the `NODE_PATH` environment variable to get started:

```
NODE_PATH=/path/to/my/node
```

You'll also need to ensure that a `storage/app/ssr` folder exists, or change the `ssr.node.temp_path` config value to something else.

If you'd rather use the V8 engine, you can skip the previous two steps. You'll need to have the [v8js extension](https://github.com/phpv8/v8js) installed though.

### Configuration

Besides the above, no configuration's required. If you need to tweak things anyway, the [config file](https://github.com/spatie/laravel-server-side-rendering/blob/master/config/ssr.php) is well documented.

### Setting up your scripts

You'll need to build two scripts: a server script and a client script. Refer to your framework-of-choice's documentation on how to build those.

This package uses a specific naming convention to find your scripts. Using mix, suffix the script names with `-client` and `-server`.

```js
mix.js('resources/assets/js/app-client.js', 'public/js')
   .js('resources/assets/js/app-server.js', 'public/js');
```

When rendering the app—more about that later—use the script name _without_ it's suffix.

```blade
{!! ssr('js/app.js) !!}
```

Your server script should call a `dispatch` function to send the rendered html back to the view. Here's a quick example of a set of Vue scripts for a server-rendered app. Read the [spatie/server-side-rendering](https://github.com/spatie/server-side-rendering) for a full explanation of how everything's tied together.

```js
// resources/assets/js/app.js

import Vue from 'vue';
import App from './components/App';

export default new Vue({
    render: h => h(App),
});
```

```js
// resources/assets/js/app-client.js

import app from './app';

app.$mount('#app');
```

```js
// resources/assets/js/app-server.js

import app from './app';
import renderVueComponentToString from 'vue-server-renderer/basic';

renderVueComponentToString(app, (err, html) => {
    if (err) {
        throw new Error(err);
    }

    dispatch(html);
});
```

### Rendering an app in your view

The package exposes an `ssr` helper to render your app.

```blade
<html>
    <head>My server side rendered app</head>
    <body>
        {!! ssr('js/app.js') !!}
    </body>
</html>
```

A facade is available too.

```blade
<html>
    <head>My server side rendered app</head>
    <body>
        {!! Ssr::entry('js/app.js') !!}
    </body>
</html>
```

Rendering options can be chained after the function or facade call.

```blade
<html>
    <head>My server side rendered app</head>
    <body>
        {!! ssr('js/app.js')->withContext('user', $user)->loadScriptAsync() !!}
    </body>
</html>
```

### Testing

``` bash
composer test
```

### Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

### Security

If you discover any security related issues, please email freek@spatie.be instead of using the issue tracker.

## Postcardware

You're free to use this package, but if it makes it to your production environment we highly appreciate you sending us a postcard from your hometown, mentioning which of our package(s) you are using.

Our address is: Spatie, Samberstraat 69D, 2060 Antwerp, Belgium.

We publish all received postcards [on our company website](https://spatie.be/en/opensource/postcards).

## Credits

- [Sebastian De Deyne](https://github.com/sebastiandedeyne)
- [All Contributors](../../contributors)

## Support us

Spatie is a webdesign agency based in Antwerp, Belgium. You'll find an overview of all our open source projects [on our website](https://spatie.be/opensource).

Does your business depend on our contributions? Reach out and support us on [Patreon](https://www.patreon.com/spatie).
All pledges will be dedicated to allocating workforce on maintenance and new awesome stuff.

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
