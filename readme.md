# Laravel Ticket Selling API

> **Status: archived.** A simple Laravel project, kept as a reference.

A small JSON API for selling tickets to events, built with Laravel 8. It has three endpoints, two models
(`Event` and `Ticket`) and migrations for the tables. The routes are in [routes/api.php](./routes/api.php) and the
logic is in [TicketController](./app/Http/Controllers/TicketController.php).

![The assignment](./assets/description.png)

## Endpoints

| Method | Path | Does |
|---|---|---|
| `POST` | `/api/tickets/purchase` | buys tickets. Body: `event_id`, `user_id`, `quantity` (1 to 10). Answers `201` with the tickets and the remaining capacity, or `422` with the validation errors |
| `GET` | `/api/tickets/user/{user_id}` | the tickets a user has bought |
| `GET` | `/api/events/available` | events that have not happened yet and still have tickets |

A purchase runs in a database transaction and locks the event row while it counts, so two buyers cannot take the last
ticket at the same time. It is refused when the event already passed or when there are not enough tickets left.

## Data

- **events**: `name`, `description`, `capacity`, `price`, `event_date`, `location`
- **tickets**: `event_id`, `user_id`, a unique `ticket_number`, `purchase_date`, `status` (`active`, `cancelled` or `used`)

## Run it

You need PHP 7.3 or 8, [Composer](https://getcomposer.org) and a database (SQLite is the easiest).

```sh
composer install
cp .env.example .env
php artisan key:generate
# in .env: DB_CONNECTION=sqlite, and create the file with: touch database/database.sqlite
php artisan migrate
php artisan serve
```

```sh
curl -X POST http://localhost:8000/api/tickets/purchase \
  -H 'Accept: application/json' -H 'Content-Type: application/json' \
  -d '{"event_id": 1, "user_id": 1, "quantity": 2}'
```

The purchase endpoint does not authenticate the buyer (it trusts `user_id`), and the repo has no seeders or tests yet
beyond Laravel's defaults. Treat it as a starting point, not as production code.

## License

MIT, see [LICENSE](./LICENSE).
