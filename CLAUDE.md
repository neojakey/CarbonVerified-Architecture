# CLAUDE.md — MonakTech PHP Projects

## Project Identity
You are working on a PHP web application built by Paul Jacobs, trading as MonakTech. All projects share the same architecture. Read this file fully before writing any code.

---

## Tech Stack
- **Language**: PHP 8.x — vanilla only. NO Laravel. NO Symfony. NO frameworks.
- **Database**: MySQL 8.x — PDO only. All queries via prepared statements in Repository classes.
- **Frontend**: Tailwind CSS (standalone binary). Vanilla JS only. No React, Vue, or jQuery.
- **No Namespaces** anywhere. Ever.
- **No ORM** anywhere. Ever.
- **Dependencies**: Composer. Commit `vendor/` to Git — required for Fasthosts deployment.

---

## Common Commands
```bash
# Build Tailwind CSS
./tailwindcss-linux -i assets/css/input.css -o assets/css/output.css --minify

# Run tests
./vendor/bin/phpunit

# Install dependencies
composer install

# Check PHP syntax
php -l filename.php
```

---

## Directory Structure
```
/
├── admin/          # Admin-only page controllers
├── ajax/           # Async request handlers
├── assets/css/     # input.css (source), output.css (compiled)
├── auth/           # login.php, register.php, logout.php, auth.php
├── classes/        # ALL business logic and repositories
├── components/     # Reusable PHP snippets
├── config/         # .env loading
├── errors/         # 403.php, 404.php, 500.php
├── includes/       # header.php, footer.php
├── logs/           # App debug logs
├── payment/        # Stripe integration and webhooks
├── reports/        # Report/export logic
├── scripts/        # Cron and utility scripts
├── sql/            # schema.sql and migrations
└── tests/          # PHPUnit tests
```

---

## Architecture Rules — Follow Without Exception

### Repository Pattern
- ALL database logic lives in `classes/XxxRepository.php`
- Repositories receive `$pdo` in the constructor
- Page controllers must not contain SQL — ever
- Example: `$user = $userRepo->findById($id);`

### Singleton Database
```php
$pdo = Database::getInstance()->getConnection();
```

### Static Helpers — Always Use These
| Helper | Usage |
|---|---|
| `Session::Get('key')` | Never use `$_SESSION['key']` directly |
| `Session::Put('key', $val)` | Set session value |
| `Session::Has('key')` | Check session value exists |
| `Input::get('key')` | Sanitised `$_GET` |
| `Input::post('key')` | Sanitised `$_POST` |
| `Csrf::GetToken()` | Generate CSRF token |
| `Csrf::Validate()` | Validate CSRF token |
| `Config::Get('KEY')` | Read from .env |
| `Logger::write($msg, $level)` | Write to logs/app_debug.log |
| `UrlHelper::canonical()` | Canonical URL for current page |
| `UrlHelper::redirect($url)` | Redirect |
| `SecurityHelper::requireLogin()` | Auth guard — top of protected pages |
| `SecurityHelper::requireAdmin()` | Admin guard — top of admin pages |
| `SecurityHelper::hashPassword($p)` | bcrypt hash |
| `SecurityHelper::verifyPassword($p, $h)` | bcrypt verify |
| `UIHelper::JobCard($job)` | Render job card component |
| `UIHelper::Alert($msg, $type)` | Render alert component |
| `UIHelper::Modal($id, $content)` | Render modal component |
| `UIHelper::Pagination($total, $page)` | Render pagination |

---

## Security — Non-Negotiable

- **EVERY POST form** must include: `<input type="hidden" name="csrf_token" value="<?= Csrf::GetToken() ?>">`
- **Validate CSRF** at top of every POST handler: `Csrf::Validate()` — abort if invalid
- **All SQL** via PDO prepared statements in Repositories. Zero raw `$_GET`/`$_POST` in SQL.
- **Passwords** always via `SecurityHelper::hashPassword()` (bcrypt). Never `md5` or `sha1`.
- **All output** escaped with `htmlspecialchars()`. No raw user input echoed.
- **Protected pages**: `SecurityHelper::requireLogin()` at very top of file, before any output
- **Admin pages**: `SecurityHelper::requireAdmin()` at very top of file
- **Session on login**: Always call `session_regenerate_id(true)` immediately after login
- **Session on logout**: Always call `session_destroy()`
- **Stripe webhooks**: Always verify `Stripe-Signature` header before processing

---

## Page Structure
Every page follows this pattern:
```php
<?php
require_once 'auth/auth.php';        // if protected
SecurityHelper::requireLogin();       // if protected

$pageTitle = 'Page Title';
$pageDescription = 'Meta description for SEO';
$canonicalUrl = UrlHelper::canonical();

require_once 'includes/header.php';
?>

<!-- Page content here -->

<?php require_once 'includes/footer.php'; ?>
```

---

## Dark/Light Mode
- Detection order: Session → DB (`users.theme_preference`) → Cookie → Default (`light`)
- Toggle via JS in `includes/footer.php` — toggles `dark` class on `<html>`
- Persistence: `localStorage` key `theme_preference` + AJAX to `ajax/set_theme.php`
- Always use Tailwind `dark:` variants. Never hardcode dark mode colours.
- Swap logos: `logo_dark.svg` / `logo_light.svg` based on current theme

---

## SEO — Every Page
Set these variables before `require_once 'includes/header.php'`:
```php
$pageTitle = '';        // Unique per page
$pageDescription = '';  // 150-160 chars, unique per page
$canonicalUrl = UrlHelper::canonical();
$ogImage = UrlHelper::asset('images/og-default.jpg'); // or page-specific
```
- Admin and dashboard pages: add `<meta name="robots" content="noindex">` in header
- Job listing pages: include JSON-LD `JobPosting` schema

---

## Tailwind CSS
- Use Tailwind utility classes directly in HTML
- **CRITICAL NOTE:** The project uses a **standalone Tailwind compiler** (`tailwindcss-linux`), NOT a Node-based build system.
- If you add *brand new* layout classes (e.g. `py-32`, new shadow utilities, or color variants) that haven't been used elsewhere in the project before, they will NOT exist in the live `output.css` file and the browser will ignore them.
- **You MUST rebuild the CSS after adding new classes:**
  `./tailwindcss-linux -i assets/css/input.css -o assets/css/output.css --minify`
- Custom overrides go in `assets/css/custom.css` only
- Dark mode strategy: `class` (not `media`)

---

## Environment (.env)
Never commit `.env`. Always provide `.env.example`.
```
APP_ENV=production
APP_URL=https://domain.co.uk
DB_HOST=localhost
DB_NAME=
DB_USER=
DB_PASS=
STRIPE_PUBLIC_KEY=
STRIPE_SECRET_KEY=
STRIPE_WEBHOOK_SECRET=
MAIL_HOST=
MAIL_USER=
MAIL_PASS=
MAIL_FROM=
MAIL_FROM_NAME=
GOOGLE_CLIENT_ID=
GOOGLE_CLIENT_SECRET=
GOOGLE_REDIRECT_URI=
```
Access via: `Config::Get('KEY')`

---

## Testing
- All tests in `tests/` — filename pattern: `ClassNameTest.php`
- Bootstrap: `tests/bootstrap.php`
- Run: `./vendor/bin/phpunit`
- Every Repository and Service class must have a corresponding test class
- Use a separate test database. Mock Stripe, Google, and SMTP calls.

---

## Error Handling
- Use `Logger::write($message, $level)` for all errors and security events
- Logs stored in `logs/app_debug.log`
- Admin error console at `admin/logs.php`
- Always log failed login attempts

---

## Google OAuth (where implemented)
- Uses `google/apiclient` via Composer
- Service class: `classes/GoogleAuthService.php`
- Auth initiation: `auth/google.php`
- Callback handler: `auth/google-callback.php`
- ALWAYS validate OAuth state parameter in callback — mismatch = 403 abort
- Never log or store raw Google access tokens
- Google-authenticated users have `password_hash = NULL` — never allow password login without set-password flow
- Google users skip email verification (`email_verified = 1` set on account creation)

---

## Stripe (where implemented)
- Service class: `classes/StripeService.php`
- Checkout: `payment/stripe_checkout.php`
- Webhook: `payment/webhook.php`
- Always verify webhook signature before processing
- Pass `job_id`, `tier_id`, `user_id` in Stripe metadata for webhook reconciliation
- Tier pricing validated server-side against `listing_tiers` DB table — never trust client-side amount

---

## What NOT To Do
- ❌ Never use Laravel, Symfony, or any PHP framework
- ❌ Never use namespaces
- ❌ Never use an ORM (no Eloquent, no Doctrine)
- ❌ Never write SQL directly in page controllers
- ❌ Never use `$_SESSION` directly — always `Session::Get()`
- ❌ Never use `$_GET`/`$_POST` directly in SQL — always via Repository prepared statements
- ❌ Never echo unescaped user input
- ❌ Never commit `.env`
- ❌ Never use `md5()` or `sha1()` for passwords
- ❌ Never skip CSRF validation on POST handlers
- ❌ Never run `composer install` on Fasthosts — vendor/ is committed to Git
- ❌ **Never forget to run `./tailwindcss-linux`** when you add novel styling classes to the codebase.
