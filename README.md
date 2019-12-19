# My personal `How Did I...?`


## Table of contents

- [General info](#general-info)
- [Packages Used](#packages-used)
- [Let's get it started](#lets-get-it-started)
- [What's next?](#whats-next)

## General Info

For starters let's establish the following:
- Application name for this guide is `Stepz`. This can be changed to whatever else, but it should be changed everywhere.
- Application should be able to handle the following logic (Subject to change as dev goes on. Should be complex enough to hit a lot of scenarios and specific cases and need a lot of components which could be reused in other projects as well):
    - A `User` - can have multiple registered `Companies`
               - can have multiple `Role`s in the Application
    - Each `Role` can have multiple `Permission`s
    - `Companies` can have subsidiary companies or temporary/permanent establishments of their own.
    - The `User`s can *CRUD* information about `Assets` (equipment, etc.)
    - App should be able to store information about `Customer`s and `Contact`s
    - `User` should be able to make `Invoice`s and register `Expense`s
    - Each `Invoice`, `Expense`, `BankStatement` to be stored as file in Storage and coresponding model as `Attachment`
    - The `User` should be able to record the `Time`s, and use the records when making the `Invoice`
    - The `User` should be able to set up `Projects`
- From the described logic we extract the application Models:
    - `User`
    - `Role`
    - `Permission`
    - `Contact`
    - `Customer`
    - `Expense`
    - `Attachment`
    - `Invoice`
    - `BankStatement`
    - `Timer`
    - `Company`
    - `Asset`
    - `Project`
    
    
## Packages Used

| Project                       | Status                                                                     | Documentation                                      |
| ----------------------------- | -------------------------------------------------------------------------- | -------------------------------------------------- |
| [laravel]                     | [![laravel-status]][laravel-package]                                       | https://laravel.com/docs                           |
| [laravel-ui]                  | [![laravel-ui-status]][laravel-ui-package]                                 | https://laravel.com/docs/6.x/frontend              |
| [vue]                         | [![vue-status]][vue-package]                                               | https://vuejs.org/                                 |
| [vue-router]                  | [![vue-router-status]][vue-router-package]                                 | http://router.vuejs.org/                           |
| [vuex]                        | [![vuex-status]][vuex-package]                                                    | https://vuex.vuejs.org                             |
| [vuex-router-sync]            | [![vuex-router-sync-status]][vuex-router-sync-package]                     | [vuex-router-sync]                                 |
| [laravel-vue-i18n-generator]  | [![laravel-vue-i18n-generator-status]][laravel-vue-i18n-generator-package] | [laravel-vue-i18n-generator]                       |



[laravel]: https://github.com/laravel/laravel
[laravel-status]: https://poser.pugx.org/laravel/framework/v/stable.svg
[laravel-package]: https://packagist.org/packages/laravel/framework

[laravel-ui]: https://github.com/laravel/ui
[laravel-ui-status]: https://poser.pugx.org/laravel/ui/v/stable.svg
[laravel-ui-package]: https://packagist.org/packages/laravel/ui

[vue]: https://github.com/vuejs/vue
[vue-status]: https://img.shields.io/npm/v/vue.svg
[vue-package]: https://npmjs.com/package/vue

[vue-router]: https://github.com/vuejs/vue-router
[vue-router-status]: https://img.shields.io/npm/v/vue-router.svg
[vue-router-package]: https://npmjs.com/package/vue-router

[vuex]: https://github.com/vuejs/vuex
[vuex-status]: https://img.shields.io/npm/v/vuex.svg
[vuex-package]: https://npmjs.com/package/vuex

[vuex-router-sync]: https://github.com/vuejs/vuex-router-sync
[vuex-router-sync-status]: https://img.shields.io/npm/v/vuex-router-sync.svg
[vuex-router-sync-package]: https://npmjs.com/package/vuex-router-sync


[laravel-vue-i18n-generator]: https://github.com/martinlindhe/laravel-vue-i18n-generator
[laravel-vue-i18n-generator-status]: https://poser.pugx.org/martinlindhe/laravel-vue-i18n-generator/v/stable.svg
[laravel-vue-i18n-generator-package]: https://packagist.org/packages/martinlindhe/laravel-vue-i18n-generator

## Let's get it started

**First step**, install a fresh Laravel instance

This can be done using 
```
laravel new Stepz
```
  or 
```
composer create-project --prefer-dist laravel/laravel Stepz
```

> Note: If we're already inside a folder `stepz` where we want to install our project, we can simply run `laravel new` to proceed with instalation.


**Next step** is to edit the `.env` file with the proper values for the application name, the credentials for connecting to the database and the default Mail driver to 'log':

    APP_NAME=Stepz
    DB_DATABASE=stepz
    DB_USERNAME=root
    DB_PASSWORD=
    MAIL_DRIVER=log


**Next step** is to clean up the default scaffolding

    php artisan preset none

This will clean some of the default `package.json` dependencies.


**Next step** is only to be done if needed, based on the development environment.

Laravel 5.4 made a change to the default database character set, and it’s now utf8mb4 which includes support for storing emojis. This only affects new applications and as long as you are running MySQL v5.7.7 and higher you do not need to do anything.

Edit `app\Providers\AppServiceProvider.php`

```php
    use Illuminate\Support\Facades\Schema;

    public function boot()
    {
        // Schema::defaultStringLength(191); //Uncomment if needed
    }
```


**Next step** is to create `app\Models` folder

After that move `app\User` inside `app\Models\` folder, and edit the namespace in `app\Models\User.php` to 

```php
namespace App\Models;
```

Replace `App\User` with `App\Models\User` in:
- `config/auth.php`
- `database/factories/UserFactory.php`
- `app/Http/Controllers/Auth/RegisterController.php`


**Next step** is to set up Laravel so that all newly created models go to the Models folder:

For that we run in terminal: 

    php artisan make:command ModelMakeCommand

This will create a new file in `app\Console\Commands\ModelMakeCommand.php` where we paste the following code:

```php
<?php

namespace App\Console\Commands;

use Illuminate\Foundation\Console\ModelMakeCommand as Command;

class ModelMakeCommand extends Command
{
    /**
     * Get the default namespace for the class.
     *
     * @param  string  $rootNamespace
     * @return string
     */
    protected function getDefaultNamespace($rootNamespace)
    {
        return "{$rootNamespace}\Models";
    }
}
```

After that we need to overwrite the existing binding in the Service Container so our `AppServiceProvider.php` will look like below:

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use Illuminate\Support\Facades\Schema;
use App\Console\Commands\ModelMakeCommand;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Bootstrap any application services.
     *
     * @return void
     */
    public function boot()
    {
        // Schema::defaultStringLength(191); //Uncomment if needed
    }
    /**
     * Register any application services.
     *
     * @return void
     */
    public function register()
    {
        $this->app->extend('command.model.make', function ($command, $app) {
            return new ModelMakeCommand($app['files']);
        });
    }
}
```

To test things out let's create our first custom model:

    php artisan make:model Company -mrc

> Note: adding `-mrc` flag will create, besides the model, a migration and a resourceful controller.


**Next step** is to install the `laravel/ui` first party package, and choose the preset to start building on. For this we will run, in terminal:

```
composer require laravel/ui
php artisan ui vue --auth
npm install && npm run dev
```

Remove everything from `routes/web.php` and replace with the following:

```php
<?php

Auth::routes();

Route::get('/{any}', 'HomeController@index')->where('any', '.*');

```

This will register the default auth routes and will basically leave everything else to be handled by our vue-router later on.

Update: 
```php
protected $redirectTo = '/home';
```
to 
```php
protected $redirectTo = '/';
```
in:
- `app/Http/Controllers/Auth/ConfirmPasswordController.php`
- `app/Http/Controllers/Auth/LoginController.php`
- `app/Http/Controllers/Auth/RegisterController.php`
- `app/Http/Controllers/Auth/ResetPasswordController.php`
- `app/Http/Controllers/Auth/VerificationController.php`

and

```php
return redirect('/home');
```
to
```php
return redirect('/');
```
in `app/Http/Middleware/RedirectIfAuthenticated.php`


**Next step**, because on a fresh installation we will update and rollback the database structure quite often, we need to create a user record to work with:

```
php artisan make:seeder UsersTableSeeder
```

This will create  `database\seeds\UsersTableSeeder.php` where we will add the details for our user:

```php
<?php

use Illuminate\Database\Seeder;
use App\Models\User;

class UsersTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        $user = new User;

        $user->email = 'admin@example.com';
        $user->password = bcrypt('password');
        $user->name = 'John Doe';

        $user->save();
    }
}

```

After that we will uncomment in `database\seeds\DatabaseSeeder.php` the following line:

```php
$this->call(UsersTableSeeder::class);
```

> Note: **Always remember to change these user / credentials in production.**


**Next step** is to configure the `phpunit` to run the tests in a sqlite - in memory database:

Add the following two lines in `phpunit.xml`:

```xml
    <server name="DB_CONNECTION" value="sqlite"/>
    <server name="DB_DATABASE" value=":memory:"/>
```


**Next step** is to 
    - delete `resources\views\welcome.blade.php`,
    - duplicate `resources\views\layouts\app.blade.php` into `resources\views\layouts\guest.blade.php`
    - replace:

```php
@extends('layouts.app')
```

to

```php
@extends('layouts.guest')
```

in:

- `resources\views\auth\login.blade.php`
- `resources\views\auth\register.blade.php`
- `resources\views\auth\verify.blade.php`
- `resources\views\auth\passwords\email.blade.php`
- `resources\views\auth\passwords\reset.blade.php`




**Next step** is to pull in the following package through npm:

```
npm i vue-router --save-dev
npm i vuex --save-dev
npm i vuex-router-sync --save-dev
npm i vue-i18n --save-dev
```

Before starting structuring our javascript files and components we will configure an alias for webpack which will allow us to use `@` instead of multiple `../` in order to properly set the path of some components. We can do that by adding the following to `webpack.mix.js`:

```js
mix.webpackConfig({
   resolve: {
       extensions: ['.js', '.vue'],
       alias: {
           '@': path.resolve(__dirname, 'resources/js/')
       }
   }
});
```

Let's now set up a basic file structure for the frontend:

- In `resources\js`: 
    - Remove the file `resources/js/components/ExampleComponent.vue`, which comes with the `laravel/ui` we installed earlier.
    - Create folder `layouts`
    - Create folder `router`
        - Create `router\routes.js`  (we will create the components for pages a bit later on)

        ```js
        const routes = [
            {
                path: '/',
                name: 'Dashboard',
                component: require('../pages/Dashboard.vue')
            },
            {
                path: '/profile',
                name: 'Profile',
                component: require('../pages/Profile.vue')
            },
            {
                path: '*',
                name: '404',
                component: require('../pages/errors/Error404.vue')
            }
        ];

        export default routes;
        ```

        - Create `router\index.js`

        ```js
        import VueRouter from 'vue-router';
        import routes from './routes';

        export default new VueRouter({
            mode: 'history',
            routes,
        });
        ```

    - Create folder `store`
    - Create folder `pages`
        We will need to create the components which we already referenced in `routes.js` first:
        - Create `resources/js/pages/Dashboard.vue`
        - Create `resources/js/pages/Profile.vue`
        - Create `resources/js/pages/errors/Error404.vue`
        
    - Create folder `plugins`

In order to consume our laravel lang files also in the frontend and keep a single source of truth for all translations used in the App we will use the following package:

```
composer require martinlindhe/laravel-vue-i18n-generator
php artisan vendor:publish --provider="MartinLindhe\VueInternationalizationGenerator\GeneratorProvider"
```

The latest command will generate `config\vue-i18n-generator.php` where we will update the following line:

```php
    'jsFile' => '/resources/js/vue-i18n-locales.generated.js',
```

to 

```php
    'jsFile' => '/resources/js/plugins/vue-i18n-locales.generated.js',
```

Next, we will need translated files for the laravel default lang files,  which we could get from here: `https://github.com/caouecs/Laravel-lang` and place them in `resources\lang\` only for the languages we would like our App to use.

Similarily we would copy the translations for Countries from `https://github.com/umpirsky/country-list`.

Now, each time we will make changes to the lang files, changes that would need to be reflected in the front side, we will use the following command to re-write the file that will be used by `vue-i18n`:

```
    php artisan vue-i18n:generate
```


## What's Next?

- https://webpack.js.org/api/module-methods/#magic-comments
- https://medium.com/front-end-weekly/webpack-and-dynamic-imports-doing-it-right-72549ff49234
