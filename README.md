# App Starter Kit

_Build with **Laravel Jetstream** and **Vue Inertia JS**_

## Docs

-   [Setup](./docs/01_setup.md)
-   [Dev environment with Sail](./docs/02_dev-environment-with-sail.md)
-   [Create S3 Bucket with Minio](./docs/03_create-S3-bucket-with-minio.md)

## Commands

-   Start dev environment

```bash
sail up -d
npm run dev
```

-   Stop dev environment

```bash
sail down
```

-   Stop dev environment and delete data

```bash
sail down -v
```

## Minio S3 Object Store

-   Login at [http://localhost:8900/login](http://localhost:8900/login)
-   Username: `sail`
-   Password: `password`

## Mailpit Email testing

-   Go to [http://localhost:8025](http://localhost:8025)
