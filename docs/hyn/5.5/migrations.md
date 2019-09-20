---
title: Migrations
icon: fal fa-pallet-alt
---

Migrations are extremely important in the Laravel ecosystem. 
Migration commands for tenants have the Artisan prefix `tenancy:`.

# About the database/migrations path

It's not wise to store the tenants migrations inside your global
`database/migrations` directory. Laravel by default runs these migrations
when calling the `php artisan migrate` command. Even though you can suggest
a connection during that command, it's much cleaner to store these migrations
inside a separate directory, e.g. `database/migrations/tenant`. 
The migrations for the system database can stay in `database/migrations`.

# Default migrations for new tenants

If you want new tenants to be migrated by default, you can easily do so. In
your `tenancy.php` configuration set `db > tenant-migrations-path` to a valid
absolute path and all your new tenants will run these migrations by default.

```php
  // ..
  "tenant-migrations-path" => database_path('migrations/tenant'),
  // ..
```

# Default seeds for newly auto-migrated tenants

In case you would also like to seed the databases of your newly created
and migrated tenants, you can enable `db > tenant-seed-class` in your `tenancy.php`
configuration file. Change this setting to a fully namespaced class name
and the package will automatically run this seed after it has run the
automatic migrations.

# Migration related commands

Tenancy is also able to run migrations through Artisan commands.

Tenancy has added the `--website_id[=WEBSITE_ID]` option to each of the following native Laravel migrate and seed
command and namespaced it accordingly, for instance.

- `tenancy:migrate` - Run the database migrations
- `tenancy:migrate:refresh` - Reset and re-run all migrations
- `tenancy:migrate:reset` - Rollback all database migrations
- `tenancy:migrate:rollback` - Rollback the last database migration
- `tenancy:db:seed` - Seed the database with records

The --website_id optional option accepts multiple values. Though optional, if you leave the option out it will 
run the command against all tenants. For example:

- `php artisan tenancy:migrate --website_id=1` to migrate just tenant website 1.
- `php artisan tenancy:migrate --website_id=1 --website_id=2` to migrate tenant website 1 & 2.
- `php artisan tenancy:migrate` to migrate all tenant websites.

> The `website_id` reflects the auto incremented `id` column of the Website and **NOT** the UUID.

Please check the separate signatures for more information of valid arguments
and options.

> Running `tenancy:migrate` without any path or realpath option and
without having configured `tenant-migrations-path` will migrate your 
tenants with the files inside the `database/migrations` directory. 

# Reconstructing tenant databases

As long as your system database is intact, there's a way to recreate all tenant databases. You can use the `tenancy:recreate` artisan command to recreate all tenant databases that do not exist. The command will run the migrations and seeds according to your configuration in `tenancy.php`.

> Though the structure of all of your tenant databases will be recreated, the data in it won't.
