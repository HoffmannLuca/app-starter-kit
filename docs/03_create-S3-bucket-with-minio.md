# Minio

# Automatically create S3 test bucket with Minio in Laravel Sail

-   [Documentation](https://helgesver.re/articles/laravel-sail-create-minio-bucket-automatically)

-   add service in `docker-compose.yml`

```yml
minio-create-bucket:
    image: minio/mc
    depends_on:
        - minio
    environment:
        AWS_ACCESS_KEY_ID: ${AWS_ACCESS_KEY_ID}
        AWS_SECRET_ACCESS_KEY: ${AWS_SECRET_ACCESS_KEY}
        AWS_BUCKET: ${AWS_BUCKET}
        AWS_ENDPOINT: ${AWS_ENDPOINT}
    volumes:
        - "./docker/minio:/etc/minio"
    networks:
        - sail
    entrypoint: /etc/minio/create_bucket.sh
```

-   create script file `docker/minio/create_bucket.sh`

```sh
#!/bin/sh

/usr/bin/mc config host add local ${AWS_ENDPOINT} ${AWS_ACCESS_KEY_ID} ${AWS_SECRET_ACCESS_KEY};
# uncomment line below if you want the bucket to be cleared on every startup 'sail up'
# /usr/bin/mc rm -r --force local/${AWS_BUCKET};
/usr/bin/mc mb -p local/${AWS_BUCKET};
/usr/bin/mc policy set download local/${AWS_BUCKET};
/usr/bin/mc policy set public local/${AWS_BUCKET};
/usr/bin/mc anonymous set upload local/${AWS_BUCKET};
/usr/bin/mc anonymous set download local/${AWS_BUCKET};
/usr/bin/mc anonymous set public local/${AWS_BUCKET};

exit 0;
```

-   Make the Script Executable

```bash
chmod +x docker/minio/create_bucket.sh
```

-   add `.env` variables

```bash
FILESYSTEM_DISK=s3
AWS_ACCESS_KEY_ID=sail
AWS_SECRET_ACCESS_KEY=password
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=local
AWS_ENDPOINT=http://minio:9000
AWS_USE_PATH_STYLE_ENDPOINT=true
AWS_URL=http://localhost:9000/local
```

-   install dependencies for S3

```bash
composer require league/flysystem-aws-s3-v3 "^3.0" --with-all-dependencies
```
