# Configurations and Environments

## Configurations
When building an application, it's helpful to have a configuration system that allows you to specify settings for your application.

For insight into the kind of configurations an app uses, browse the files in the `/config` folder in your Laravel app.

Note how each file contains an array of configuration values.

These values are accessible throughout your application using Laravel's `config` helper.

Examples:
```php
# *all* the mail related configs
dump(config('mail'));

# One specific config from the mail configs
dump(config('mail.host'));
```

Many of the configs that ship with Laravel are used by Laravel when your application is run. For example, Laravel's [Mail](https://laravel.com/docs/5.5/mail) functionality will refer/use the configs when sending email from your application.

You can also utilize the configs when writing your application code (we'll see examples of this in upcoming material).


## Define your own configs
You can add new configs for your application as needed. For example, in the `mail.php` config array, you might define what the support address is for your site:

```
# config/mail.php
return [
    [...]
    'supportEmail' => 'help@foobooks.com'
    [...]
];
```

Now, whenever you want to display this address, you can pull it from the config:

```
Questions? Email us at <?=config('mail.supportEmail')?>
```

Using configs in this way gives you __consistency__ (ensures you're using the same address everywhere) and __flexibility__ (if your address changes, you only have to change it in one spot).

In addition to adding configurations to existing config files, you can also create entirely new config files as needed.


## Environments
Building a web application often entails running that application in different environments that have different configuration needs.

For example, when running your application locally on your machine, you would define this as a **local** environment and it would have specific configuration needs, for example:

+ Turn on all error reporting
+ Connect to a local, development database
+ Route outgoing mail through a service like [MailTrap.io](https://mailtrap.io/)

On the flip side, you also have your application running on a live server, which you would define as a **production** environment that may have these specific configuration needs:

+ Turn off all error reporting to the page
+ Connect to a live database
+ Send outgoing mail using a service like [SendGrid](https://sendgrid.com/)

**local** and **production** are the two most common environments, but you may have other ones like **staging**, **testing**, or more.

Additionally, each developer working on your project might also have their own local environment configurations.



## Working with environments
Laravel utilizes the [DotEnv](https://github.com/vlucas/phpdotenv) PHP library. Here's how it works:

There are global configurations defined in `config/` and then each specific environment can overwrite those configurations on a as-needed basis via a custom `.env` file.

__Let's look at an example&mdash; your debugging configurations.__ When debugging is **on**, exceptions and errors are displayed in the browser. This is useful in local environments.

When debugging is *off*, exceptions and errors are suppressed and the user is shown a generic error page. This is useful in production environments.

If you open `/config/app.php`, you'll see where the `debug` config is set:

```php
/*
|--------------------------------------------------------------------------
| Application Debug Mode
|--------------------------------------------------------------------------
|
| When your application is in debug mode, detailed error messages with
| stack traces will be shown on every error that occurs within your
| application. If disabled, a simple generic error page is shown.
|
*/
'debug' => env('APP_DEBUG', false),
```

Here we see the [env](https://laravel.com/docs/helpers#method-env) helper function which takes two parameters:

1. Environment-specific variable you're looking for (in this case, `APP_DEBUG`)
2. What default value to use if that variable does not exist (in this case, `false`)

Environment-specific variables are set in `.env` files at the root of your project.

Here's a snapshot of the default `.env` file you will have:

```xml
APP_ENV=local
APP_KEY=[your unique key will be here]
APP_DEBUG=true
APP_LOG_LEVEL=debug
APP_URL=http://localhost

[...]
```

Notice the third variable, `APP_DEBUG`, is set to `true`.

And so, revisiting this line in your app config...

```
'debug' => env('APP_DEBUG', false),
```

...we can conclude that when the application is running in the **local** environment, app debugging will be on (true).


**Because the `.env` file is not tracked via git, it's possible that `APP_DEBUG` can be set to different values in different contexts (e.g. local v.s production).**


## Consistent configurations
Not all configurations use the env helper method, for example in `/config/session.php` encrypt is &ldquo;hardcoded&rdquo; as `false`:

```
'encrypt' => false,
```

This makes sense, because there's typically not a reason you would need to change this configuration in different environments.

However, if there is some reason you need to do just that, just edit the line to look like this:

```
'encrypt' => env('SESSION_ENCRYPT', false),
```

Now `encrypt` it will default to `false` but you have the option of overwriting it by setting `SESSION_ENCRYPT` in an environment's `.env` file.


## Reading configurations
In addition to examining your configuration files, you can see what specific configs are set to using the the global `config` helper function.

For example:

```php
# Echo out what the mail => driver config is set to
echo config('mail.driver');

# Dump *all* of the mail configs
dump(config('mail'));
```

Aside: [Wondering where you can quickly and easily test lines of code like these?](https://github.com/susanBuck/dwa15-spring2017-notes/blob/master/03_Laravel/99_Practice_Work.md)


## What's my current environment?

You can find out the environment your application is currently running in using the Artisan `env` command.

```bash
$ php artisan env
Current application environment: local
```

Or you can output the environment using the App facade's `environment` method:

```php
dump(App::environment());
```




## Production environment
SSH into your DigitalOcean production server and locate the `.env` file at the root of your foobooks example project.

The top of this file should look something like this:

```xml
APP_ENV=local
APP_KEY=[your unique key will be here]
APP_DEBUG=true
APP_LOG_LEVEL=debug
APP_URL=http://localhost

[...]
```

Edit it changing...

1. `APP_ENV` to `production`
2. `APP_DEBUG` to `false` (We don't want debugging enabled on the production server)
3. `APP_URL` to whatever domain you're using to run foobooks, e.g. `http://foobooks.yourdomain.com`

Example post edits:
```xml
APP_ENV=production
APP_KEY=[your unique key will be here]
APP_DEBUG=false
APP_LOG_LEVEL=debug
APP_URL=http://foobooks.dwa15.me

[...]
```

Save your changes to complete this configuration.
