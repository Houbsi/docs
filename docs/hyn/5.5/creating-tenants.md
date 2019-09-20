---
title: Creating tenants
icon: fal fa-dolly-flatbed
---

Tenancy is heavily driven by events. For event listeners to properly work, you
have to use the provided repositories to create new websites and hostnames.

# Create website

```php
use Hyn\Tenancy\Models\Website;
use Hyn\Tenancy\Contracts\Repositories\WebsiteRepository;

$website = new Website;
app(WebsiteRepository::class)->create($website);
dd($website->uuid); // Unique id
```

## UUID

Each website will be given a unique identifier automatically. To prevent
guessing URLs and to add a slight increased level of security this is a
random hash. 

The UUID is used for the database username and database name. In addition
I'd recommend using this unique string for every public facing URL, eg
an admin dashboard.

By default this package will create a `uuid` automatically
using a UUID generator. You can replace that class in the 
`tenancy.php > website > random-id-generator`, as long as it implements
`Hyn\Tenancy\Contracts\Website\UuidGenerator`.

If you want to force your own UUID for the website, without creating a full
fledged UUID generator, set the `uuid` property of your website before passing
it to the repository. 

```php
$website = new Website;
// Implement custom random hash using Laravels Str::random
$website->uuid = Str::random(10);
app(WebsiteRepository::class)->create($website);
dd($website->uuid); // Random string of length 10
```

## Managing connection

Whenever you are using multiple databases, to separate tenants over different
regions for instance, you can easily specify the connection with which tenancy
should create, update and delete tenant databases and users.

```php
$website = new Website;
$website->managed_by_database_connection = 'system.asia';
app(WebsiteRepository::class)->create($website);
// Information of website is stored in the default system connection,
// the tenant database is created using the system.asia connection.
```

> Make sure the provided connection name is configured in your `config/database.php`.


# Create and connect hostname

A hostname allows the package to map a requested hostname to a website. Websites
that have no (active) hostnames connected to it will never be visible. So make
sure to connect a hostname to any tenant website you'd like to work on.

```php
use Hyn\Tenancy\Models\Hostname;
use Hyn\Tenancy\Contracts\Repositories\HostnameRepository;

$hostname = new Hostname;
$hostname->fqdn = 'luceos.demo.app';
$hostname = app(HostnameRepository::class)->create($hostname);
app(HostnameRepository::class)->attach($hostname, $website);
dd($website->hostnames); // Collection with $hostname
```

> The `fqdn` property of the hostname is crucial. It stands for
Fully Qualified Domain Name and can be something like `yourapp.com` or
`demo.yourapp.com` but can never include protocol (eg `http://`) or paths
(eg `yourapp.com/tenant`).

# Switch to new tenant

For the package to set up Laravel for the specific tenant, the environment has to be
"switched". This switching will set up things like the tenant database connection and
filesystem logic like custom config files.

```php
use Hyn\Tenancy\Environment;
$tenancy = app(Environment::class);

$tenancy->hostname($hostname);

$tenancy->hostname(); // resolves $hostname as currently active hostname

$tenancy->tenant($website); // switches the tenant and reconfigures the app

$tenancy->website(); // resolves $website
$tenancy->tenant(); // resolves $website

$tenancy->identifyHostname(); // resets resolving $hostname by using the Request
```

# Querying

In case you would like to query the database for hostnames or websites. Use the
`query()` method of the repositories, this will return a [`Illuminate\Database\Eloquent\Builder`][query-builder]
instance.

[query-builder]: https://laravel.com/docs/6.0/queries
