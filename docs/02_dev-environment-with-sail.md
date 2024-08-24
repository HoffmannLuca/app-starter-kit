# Sail

## Installation

-   [Documentation](https://laravel.com/docs/11.x/sail#installing-sail-into-existing-applications)

```bash
composer require laravel/sail --dev

php artisan sail:install
```

-   choose the services needed for development

![Screenshot_A](./assets/02_dev-environment-with-sail_A.png)

![Screenshot_B](./assets/02_dev-environment-with-sail_B.png)

-   example ( `pgsql` for database / `minio` for S3 file storage / `mailpit` for email testing )

![Screenshot_C](./assets/02_dev-environment-with-sail_C.png)
