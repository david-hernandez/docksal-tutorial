# Database Commands

Development projects commonly use a database server. Docksal's default stack will add a MySQL service, which 
the `db` commands can interact with.

```
$ fin db [command]
```

The help command will list all commands and options explained here.

```
$ fin help db
 
Usage: fin db <command> [file] [options]
 
Database related commands
 
...
```

When the database service is created it will automatically create a database named 'default' but if you want 
to create another database use the `create` command. The database will be added within the same MySQL server.

```
$ fin db create mydatabase
```

As with creating databases, you can also drop databases.

```
$ fin db drop mydatabase
```

The `list` command will print a simple list of existing databases. You can also use `ls` for short.

```
$ fin db list
 
information_schema
default
mysql
performance_schema
```

#### Options

Before proceeding with additional commands, the options need explaining. Given that you can have multiple databases 
you may need to specify which database for the commands to interact with.

```
Options:
  --db=drupal              	Use another database (default is the one set with 'MYSQL_DATABASE')
  --db-user=admin          	Use another mysql username (default is 'root')
  --db-password=p4$$       	Use another database password (default is the one set with 'MYSQL_ROOT_PASSWORD', see fin config)
  --db-charset=utf8        	Override charset when creating a database (default is utf8)
  --db-collation=utf8mb4   	Override collation when creating a database (default is utf8_general_ci)
```

As you see, you can specify the usual MySQL options for the name of the database, user, password, etc. The most commonly 
used option is `--db` since multiple databases are often used. If you do not specify a user and password, Docksal will 
use the root account.

One such use cases is using the `dump` command.

```
$ fin db dump my_file.sql
```

The file name specified will be written to the current directory. If you want a different location, you can write the 
path with the file name. (`~/my_file.sql` or `/some/directory/my_file.sql`) If no file is specified, the dump will be 
written to standard output. By default, Docksal will dump the 'default' database. If you need a different database, use 
the `--db` option.

 ```
 $ fin db dump --db=mydatabase my_file.sql
 ```

The same is true for the `import` command.

```
$ fin db import my_import_file.sql
```

```
$ fin db import --db=mydatabase my_file.sql
```

The `import` command also has two additional options.

```
--progress      	Show import progess (requires pv).
--no-truncate   	Do no truncate database before import.
```

`--progress` is useful when you have a particularly large database import. You will need to have the Pipe Viewer tool 
installed for it to work. (http://www.ivarch.com/programs/pv.shtml)

When importing Docksal will truncate the dump file by default. Use the `--no-truncate` option to skip that process.

Also, when importing the database you specify must exist. Docksal will not create it on the fly. So if doesn't already 
exist, use the `create` command first.
