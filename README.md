# Bright Home

## Generate Your Application Key

First, and most important thing when downloading the Laravel app is to generate your application key, otherwise, you application will fail at doing just about everything.

First, clone/download the project and navigate to the project root directory.

Then run:

```
$ php artisan key:generate
```

## Set Up Vagrant

Vagrant comes pre-installed in this app. Just ensure you have VirtualBox installed on your computer and Vagrant will take of the rest.

To run the app inside Vagrant, execute:

```
vagrant up
```

Once it's finished booting, you can then SSH into the virtual Vagrant machine with:

```
vagrant ssh
```

The project is located in the directory `~/Code/nannypack`.

## Set Up Database

The Vagrant virtual machine is already configured with PostgreSQL 9.5.

Copy the `.env.example` file and save duplicate as `.env`:

```
vagrant@bright-home:~ $ cp .env.example .env
```

Then go into your `.env` file and update the database credentials to these settings:

```
DB_CONNECTION=pgsql
DB_HOST=localhost
DB_PORT=5432
DB_DATABASE=bh_dev_db
DB_USERNAME=vagrant
DB_PASSWORD=vagrant
```

You will need to create a new Postgres user for your `vagrant` user:

```
vagrant@bright-home:~ $ sudo su - postgres
postgres@bright-home:~ $ psql
psql (9.5.6)
Type "help" for help.

postgres=# CREATE ROLE vagrant WITH PASSWORD 'vagrant';
postgres=# ALTER ROLE vagrant WITH SUPERUSER;
postgres=# ALTER ROLE vagrant WITH LOGIN;
postgres=# \q

postgres@bright-home:~ $ exit
vagrant@bright-home:~ $ createdb bh_dev_db
```

Ensure that the `.env` file is specified in your `.gitignore` file. This should already be done, but double check to be sure.

## Set Up Testing Environment

The project is equipped to use Codeception for unit, functional, and acceptance testing.

To set up your testing environment, copy the `.env` file and name the duplicate '.env.testing' like so:

```
vagrant@bright-home:~ $ cp .env .env.testing
```

This `.env.testing` file will be picked up by Codeception as your test configuration file. Since it is best practice to have a separate test database from your development database, we will need to slightly modify the `.env.testing` file to point to a different database than the one specified in `.env`:

```
DB_CONNECTION=pgsql
DB_HOST=localhost
DB_PORT=5432
DB_DATABASE=bh_test_db   // Changed from bh_dev_db to bh_test_db
DB_USERNAME=vagrant
DB_PASSWORD=vagrant
```

And last, but not least, we need to actually create the test database:

```
vagrant@bright-home:~ $ createdb bh_test_db
```

Ensure that the `.env.testing` file is specified in your `.gitignore` file. This should already be done, but double check to be sure.

### Running Tests

To access the binary in the terminal, use `vendor/bin/codecept`. To make your life a little easier, you should alias this to just `codecept` and add it to your `~/.bashrc` file so it aliases every time you boot the Vagrant VM:

```
$ echo "alias codecept='vendor/bin/codecept'" > ~/.bashrc
```

#### Run All Tests

```
$ vendor/bin/codecept run
```

#### Run Unit Tests

```
$ vendor/bin/codecept run unit
```

#### Run Functional Tests

```
$ vendor/bin/codecept run functional
```

#### Run Acceptance Tests

```
$ vendor/bin/codecept run acceptance
```
## Running Migrations

Migrations are a way to version your database. You can generate a new migration using `php artisan make:migration create_mynewentity_table`.

To execute your migrations, run:

```
php artisan migrate
```

This will run any pending migrations that have not been run yet in the past.

When you create a migration, they can be found in the `bright-home/database/migrations/` folder. The timestamp in front of the migration name is used by Laravel to determine which migration to run first. This is not something you should have to mess with, but is generally good to know to understand how migrations work.