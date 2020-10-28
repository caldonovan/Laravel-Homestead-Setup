# Laravel Homestead Installation Guide for Windows

## Before We Begin

Although this is the recommended method of development in the Laravel docs, you do 
not have to follow it. An alternative to homestead, if you have Windows 10 Home and 
above, is to use the Windows Subsystem for Linux.

### WSL

WSL allows you to run a Linux OS inside of your Windows installation. Much like a VM, 
but with more convienient integration and set-up. WSL is more suited to people who are 
comfortable with the command line, and don't require any GUI tools. Amazingly, WSL works 
wonderfully with web development, and is simple to install.

#### Enabling WSL

1. Open the start menu and type 'features'.
2. Open 'Turn windows features on or off'.
3. Scroll down and tick 'Windows Subsystem for Linux'.
4. Click OK and restart your PC when prompted.

#### Installing an OS

1. Open the Windows Store app.
2. Search for 'Ubuntu'.
3. Install either version 18.04 or 20.04.
4. Launch the OS either through the terminal using ``ubuntu-20.04``, or click on the start menu shortcut.
5. Follow the instructions on setting up the instance.

#### Installing Pre-requisits

1. Perform a system update using ``sudo apt update && sudo apt upgrade``.
2. Install php, mysql-server, and composer using their respective package names.
3. Make sure to insall all the required extensions Laravel requires using ``sudo apt install php7.4-<extension_name>``.
4. Follow the Laravel docs to install the ``laravel/installer`` package globally.
5. If you're planning on using VueJS, install node and npm.

#### Serving sites

Once you have cloned a repository that you want to work on, you can use the built-in PHP development server to 
serve the sites. 

1. Navigate to the root directory of the project.
2. Type ``php -S localhost:8000`` to start the development server.
3. Access it from your browser according to the url: ``localhost:8000``.

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
6. When changing values in the .yaml file, you must reload the machine with the `--provision` flag.

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
* `vagrant ssh`: SSH into the VM
* `vagrant halt`: Stops the VM
* `vagrant reload`: Reboots the VM
* `vagrant reload --provision`: Reloads and provisions the VM (Do this every time you want to reboot the VM, as it reloads all configuration files.)

> If you have added the homestead.bat to your PATH, you can swap `vagrant` for `homestead` to achieve these commands from outside the homestead directory.

### Making New Apps

* SSH into the VM.
* Navigate to the code folder you created to house all the projects.
* Type `laravel new <project_name>`, where <project_name> is the name you want to call your app.
* After composer has finished installing Laravel, open the project folder in your favourite editor.
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
* Add an entry with the IP of your homestead box listed in the Homestead.yaml file, and the domain name you chose.
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

There are a few considerations when using NPM from within the VM, namely permissions. Whenever possible, try to run npm commands outside of the VM.
If you encounter errors when installing packages using npm, you can use the following commands to fix it:

* When running `npm install` for the first time, do so from outside the VM
* If you get an error when running `npm run dev`, do the following from inside the VM:
   * `rm -rf node_modules`
   * `rm package-lock.json`
   * `npm cache clear --force`
* Then from outside the VM run `npm install`, and all should be solved.
