# Hydraudrill Madagascar: Drupal 11 backend

Drupal 11 site for https://hydraudrill-madagascar.com. It also serves the JSON:API
backend for the todo app at https://test.hydraudrill-madagascar.com.

## What's in this repo

| Path | Contents |
| --- | --- |
| `composer.json` / `composer.lock` | Drupal core, Drush and other dependencies (exact versions) |
| `config/sync/` | Exported site configuration (content types, roles, JSON:API settings…) |
| `web/sites/default/settings.php` | Settings without secrets; loads `settings.local.php` |
| `web/sites/default/services.yml` | CORS for the todo app |
| `web/modules/custom/`, `web/themes/custom/` | Custom code (none yet) |

Not in git: `vendor/`, `web/core/`, contrib modules/themes (Composer installs them),
`settings.local.php` (database password, hash salt), uploaded files.

## Todo API

- Content type **Task** (`node--task`): `title` = task text, `field_done` = boolean.
  New tasks are unpublished, so only their author can see them.
- Role **Todo user** (`todo_user`): create, edit and delete own tasks; view own unpublished content.
- Endpoints: `POST /user/login?_format=json`, `GET|POST /jsonapi/node/task`,
  `PATCH|DELETE /jsonapi/node/task/{uuid}` (send `X-CSRF-Token` from `/session/token`).
- CORS: only `https://test.hydraudrill-madagascar.com`, with credentials.

## Set up a copy

```bash
git clone https://github.com/miandry/backenapi.git && cd backenapi
composer install
cp web/sites/example.settings.local.php web/sites/default/settings.local.php
# edit settings.local.php: add $databases['default']['default'] and $settings['hash_salt']
vendor/bin/drush site:install --existing-config -y
```

## Day-to-day

```bash
vendor/bin/drush config:export -y   # after changing settings in the admin UI, then commit config/sync
vendor/bin/drush deploy             # after pulling: updb + config:import + cache rebuild
composer update "drupal/core-*" --with-all-dependencies   # core updates
```
