# A package to make your application localized

![Packagist Downloads](https://img.shields.io/packagist/dt/jersyfi/laravel-localization)
![Packagist Version](https://img.shields.io/packagist/v/jersyfi/laravel-localization)
![GitHub License](https://img.shields.io/github/license/jersyfi/laravel-localization)


## Installation

You can install the package via composer
```bash
composer require jersyfi/laravel-localization
```
You need to publish the config file to customize the package
```bash
php artisan vendor:publish --provider="Jersyfi\Localization\LocalizationServiceProvider" --tag="config"
```
The published config `localization`looks like so
```php
return [

    /**
     * Applications default locale need to be set because the config('app.locale')
     * gives back the current locale and not the value from config
     */
    'default_locale' => 'de',

    /**
     * Application locales determines all locals that exists in the application
     */
    'locales' => [
        'en',
        'de'
    ],
    
    /**
     * Redirect to default locale when not found
     */
    'redirect_default' => false,

    /**
     * Detect user locale via http header
     * When no locale is stored in session user gets redirected
     */
    'detect_locale' => false,

];
```


## How to use

More information can be found in the original Laravel documentation with version 8.x.
There you need to know everything about [Routing](https://laravel.com/docs/8.x/routing) and [Localization](https://laravel.com/docs/8.x/localization). When you also want to have translatable models i prefere to use [laravel-translatable](https://github.com/spatie/laravel-translatable) from Spatie.

### Routing

The middleware is using `redirect_default` to redirect any request when the requested locale was not in `locales`. To detect the browser language when entering the page for the first time the `LocaleController` is using `detect_locale`.

You can redirect to the `default_locale` by accessing the `LocaleController` function called `localize` with the example:
```php
Route::get('/', [LocaleController::class, 'localize'])
    ->name('locale');
```
To group a route it is the easiest way to set a prefix named `{locale}` together with the middleware `locale`.
Inside this group you can set your own localized routes.
An example to get this localized route group:
```php
Route::prefix('{locale}')
    ->middleware('locale')
    ->group(function () {

        // your localized routes here
    });
```

### Helpers

`Localization::`
or
`app('localization')->`

Return all available locales
`Localization::getLocales()`

Return application locale
`Localization::getDefaultLocale()`

Return all available locales without the default locale.
`Localization::getLocalesWithoutDefault()`

Return the current Route URL with different locale
`Localization::currentRouteLocaleURL()`

Return the current Route URL with default locale
`Localization::currentRouteDefaultLocaleURL()`

Check if the locales are valid
`Localization::localeIsValid()`


## Examples

### Route to named routes

We create a simple index Route named `home` calling whatever you want. In this example we call a Controller. Then you can call your route from whereever you want with `route('home')`.
```php
Route::get('/', [LocaleController::class, 'localize'])
    ->name('locale');

Route::prefix('{locale}')
    ->middleware('locale')
    ->group(function () {

        // your localized routes here
        Route::get('/', [HomeController::class, 'index'])
            ->name('home');
    });
```
```html
<a href="{{ route('home') }}">Home</a>
```

### Create a canonical link

You need to call the helper function `Localization::currentRouteDefaultLocaleURL()`
```html
<link rel="canonical" href="{{ Localization::currentRouteDefaultLocaleURL() }}">
```

### Create alternate links

You need to call the helper function `Localization::currentRouteLocaleURL()` and pass the `$locale` you want to get the link for

To get all alternate links without the default locale you can call the helper function `Localization::getLocalesWithoutDefault()` inside a foreach loop. Inside the href of the html you can call the helper function `Localization::currentRouteLocaleURL()` and pass the `$locale` to it.
```html
@foreach(Localization::getLocalesWithoutDefault() as $locale)
    <link rel="alternate" hreflang="{{ Localization::getLocalSlug($locale) }}" href="{{ Localization::currentRouteLocaleURL($locale) }}">
@endforeach
```

### Create a language selector

```html
<div>{{ Localization::getLocalSlug() }}</div>
<ul>
    @foreach(Localization::getLocalesWithoutDefault() as $locale)
        <li>
            <a rel="nofollow" href="{{ Localization::currentLocaleRoute($locale) }}">{{ Localization::getLocalSlug($locale) }}</a>
        </li>
    @endforeach
</ul>
```


## Changelog

Please see [CHANGELOG](CHANGELOG.md) for more information what has changed recently.

## Credits

- [Jérôme Bastian Winkel](https://github.com/jersyfi)
- [All Contributors](../../contributors)

## License

The MIT License (MIT). Please see [License File](LICENSE) for more information.
