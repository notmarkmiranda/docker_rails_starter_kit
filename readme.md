# Docker & Rails Development Starter Kit

I'm adding this purely for myself, but maybe someone out there can use too. This is the least amount of files you need to spin up a new Dockerized Rails app.

## How to get this up and running
### Prerequisites
Docker should be **installed** and **running** on your machine.
### Steps
0. `git clone https://github.com/notmarkmiranda/docker_rails_starter_kit.git your_folder_name && cd your_folder_name`
0. You can update the `ruby_version` environment variable in `./Dockerfile` on line 1. This is currently set to use 3.0.3
0. You can also update the rails version in `./Gemfile` on line 2. This is currently set to use 7.0.0
0. To start a new rails project, I use:
`docker compose run --rm --no-deps web rails new . --force --database=postgresql -T --api`
    * `run --rm` is to tear down the container when the command is finished.
    * `--no-deps` tells docker compose not to run linked services like a container for postgres listed in our compose file.
    * `.` will install it in the folder you are currently in.
    * `--force` is a rails option that overwrites files, like our existing gemfile and adds the dependencies it needs.
    * `--database=postgresql` changes from the default sqlite database to postgres. You can probably use whatever you want, but I don't know how to configure other databases
    * `-T` is shorthand for `--skip-test-unit` or not installing minitest. You can omit this if you want to use minitest, but I generally use RSpec for rails testing.
    * `--api` is the command to preconfigure a smaller stack for API only applications.
0. According to [this link](https://docs.docker.com/samples/rails/), there is a step needed for linux installations: `sudo chown -R $USER:$USER .` I have not tested this.
0. `docker compose build` because our Gemfile was updated.
0. update `config/database.yml` with:
    ```
    default: &default
      adapter: postgresql
      encoding: unicode
      host: db
      username: postgres
      password: password
      pool: <%= ENV.fetch("RAILS_MAX_THREADS") { 5 } %>

    development:
      <<: *default
      database: myapp_development

    test:
      <<: *default
      database: myapp_test

    production:
      <<: *default
      database: myapp_production
      username: myapp
      password: <%= ENV["MYAPP_DATABASE_PASSWORD"] %>
    ```
0. To boot the app, run `docker compose up -d`
0. To create the database, run `docker compose exec web rails db:create`
0. Navigate to `https://localhost:3000` 🎉
