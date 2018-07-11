# Creating Projects

Docksal's management utility is based on managing individual projects, with separate sets of 
containers for each. Creating, and even converting non-Docksal projects to use Docksal, is a 
simple, straight forward process.

`fin` is a global command that can execute anywhere, but it won't be very useful until you are in 
a project directory.

To initialize a directory as a Docksal project all you need to do is add a `.docksal` directory 
inside it. This is Docksal's only requirement.

```
// Change directories to your root projects directory.
$ cd ~/projects
 
// Make a new directory.
$ mkdir example
 
// Create an empty .docksal directory.
$ mkdir .docksal
```

Looking inside this new empty project, we'll see nothing but the `.docksal` directory.

```
$ ls
.        ..       .docksal
```

This alone is now enough to start a project. No configuration files are actually necessary 
because Docksal has a default configuration stack it will use to manage a project if none is 
specified. The default stack will create a command container (cli), a web container with Apache, 
and a database container with MySQL.

To start the project, use the `start` command.

```
$ fin start
Starting services...
Creating network "example_default" with the default driver
Creating volume "example_project_root" with local driver
Creating example_db_1  ... done
Creating example_cli_1 ... done
Creating example_web_1 ... done
Waiting for example_cli_1 to become ready...
Waiting for example_cli_1 to become ready...
Connected vhost-proxy to "example_default" network.
Virtual Host: http://example.docksal
```

This project is now functional. Docksal will automatically use the directory name as the virtual host. 
When you use this url in a web browser you'll discover you get a "Forbidden" error. That is expected, 
because we haven't yet created a document root or put anything in it.

The `status` command can be used to verify services are running.

```
$ fin status
 
    Name                  Command                State                 Ports           
---------------------------------------------------------------------------------------
example_cli_1   /opt/startup.sh supervisord   Up (healthy)   22/tcp, 3000/tcp, 9000/tcp
example_db_1    /entrypoint.sh mysqld         Up             0.0.0.0:32772->3306/tcp   
example_web_1   httpd-foreground              Up             443/tcp, 80/tcp         
```

Now that the project has been started, we can look at the default configuration.

```
$ fin config
 
---------------------
COMPOSE_PROJECT_NAME_SAFE: example
COMPOSE_FILE:
/Users/test/.docksal/stacks/volumes-bind.yml
/Users/test/.docksal/stacks/stack-default.yml
/Users/test/projects/example/.docksal/docksal.yml
ENV_FILE:
/Users/test/projects/example/.docksal/docksal.env
 
PROJECT_ROOT: /Users/test/projects/example
DOCROOT: docroot
VIRTUAL_HOST: example.docksal
VIRTUAL_HOST_ALIASES: *.example.docksal
IP: 192.168.64.100
MYSQL: 192.168.64.100:32772

Docker Compose configuration
---------------------
services:
  cli:
    ...
  db:
    ...
  web:
    ...
version: '2.1'
volumes:
  ...
---------------------
```

We can see in the printed configuration that Docksal defaults to using a document root called 
'docroot'. This can be changed in the project's configuration, but we'll leave the default and 
create that directory.

```
$ mkdir docroot
```
And then, add a file so we can see the web server is working. The line below will write an index.php 
file that prints the PHP configuration.

```
$ echo "<?php phpinfo(); ?>" > docroot/index.php
```
Now when visiting `http://example.docksal` in a web browser the PHP info page is displayed.

We can also check that the database server is running, as well.

```
$ fin db cli
...
 
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
 
mysql> _
```

We get a prompt, and are logged into MySQL. We know the database server is working.

## Converting an existing project to use Docksal

Converting an existing project to use Docksal is essentially the same process as creating a new 
project. All you need to do is add a `.docksal` directory to your project and start the services. If 
your project relies on a database, you'll need to import it into the database server, and of course 
you may need to make configuration changes that better match your project specifics, but with this bare 
minimum Docksal will already function.

## Using the wizard to create new projects

Docksal comes with a series of boilerplates that can be used to create new projects from scratch. 
This doesn't just mean setting up a a set of services. The wizard will create a project folder and new 
starting code base to begin developing a project.

Start at the root of your projects folder. The wizard will create the actual project folder that will 
contain the code and configuration.

```
$ cd ~/project
```

Then, run the project wizard.

```
$ fin project create

```

You will be presented with a number of prompts. This information is used to create the project.

```
1. Name your project (lowercase alphanumeric, underscore, and hyphen): foo
 
2. What would you like to install?
  PHP based
    1.  Drupal 8
    2.  Drupal 7
    3.  Wordpress
    4.  Magento
    5.  Laravel
    6.  Symfony Skeleton
    7.  Symfony WebApp
    8.  Grav CMS
    9.  Backdrop CMS
 
  JS based
    10. Hugo
    11. Gatsby JS
 
  HTML
    12. Static HTML site
 
Enter your choice (1-12): 1
 
Your site will be created at /Users/test/projects/foo
Your site will run Drupal 8
The URL of your site will be http://foo.docksal
 
Do you wish to proceed? [y/n]: y
```

Docksal will now clone from a project repository that contains the boilerplate for the chosen project and go through 
any predefined setup tasks.

```
Cloning repository...
Cloning into 'foo'...
remote: Counting objects: 51953, done.
remote: Compressing objects: 100% (811/811), done.
remote: Total 51953 (delta 311), reused 588 (delta 220), pack-reused 50891
Receiving objects: 100% (51953/51953), 30.59 MiB | 2.78 MiB/s, done.
Resolving deltas: 100% (23543/23543), done.
Checking out files: 100% (14901/14901), done.
3. Installing site
 Step 1  Initializing stack...
Removing containers...
Removing network foo_default
WARNING: Network foo_default not found.
Removing volume foo_project_root
WARNING: Volume foo_project_root not found.
Volume docksal_ssh_agent is external, skipping
Starting services...
Creating network "foo_default" with the default driver
Creating volume "foo_project_root" with local driver
Creating foo_cli_1 ... done
Creating foo_db_1  ... done
Creating foo_web_1 ... done
Waiting for foo_cli_1 to become ready...
Waiting for foo_cli_1 to become ready...
Connected vhost-proxy to "foo_default" network.
Waiting 10s for MySQL to initialize...
 Step 2  Initializing site...
Making site directory writable...
Copying /var/www/docroot/sites/default/settings.local.php...
You are about to DROP all tables in your 'default' database. Do you want to continue? (y/n): y
Starting Drupal installation. This takes a while. Consider using the --notify global option.                                                                                   [ok]
error sending mail
2018/06/27 02:55:36 dial tcp: lookup mail on 127.0.0.11:53: no such host
Installation complete.  User name: admin  User password: X2RuWjAAM6                                                                                                            [ok]
Unable to send email. Contact the site administrator if the problem persists.                                                                                                  [error]
Congratulations, you installed Drupal!                                                                                                                                         [status]
 
real	0m39.075s
user	0m9.050s
sys	0m12.660s
```

In this example, Drupal 8 was chosen so Docksal will perform a full install of Drupal. When complete 
it prints the administrator user name and password, and a record of the amount of time the installation took.

After the wizard is complete, check out the project directory.

```
$ cd foo
 
$ ls -l

total 24
drwxr-xr-x  11 test  staff   374 Jun 26 22:54 .
drwxr-xr-x  57 test  staff  1938 Jun 26 22:54 ..
drwxr-xr-x   3 test  staff   102 Jun 26 22:54 .circleci
drwxr-xr-x   7 test  staff   238 Jun 26 22:54 .docksal
drwxr-xr-x  13 test  staff   442 Jun 26 22:54 .git
-rw-r--r--   1 test  staff   108 Jun 26 22:54 .gitignore
-rw-r--r--   1 test  staff   184 Jun 26 22:54 .travis.yml
-rw-r--r--   1 test  staff  2254 Jun 26 22:54 README.md
drwxr-xr-x   3 test  staff   102 Jun 26 22:54 config
drwxr-xr-x  25 test  staff   850 Jun 26 22:54 docroot
drwxr-xr-x   3 test  staff   102 Jun 26 22:54 drush
```

We can see that not only does Docksal create a project directory and a `.docksal` directory it, but the 
boilerplate comes with a few other bells and whistles.

All of the boilerplates can be found in the Docksal Github account.

https://github.com/docksal
https://github.com/docksal/drupal8