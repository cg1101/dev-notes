# Using MySQL

## Install MySQL using port

Check notes of mysql57


## Start MySQL automatically

```
sudo launchctl load -F /Library/LaunchDaemons/org.macports.mysql57-server.plist 
```

## Create Database User

Start client as root
```bash
mysql -u root
```

Create a user with all privileges and grant option.
```SQL
CREATE USER myself;
GRANT ALL PRIVILEGES ON *.* TO 'myself'@'%' WITH GRANT OPTION;
\q
```

Try this login now, run:
```bash
mysql -u myself
```

## Connect to database

```bash
mysqld --verbose --help
```

Search in output:
```
...
bind-address                                                 *
...
port                                                         3306
...
skip-networking                                              FALSE
...
socket                                                       /tmp/mysql.sock
...
```

Update configuration file, do this
```
cd /opt/local/etc/mysql57
sudo vi my.cnf
```

Add following line to the end.

```
[mysqld]
skip-networking=FALSE
port=3306
bind-address=*
```

Quote from [MySQL documentation](https://dev.mysql.com/doc/refman/8.0/en/option-files.html):

>If multiple instances of a given option are found, the last instance takes precedence, with one exception: For mysqld, the first instance of the --user option is used as a security precaution, to prevent a user specified in an option file from being overridden on the command line. 


## Connect using SQLAlchemy

Check in mysql client
```
mysql> status
--------------
mysql  Ver 14.14 Distrib 5.7.17, for osx10.12 (x86_64) using  EditLine wrapper

Connection id:		10
Current database:	
Current user:		gcheng@localhost
SSL:			Not in use
Current pager:		stdout
Using outfile:		''
Using delimiter:	;
Server version:		5.7.17 Source distribution
Protocol version:	10
Connection:		Localhost via UNIX socket
Server characterset:	utf8
Db     characterset:	utf8
Client characterset:	utf8
Conn.  characterset:	utf8
UNIX socket:		/opt/local/var/run/mysql57/mysqld.sock
Uptime:			24 min 10 sec

Threads: 1  Questions: 62  Slow queries: 0  Opens: 109  Flush tables: 1  Open tables: 102  Queries per second avg: 0.042
--------------
```




Try following statements in Python interactive shell:
```python
import sqlalchemy as sa
sa.create_engine('mysql+pymysql://myself@localhost/information_schema').connect()
```

You should get something like this:
```
<sqlalchemy.engine.base.Connection object at 0x10e22c890>
```
