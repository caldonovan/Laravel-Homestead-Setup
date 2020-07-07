# Laravel 7.0 Installation Guide for Windows

## Pre-requisites

You must have the following installed and configured in order to do this:

* Vagrant: <https://www.vagrantup.com/>.
* Virtualbox: <https://www.virtualbox.org/>.
* Virtualization enabled in BIOS (Typically `AMD-V` or `VT-x`).
* I would recommend you have at least 4GB RAM and 4 cores for performance.

## Installing the Box

1. Open CMD
2. Type `vagrant box add laravel/homestead`
3. When prompted, chose `3. Virtualbox`
4. Wait for the install to finish (typically takes at least 5 minutes on decent internet)

## Installing Homestead

1. Create a folder in a location of your choosing to install Homestead
2. Clone the Homestead repo: `git clone https://github.com/laravel/homestead.git`
3. cd into homestead folder
4. Switch to release `git checkout release`
5. Execute `init.bat`

## Configuring Homestead

1. Open `Homestead.yaml` in an editor
2. IP, Memory, CPU and Provider can be kept as default
3. Delete `keys` section.
4. The folders section maps the project source files between your local machine
   and the virtual machine. When choosing a path, make sure the host file path is in 
   the absolute path format: `C:\Users\MyUser\Documents\LaravelCode` is an example of such format.

![Homestead Yaml File](homestead_yaml_example.jpg?raw=true)

5. The sites section contains the path to the public folder of the project source files. As well as the url that will be used to view the site.

### Accessing Homestead outside of its Directory

* Create a `homestead.bat` file in a folder of your choosing
* Add the following code to it, replacing the path for the Homestead instance with yours:

```bat
   @echo off

   set cwd=%cd%
   set homesteadVagrant=E:\homestead

   cd /d %homesteadVagrant% && vagrant %*
   cd /d %cwd%

   set cwd=
   set homesteadVagrant=
```
* Add this file to your `PATH` environment variable.

### Vagrant Commands

* `vagrant up`: Starts the VM
* `vagrant halt`: Stops the VM
* `vagrant reload`: Reboots the VM
* `vagrant reload --provision`: Reloads and provisions the VM (Do this every time you want to reboot the VM, as it reloads all configuration files.)

### Making New Apps

* Navigate to the code folder you created to house all the projects
* Type `laravel new <project_name>`, where <project_name> is the name you want to call your app
* After composer has finished installing, open the project folder in your favourite editor
* Investigate the `.env` file, and configure the DB values as required, the default values for the homestead DB are:

```properties

DB_DATABASE=homestead
DB_USERNAME=homestead
DB_PASSWORD=secret

```

### Viewing Your New App

> This is specifically for Windows
* Navigate to the `Homestead.yaml` file
* Create a new entry that has a URL of your choice, avoid using `.app` or `.dev` unless you want to tackle with SSL issues!
* Add the direct path to the project files, such as: `/home/vagrant/code/blog/public`. Ensure it points to `public`.
* Open your `hosts` file located at `C:\Windows\System32\drivers\etc`.
* Add an entry with the IP of your homestead box, usually `192.168.10.10`, and the domain name you chose in the yaml file.
* Re-provision the machine using `vagrant reload --provision`.
* Navigate to the domain you chose in your browser, you should see the default Laravel landing page.

### Artisan Commands

All the laravel commands that can be executed in the project folder are prefixed with `php artisan`:

* `php artisan migrate:fresh`: Drop and re-create all databases with no data.
* `php artisan migrate:fresh --seed`: Same as above, but run the seeders to populate the database.
* `php artisan make`: Basically the main command used for Laravel, if you run this you can see all the commands used with make to create whatever you want.

> From within the Homestead machine, you can use aliases as set in the Homestead `alias` file
* With the default aliases included with Homestead, you can shorten `php artisan` to just `art`. For example, `art migrate`.
* This also applies to some composer commands, such as `composer dump-autoload` to `composer dump`.

### NPM Issues

I've had quite a few problems when it comes to compiling and running npm within the VM, so here are a few tips to avoid the fuckery that is npm problems:

* When running `npm install` for the first time, do so from outside the VM
* If you get an error when running `npm run dev`, do the following from inside the VM:
   * `rm -rf node_modules`
   * `rm package-lock.json`
   * `npm cache clear --force`
* Then from outside the VM run `npm install`, and all should be solved.
