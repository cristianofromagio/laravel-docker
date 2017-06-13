# Laravel Docker Dev Environment

This installation requires no `PHP` or `composer` globally installed.

## Settings

- php: 7.0-fpm
- nginx: 1.10
- mysql: 5.6

| **Service** | **Port** | **User**  | **Password** |
|:-----------:|----------|-----------|--------------|
|    `web`    |   8080   |     -     |       -      |
|  `database` |   33061  | homestead |    secret    |

## Setup

Follow the steps below:

### Step 1: Create a new Laravel project

Since we don't have composer installed, we need to create from the repository source code (refer to [https://github.com/laravel/laravel/releases](https://github.com/laravel/laravel/releases) for other versions).

```bash
curl -L https://github.com/laravel/laravel/archive/v5.4.0.tar.gz | tar xz
mv laravel-5.4.0 myapp
cd myapp
```

### Step 2: Install dependencies

To avoid composer installation, we create a throw-away container from the composer image by running inside the project folder the command:

```bash
docker run --rm -v $(pwd):/app composer/composer install
```

### Step 3: Build images and start services

Copy the files at this repository into the root folder of the created project, run:

```bash
docker-compose up -d
```

### Step 4: Additional configuration

Since we didn't installed the Laravel project using `composer`, aditional configuration is required.

#### Environment configuration file

We need to copy the `.env.example` file to `.env` file.

#### Application key & optimize

To create the application key we use `artisan`, but since all our PHP installation is inside the container, we run:

```bash
docker-compose exec app php artisan key:generate
docker-compose exec app php artisan optimize
```

Now you should be able to access your project running on http://localhost:8080

## Handling issues

#### Homepage returns HTTP 500 ERROR

Update project folder permissions with:

```bash
sudo chmod 755 -R myapp
chmod -R o+w myapp/storage
```

#### The bootstrap/cache directory must be present and writable.

```bash
docker-compose exec app php artisan cache:clear
```

And restart the containers:

```bash
docker-compose stop
docker-compose start
```

## Sources

This configuration was created following the guides below:

- [https://medium.com/@shakyShane/laravel-docker-part-1-setup-for-development-e3daaefaf3c](https://medium.com/@shakyShane/laravel-docker-part-1-setup-for-development-e3daaefaf3c) and [https://gist.github.com/anonymous/a13cf604981726c8e8b0bb05a35664e2](https://gist.github.com/anonymous/a13cf604981726c8e8b0bb05a35664e2)
- [https://kyleferg.com/laravel-development-with-docker/](https://kyleferg.com/laravel-development-with-docker/) and [https://github.com/kyleferguson/laravel-with-docker-example](https://github.com/kyleferguson/laravel-with-docker-example)
- [https://github.com/laradock/php-fpm/blob/master/Dockerfile-70](https://github.com/laradock/php-fpm/blob/master/Dockerfile-70) and partially [https://github.com/docker-library/php/issues/344](https://github.com/docker-library/php/issues/344)
- [https://stackoverflow.com/questions/31543175/getting-a-500-internal-server-error-on-laravel-5-ubuntu-14-04](https://stackoverflow.com/questions/31543175/getting-a-500-internal-server-error-on-laravel-5-ubuntu-14-04) and [https://stackoverflow.com/questions/43718391/laravel-throws-the-bootstrap-cache-directory-must-be-present-and-writable-erro](https://stackoverflow.com/questions/43718391/laravel-throws-the-bootstrap-cache-directory-must-be-present-and-writable-erro)
