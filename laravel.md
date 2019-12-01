
# PHP Laravel Getting Started

## Get Composer
Make sure you installed composer globally:
```
composer --version
```

## Get Laravel Installer
Get Laravel Installer via Composer:
```
composer global require laravel/installer
```

Check for Laravel installer:
```
laravel
```
You should have something like this output in your terminal
```
Laravel Installer 3.0.1

Usage:
  command [options] [arguments]

Options:
  -h, --help            Display this help message
  -q, --quiet           Do not output any message
  -V, --version         Display this application version
      --ansi            Force ANSI output
      --no-ansi         Disable ANSI output
  -n, --no-interaction  Do not ask any interactive question
  -v|vv|vvv, --verbose  Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  help  Displays help for a command
  list  Lists commands
  new   Create a new Laravel application
```
If not (it usually says ```laravel: command not found```), then do as below to add composer to your system path (applied for Linux Ubuntu):
```
echo 'export="$PATH:$HOME/.composer/vendor/bin' >> ~/.bashrc
```
or
```
echo 'export="$PATH:$HOME/.config/composer/vendor/bin' >> ~/.bashrc
```
then reload ```.bashrc```
```
source ~/.bashrc
```

## Install Laravel Project
Create a new project:
```
laravel new <project_name>
```
it will take a couple of minutes to download and craft your blank project.

Once the project is completed scaffolding, run this command to view it in the browser:
```
php artisan serve
```
don't forget to access your project root folder (using ```cd <path_to_project>```).

It then outputs something like ```Laravel development server started: http://127.0.0.1:8000```, so just copy the url ```http://127.0.0.1:8000``` to the browser, and view the web page.

Looks good, let's move on..

Next, let's make the User Authentication feature, run:
```
php artisan make:auth
```

**For new Laravel 6, Authentication isn't shipped out of the box, therefore we need to run extra commands**.

After creating a new project, change directory to the project root folder, and run:
```
composer require laravel/ui
```

Then install UI framework, either Vue or React, your choice:
```
php artisan ui vue
php aritsan ui react
```
then include the Authentication feature:
```
php artisan ui:auth
```
or do it with in just 1 line:
```
php artisan ui vue --auth
php aritsan ui react --auth
```

That's it, Authentication feature has been scaffolded. Now we need to get it to work.

Run these ```npm``` commands to pull in and compile dependencies:
```
npm install
npm run dev
```

When completed, view your project to see if it works:
```
php artisan serve
```
It should be up and running after all.
