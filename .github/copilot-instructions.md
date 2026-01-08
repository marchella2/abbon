# Copilot / AI Agent Instructions — abbon

Quick context
- This project is a Laravel-based app (CarbonPHP flavour) with PHP 8.2, Vite front-end, and PHPUnit tests. See [composer.json](composer.json) and [package.json](package.json).

Quick start (commands the agent may run)
- Install: `composer install` then `npm install` (or run `composer run-script setup`).
- Dev server: `php artisan serve` + `npm run dev` (there is a `dev` composer script that uses `concurrently`).
- Build assets: `npm run build` (Vite) or `composer run-script post-update-cmd` for vendor assets.
- Tests: `./vendor/bin/phpunit` or `composer test` (composer `test` runs `@php artisan test`).

Architecture & important bootstrapping
- Entry point: [public/index.php](public/index.php) — checks maintenance, loads `vendor/autoload.php`, then bootstraps via [bootstrap/app.php](bootstrap/app.php).
- Application config is created via `Application::configure(...)->create()` in `bootstrap/app.php`. Routing and middleware are wired there.
- Routes: [routes/web.php](routes/web.php) (HTTP routes) and [routes/console.php](routes/console.php) for commands.
- Service providers live in [app/Providers](app/Providers). Example: `AppServiceProvider` used to register/boot app services.

Key directories & patterns
- Controllers: `app/Http/Controllers/` — follow controller -> model -> view flow.
- Models: `app/Models/` (PSR-4 autoload `App\` → `app/`).
- Views: `resources/views/` (Blade templates). Example: `routes/web.php` returns `view('welcome')`.
- Migrations: `database/migrations/` (note this repo uses zero-padded names like `0001_...`).
- Factories/Seeders: `database/factories/` and `database/seeders/` — use when creating test data.
- Tests: `tests/Feature` and `tests/Unit`. PHPUnit config sets `DB_CONNECTION=sqlite` and `DB_DATABASE=:memory:` for CI-friendly tests ([phpunit.xml](phpunit.xml)).

Project-specific conventions
- Composer scripts are used for multi-step setup (see `setup`, `dev`, `test` in [composer.json](composer.json)). Prefer invoking those scripts when the task touches setup or running multiple processes.
- Frontend uses Vite; compiled assets land in `public/build/` with a `manifest.json` consumed by views.
- The test environment is optimized for in-memory sqlite — create tests assuming that persistence is ephemeral.

Integration points & external dependencies
- Core framework: `laravel/framework` (see `composer.json`). Additional vendor code exists under `vendor/` including CarbonPHP packages.
- Background processing: composer `dev` script runs `php artisan queue:listen` and `php artisan pail` — be mindful of queue workers in local dev.
- Asset pipeline: `vite` and `laravel-vite-plugin` — update `resources/js` and `resources/css`, then run `npm run build`.

How to make safe changes
- Keep service registration in `app/Providers/*` and configuration changes in `config/*`.
- For database changes add migration files to `database/migrations/` and, when relevant, update `database/seeders/` and `database/factories/`.
- When editing routes, prefer `routes/web.php` for HTTP endpoints; keep route closures minimal, move logic to controllers.

Examples to reference while coding
- Add an HTTP route: see [routes/web.php](routes/web.php) for a minimal `Route::get('/', ...)` example.
- Bootstrapping example: see [bootstrap/app.php](bootstrap/app.php) for how routing and middleware are attached to the application.
- Tests: see `tests/Feature` and `phpunit.xml` for environment variables, use in-memory sqlite.

If you need to run anything I should know before running
- Creating `.env` from `.env.example` and generating the app key is part of `composer run-script setup`.
- Long-running dev tasks are launched via the `dev` composer script; avoid for CI steps.

When unsure, ask
- If a change touches deployment, DB migrations, or external services (mail, queues), ask before applying — these areas have side effects outside the codebase.

---
If you'd like, I can adapt this to include explicit common PR checklists (testing, caching, migrations) or add example snippets for typical agent tasks. Tell me which areas to expand.
