---
title: Events
icon: fal fa-hourglass-end
---

To create a fluid ecosystem around Laravel, this package uses events heavily.

> It's highly recommended to read up on the native [events][laravel-events] in Laravel.

# Model events

Namespaces: 

- `Hyn\Tenancy\Events\Hostnames`
- `Hyn\Tenancy\Events\Websites`

All of the models have the following events, whose purposes mirror Laravel's own model events.

- Creating
- Created
- Updating
- Updated
- Deleting
- Deleted

## Websites

Namespace: `Hyn\Tenancy\Events\Websites`.

- Identified: a tenant website was identified.
- NoneFound: no tenant website was identified.
- Migrated: a website was auto migrated (not fired from console commands).
- Switched: the current tenant website was changed.

## Hostnames

Namespace: `Hyn\Tenancy\Events\Hostnames`.

The hostname model has a few additional events:

- Attached: the hostname was attached to a website.
- Detached: the hostname was removed from a website.
- Identified: the hostname is identified based on the request.
- NoneFound: no hostname was found during identification.
- Redirected: the hostname is configured to redirect to another url.
- Secured: the hostname is configured to upgrade the request to https.
- Switched: currently active hostname was changed.
- UnderMaintenance: the hostname is configured to be in maintenance.

# Database

Namespace: `Hyn\Tenancy\Events\Database`.

- Creating
- Created
- Deleting
- Deleted
- Renaming
- Renamed
- ConfigurationLoading: the configuration for a tenant database connection
is loading.
- ConfigurationLoaded: the configuration for a tenant database connection
was loaded.
- ConnectionSet: a connection was set and possibly an old one purged.

# Webservers

Namespace: `Hyn\Tenancy\Events\Webservers`.

- ConfigurationSaved: the configuration for the webserver was saved to disk.

[laravel-events]: https://laravel.com/docs/6.0/events
