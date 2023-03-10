# What is docker-laravel-standard?

A sample project for building a Laravel web app on Docker and developers provide the same development environment on Windows, Mac, and Linux.

## Project configuration

The whole project is built using Docker.
This project is a full stack -type web system.
It consists of the following Docker container.


## Docker configuration

To see `docker-compose.yml`

- app
  - web app
  - php-8.1
  - Laravel 9
  - When executing composer and artisan, execute it in the container.
- db
  - RDB server (MySQL, mariadb)
- nginx
  - Web server (nginx)
  - Reverse proxy built as a pre-stage of web access to laravel-app

## WEnvironment construction for Windows beginners

In the case of Windows, the folder structure is more flexible than other OSs, so there was an opinion that they did not know what to do when building a development environment for a work PC, so I wrote a standard directory structure. increase.

I recommend it, but it doesn't mean that you can't develop unless you do it this way. Please proceed with development in a way that suits your skills and policies.

### Folder structure (Windows)

- Create a `C:\Home` folder with Explorer
- Change icon of `Home` folder to green tree
  - Folder Properties->Customize->Folder Icon
- Create `C:\Home\src` and `C:\Home\local` folders
  - Clone/checkout the development target project such as `laravel-docker-standard` under `C:\Home\src`
  - Install development tools such as MSys2, Git, cmake, WinMerge, etc. under `C:\Home\local`

### What are environment variables

A bundle of string settings passed from the OS when it launches a process such as an application.

For example, if you write the name of a command from the console and try to execute it, the command's program is searched for in the order written in the `PATH` environment variable of the application in the console, and the first program found is executed.

In addition, each Docker container in docker-compose.yml has an environment variable set in the `environment` setting that will be used when each container starts. For example, the `db` container uses environment variables to define MySQL connection information.

### Setting environment variables (Windows)

In the case of Windows, environment variables are set using an application.
Rapid Environment Editor may be useful.
https://www.rapidee.com/en/about

- Installation destination is OK by default
- Left is system wide, right is current user (left is preferred)
- To enable editing on the left run the app itself with a privileged level or press the Run as Administrator button
- You can change the search order during program execution by manipulating the PATH on the system side (left side)
   - By pressing the [+] button, it will be listed in bullet points (higher priority)
   - By pressing the add button on the toolbar (there are two on the right side), you can insert a new path on the next line of the selected path.
   - Easy to copy path from Explorer
     - By clicking the white part on the right side of the path display field, it changes to a textbox and you can copy the path

### Initial setup

- Installing Docker
  - https://www.docker.com/products/docker-desktop/
- Install Visual Studio Code (standard editor for this project)
- Windows
  - Install Windows Terminal (from Microsoft Store app)
  - Install MSYS2
  - https://www.msys2.org/
  - Install Git for Windows (MSYS2 git is fine)
  - https://gitforwindows.org/
  - Select "Checkout as-is, comment Unix-style line endings"
  - https://qiita.com/uggds/items/00a1974ec4f115616580
  - Install docker-compose
- Mac
  - brew install
  - https://brew.sh/index_en
  - install git
  - Install docker-compose


### Update docker-compose (Mac)

Below, for brew. (Probably the state where docker-compose v1 series is executed in the initial state)

```bash
$ brew install docker-compose
$ brew unlink docker-compose
$ brew link --force --overwrite docker-compose
```

### Notes on Windows (git)

```
$ git config -l
```
and if `core.autocrlf=true` is set, it cannot be operated normally.
Do the following:

``` bash
$ git config --global core.autocrlf input
```
should be changed to

git config has three levels of settings: system/global/local.

-local
   - Valid only within that repository (highest priority)
-global
   - Valid for all repositories for current login user (next to local)
- system
   - Settings common to all users of the host (low priority)
   - On Windows, you can change if you run git config with privileged level


Check if the setting value including local is overwritten.

This must be done before cloning the repository.

### Initial setting

- Clone this project's repository
- .env setup
- Docker container setup
- composer setup

```bash
$ git clone https://github.com/kanryu/docker-laravel-standard.git
$ cd laravel-docker-standard
$ cd laravel-app
$ cp .env.local .env
$ cd ..
$ docker-compose up -d --build
$ docker-compose exec app composer install
$ docker-compose exec app npm install
$ docker-compose exec app npm run build
```

Connect to http://localhost:8086 with a web browser, and if the app service under development is displayed, the initial setup is complete.

### Network configuration by Docker container

When developing web using Docker container, access to localhost when connecting from development PC. For example, accessing `http://localhost:8086` will connect to the `nginx` container, and accessing 127.0.0.1:33306 will connect to the `db` container.

However, each Docker container of app, db, nginx is not started directly on the development PC, but exists in another network.

And the development PC itself is acting as a router to that network, e.g. nginx:80 mounted as localhost:8086 . This is called Port forwarding, which is a kind of NAT (Network Address Translation).

The Docker container is on the none network by default, and can only be connected to via port forwarded ports from the development PC. If you want the Docker container to freely communicate with the network like a normal host, add a `network` item on `docker-compose.yml` and associate the network with the Docker container.

Since app, db and nginx are in the same network, they can communicate freely with each other. For example, you can connect to the MySQL server in the db container from the app container with db:3306.

### How to connect to MySQL

There are many DB clients for both Windows and Mac, so you can use your favorite client.
If you don't know what to use, use DBeaver. https://dbeaver.io/

By configuring `docker-compose.yml`, MySQL's Docker container is a host named `db` and the container name is `bcp_creator_db`.

DB connection information is set as environment variables in `docker-compose.yml`.

When connecting to the MySQL server in the db from the work PC, set as follows.

- host
  - 127.0.0.1
- port
 - 33306
- database
 - laravel_app
-user
 - db-user
-password
 - db-pass (you can also set it to be entered every time you connect)

### composer

composer.json is built under `/laravel-app` and is also a Laravel 9 app.
Run inside the `app` container.

Example: Install a new package

Method 1: Run composer in docker indirectly from the host side

``` bash
$ docker-compose exec app composer require laravelcollective/html
```

Method 2: Run composer inside the app container

``` bash
$ docker-compose exec app bash
# composer require laravelcollective/html
```

### Node.js(npm)

CSS and JavaScript will be built with Node.js (npm).
package.json is built under `/laravel-app`.
Run inside the `app` container.

Example: Install a new package

Method 1: Run npm in docker indirectly from the host side

``` bash
$ docker-compose exec app npm run build
```

Method 2: Run npm inside the app container

``` bash
$ docker-compose exec app bash
# npm run build
```

### artisan

artisan can be run under the `/laravel-app` directory like docker.
Run inside the `app` container.

Example: Checking a web app's root settings

Method 1: Run artisan indirectly from the host side

``` bash
$ docker-compose exec app php artisan route:list
```

Method 2: Run artisan inside the app container

``` bash
$ docker-compose exec app bash
# php artisan route:list
```

## Debug method

Can be debugged with Xdebug3.

For vscode, describe as follows in `.vscode/launch.json`.
(Necessary extensions must be installed by yourself)

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Listen for Xdebug",
      "type": "php",
      "request": "launch",
      "port": 9003,
      "pathMappings": {
        "/var/www": "${workspaceRoot}/creator"
      }
    },
  ]
}
```

Run a PHP program inside the app container.
Or you can debug with vscode etc. when running the app web application.

Start the debugger with a breakpoint set with vscode etc. and wait,
When I run the web app and the program runs to the breakpoint line
The program will stop running there, and you will be able to get information such as variables.

By default, all PHP programs have xdebug turned ON, and if no debugger is set,

```
Xdebug: [Step Debug] Could not connect to debugging client. Tried: host.docker.internal:9003 (through xdebug.client_host/xdebug.client_port) :-(
```

The above warning is displayed. 
If you don't like it, comment out xdebug.start_with_request in `php-xdebug.ini`.

## About DEBUGBAR

The red bar that appears at the bottom of your browser on your webpage under development is Laravel's DEBUGBAR.

laravel-app/.env

in

```
APP_DEBUG=true
```
or
```
APP_DEBUG=false
DEBUGBAR_ENABLED=true
```
is displayed when
 
      

