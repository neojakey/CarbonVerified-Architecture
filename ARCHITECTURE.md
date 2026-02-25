# CarbonRegistryApp - Project Architecture Overview

This document provides a technical overview of the `CarbonRegistryApp` architecture. It serves as a guide for AI agents and developers to understand the project's construction, design patterns, and standards.

## 1. Technical Stack
- **Language**: PHP 8.x (Custom lightweight framework)
- **Database**: MySQL 8.x (using PDO)
- **Frontend**: Tailwind CSS (Utility-first, built with standalone binary)
- **Styling**: Vanilla CSS for overrides (`assets/css/custom.css`)
- **Dependencies**: Managed via Composer. For deployment stability on specific hosts (e.g., Fasthosts), the `vendor/` directory is committed to the Git repository to ensure all dependencies and autoloaders are present on all environments without requiring remote composer execution.

---

## 2. Directory Structure
```bash
/
├── admin/          # Admin-only page controllers
├── ajax/           # Asynchronous request handlers
├── assets/         # Compiled CSS, images, and raw JS
│   └── css/        # input.css (source) and output.css (compiled)
├── auth/           # Authentication logic (Login, Register, Logout)
├── classes/        # Core business logic and Data Access (Repositories)
├── components/     # Reusable PHP snippets (Analytics, Cookie Banners)
├── config/         # App configuration handling
├── errors/         # Custom error pages
├── includes/       # Layout parts (header.php, footer.php)
├── logs/           # Application error logs
├── payment/        # Stripe integration and Webhooks
├── reports/        # Report generation logic
├── resources/      # Shared resources and generators
├── scripts/        # Utility and maintenance scripts
├── sql/            # Database schema and migrations
└── tests/          # Unit tests and verification scripts
```

---

## 3. Core Design Patterns

### 3.1. Repository Pattern (`classes/`)
Data access is abstracted into Repository classes (e.g., `UserRepository`, `PaymentRepository`).
- They receive a `PDO` instance in the constructor.
- They contain all SQL logic, keeping page controllers clean.
- Example: `$user = $userRepo->findById($id);`

### 3.2. Singleton Database Access
The `Database` class (`classes/Database.php`) provides a single point of access to the PDO connection using the Singleton pattern.
- Usage: `$pdo = Database::getInstance()->getConnection();`

### 3.3. Static Helper Classes
Core web primitives and utilities are handled by static helper classes:
- **`Session`**: Wraps `$_SESSION` with clean methods (`Get`, `Put`, `Has`).
- **`Input`**: Handles `$_GET`, `$_POST`, and simple sanitization.
- **`Csrf`**: Manages CSRF token generation and validation.
- **`Config`**: Loads environment variables and provides static access to settings.
- **`UIHelper`**: Generates reusable UI components (Modals, Icons, Cards).
- **`UrlHelper`**: Manages canonical URLs and link generation.
- **`SecurityHelper`**: Wrapper for session management and basic sanitization.

### 3.4. Service Classes
Dedicated service classes handle external integrations:
- **`GoogleAuthService`**: Manages Google OAuth2 authentication flow using `google/apiclient`.

---

## 4. Environment & Configuration
The project uses `.env` files for configuration, handled by `vlucas/phpdotenv`.
- **Environment Detection**: `Config::Load()` automatically detects if the app is on `localhost` or production.
- **Base URL**: Auto-calculated for dev and explicitly set for production to ensure correct asset linking.

---

## 5. UI & Styling Standards
- **Tailwind CSS**: The primary styling engine. Use Tailwind classes directly in HTML.
- **Build Process**: Styles are rebuilt using `./tailwindcss-linux` (or platform equivalent).
- **Dark Mode**: Enabled via the `dark` class on the `<html>` element. Use `dark:` variants.
- **Includes**: Pages are constructed by wrapping content with `require_once 'includes/header.php'` and `includes/footer.php`.

### 5.1. SEO & Structured Data
The project follows strict SEO and accessibility guidelines managed via `includes/header.php`:
- **Meta Tags**: Page-specific variables (`$pageTitle`, `$pageDescription`, `$ogImage`, `$canonicalUrl`) must be set before requiring the header.
- **Social Metadata**: Automatically generates Open Graph and Twitter Card tags based on the provided variables.
- **JSON-LD**: Implements Schema.org structured data using the `application/ld+json` script tag. It defines the `Organization`, `WebSite`, and `SoftwareApplication` entities in a unified graph.
- **Canonical URLs**: Automatically calculated to prevent duplicate content issues across different environments (localhost vs. production).

### 5.2. Theme & Dark Mode
The project supports a hybrid Dark/Light mode implementation:
- **Detection**: PHP determines the initial theme based on Session > Database > Cookie > Default ('light').
- **Toggling**: Handled by JavaScript in `includes/footer.php`. It toggles the `dark` class on the `<html>` element.
- **Persistence**: Use a dual-strategy for persistence:
    1.  **Local Storage**: `theme_preference` for immediate client-side restoration.
    2.  **Database**: AJAX call to `/ajax/set_theme.php` saves the preference to the `users` table for cross-device consistency.
- **Assets**: Logos and icons allow for dynamic swapping (e.g., `logo_horizontal_dark.svg` vs `_light.svg`).

---

## 6. Security Standards
- **Prepared Statements**: Always use PDO prepared statements in Repositories.
- **CSRF Protection**: All `POST` forms must include `<input type="hidden" name="csrf_token" value="<?= Csrf::GetToken() ?>">`.
- **Authentication Guards**: Use `requireLogin()` from `auth/auth.php` at the top of protected pages.
- **Admin Authorization**: Check `Session::Get('is_admin')` for sensitive routes.

---

## 7. Testing Standards
The project uses **PHPUnit** for automated testing.
- **Location**: All tests are located in the `tests/` directory.
- **Naming Convention**: Test files must follow the `ClassNameTest.php` pattern.
- **Bootstrap**: Environment setup for tests is handled by `tests/bootstrap.php`.
- **Execution**: Run tests using `./vendor/bin/phpunit`.
- **Requirements**: Every new Repository or Business Logic class must have a corresponding test class with high coverage.
- **Database**: Use a separate testing database or mock the database connection where appropriate.

---

## 8. Error Handling & Logging
The project uses a custom file-based logging system for debugging and monitoring.
- **Logger Class**: `classes/Logger.php` provides a static `write($message, $level)` method.
- **Log Location**: System logs are stored in `_logs/app_debug.log`.
- **Admin Console**: Administrators can view and manage system health via the **Error Console** at `admin/logs.php`.
- **System Vitals**: The admin console monitors disk space, failed login attempts (security), and database connectivity in real-time.
- **Best Practice**: Critical failures and security-related events (like failed logins) should always be logged with `Logger::write()`.

---

## 9. Interaction with AI Agents
When working with this codebase, AI agents should:
1.  **Follow the Repository Pattern**: Add new logic to `classes/RepositoryName.php`.
2.  **Use Static Helpers**: Prefer `Session::Get()` over `$_SESSION['key']`.
3.  **Rebuild CSS**: If new Tailwind classes are added, run the Tailwind build command.
4.  **Preserve UI Consistency**: Use `UIHelper` components where available for modals and common icons.
5.  **Maintain Documentation**: Update this file when introducing new architectural patterns.
