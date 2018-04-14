Host a Python WSGI app
======================

This is a quick guide that shows the basic steps about hosting a WSGI application on an Apache Web server.

In this guide, the target machine is running macOS. The package manager is [MacPort](https://www.macports.org/). The database is [PostgreSQL](https://www.postgresql.org/).

Install MacPort
---------------
Please follow instruction in [this page](https://www.macports.org/install.php) to install MacPort.

Install Postgresql
------------------
After port is successfully installed, we can then use it to install postgresql. First, let's check for available releases. 

```
port list postgresql*
```

This may return a list of version, e.g.:
```
postgresql-jdbc                @9.1-901        java/postgresql-jdbc
postgresql-unaccent            @0.1            databases/postgresql-unaccent
postgresql7                    @7.4.24         databases/postgresql7
postgresql10                   @10.3           databases/postgresql10
postgresql10-doc               @10.3           databases/postgresql10-doc
postgresql10-server            @10.3           databases/postgresql10-server
postgresql80                   @8.0.26         databases/postgresql80
postgresql80-doc               @8.0.26         databases/postgresql80-doc
postgresql80-server            @8.0.26         databases/postgresql80-server
postgresql81                   @8.1.23         databases/postgresql81
postgresql81-doc               @8.1.23         databases/postgresql81-doc
postgresql81-server            @8.1.23         databases/postgresql81-server
postgresql82                   @8.2.23         databases/postgresql82
postgresql82-doc               @8.2.23         databases/postgresql82-doc
postgresql82-server            @8.2.23         databases/postgresql82-server
postgresql83                   @8.3.23         databases/postgresql83
postgresql83-doc               @8.3.23         databases/postgresql83-doc
postgresql83-server            @8.3.23         databases/postgresql83-server
postgresql84                   @8.4.22         databases/postgresql84
postgresql84-doc               @8.4.22         databases/postgresql84-doc
postgresql84-server            @8.4.22         databases/postgresql84-server
postgresql90                   @9.0.23         databases/postgresql90
postgresql90-doc               @9.0.23         databases/postgresql90-doc
postgresql90-server            @9.0.23         databases/postgresql90-server
postgresql91                   @9.1.24         databases/postgresql91
postgresql91-doc               @9.1.24         databases/postgresql91-doc
postgresql91-server            @9.1.24         databases/postgresql91-server
postgresql92                   @9.2.24         databases/postgresql92
postgresql92-doc               @9.2.24         databases/postgresql92-doc
postgresql92-server            @9.2.24         databases/postgresql92-server
postgresql93                   @9.3.22         databases/postgresql93
postgresql93-doc               @9.3.22         databases/postgresql93-doc
postgresql93-server            @9.3.22         databases/postgresql93-server
postgresql94                   @9.4.17         databases/postgresql94
postgresql94-doc               @9.4.17         databases/postgresql94-doc
postgresql94-server            @9.4.17         databases/postgresql94-server
postgresql95                   @9.5.12         databases/postgresql95
postgresql95-doc               @9.5.12         databases/postgresql95-doc
postgresql95-server            @9.5.12         databases/postgresql95-server
postgresql96                   @9.6.8          databases/postgresql96
postgresql96-doc               @9.6.8          databases/postgresql96-doc
postgresql96-server            @9.6.8          databases/postgresql96-server
postgresql_autodoc             @1.41           databases/postgresql_autodoc
postgresql_select              @0.3            databases/postgresql_select
```

We can choose the version we want, e.g. Postgresql 9.6, then we run:
```
sudo port install postgresql96-server
```

The initialization should be taken care of by installer. However, in case you need to initialize database yourself, you can check the notes of this port:
```
port notes postgresql96-sever
```
This will give you the instructions.

Once database is created, you may try this:
```
sudo -u postgres psql -l
```
It should return something like this:
```
                                      List of databases
        Name        |  Owner   | Encoding |   Collate   |    Ctype    |   Access privileges   
--------------------+----------+----------+-------------+-------------+-----------------------
 postgres           | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | 
 template0          | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                    |          |          |             |             | postgres=CTc/postgres
 template1          | postgres | UTF8     | en_US.UTF-8 | en_US.UTF-8 | =c/postgres          +
                    |          |          |             |             | postgres=CTc/postgres

```

Everything seems okay. For convenience, you may want to create a user for yourself.
```
sudo -u postgres createuser <your_login>
```

Give proper privileges to yourself. Then verify you can login. You may need to update postgresql's configuration. First find out where it is saved:
```
echo "SHOW hba_file" | psql postgres
```
And we get something like this:
```
                       hba_file                       
------------------------------------------------------
 /opt/local/var/db/postgresql96/defaultdb/pg_hba.conf
(1 row)
```
Update this file if needed. For more information, check [Postgresql's documentation](https://www.postgresql.org/docs/9.6/static/client-authentication.html)


Install Apache2
---------------
First, let's check available releases:
```
port list apache*
```

Let's install Apache2.4
```
sudo port install apache2
```

Apache server should be started automatically, in case you need to start it manually, you can run:
```
port load apache2
```
This is actually a shortcut to macOS's ```launchctl``` command.

Install mod_wsgi
----------------
First, search for that port:
```
port search mod_wsgi
```
It returns:
```
mod_wsgi @4.5.20 (www, python)
    Python WSGI adapter
    module for Apache.
```
The we install it:
```
sudo port mod_wsgi
```

Next, we need to update apache2's configuration file to load this module. First, let's find out where is apache2's configuration file.
```
apachectl -V
```
From output, we can tell configuration is ```/opt/local/etc/apache2/httpd.conf```

Edit that file and added following line to it
```apache
LoadModule wsgi_module lib/apache2/modules/mod_wsgi.so
```

First WSGI sample app
---------------------
Next, let's try mod_wsgi actually works. Create a sub directory inside the folder where apache2's configuration file ```httpd.conf``` is saved. Let's call it ```my-sites```.
```
mkdir my-sites
```
The create file inside this folder, let's call it ```100-test.conf```. Add following content to it:
```apache
Listen 8088

<VirtualHost *:8088>

    <Directory /opt/python/sample-app/>
        Options Indexes FollowSymLinks
        AllowOverride All
        Require all granted
    </Directory>

    WSGIScriptAlias / /opt/python/sample-app/application.py
    SetEnv MYTEST abc
</VirtualHost>
```

Then create the python file as ```/opt/python/sample-app/application.py```, and add following content to it:
```python
import os
import sys

def application(environ, start_response):
    status = '200 OK'
    buf = []
    for k in sorted(environ.keys()):
        if isinstance(environ[k], basestring):
            buf.append('{}\t{}\n'.format(k, environ[k]))
    buf.append('-' * 80)
    for k in sorted(os.environ.keys()):
        buf.append('{}\t{}\n'.format(k, os.environ[k]))
    buf.append('-' * 80)

    buf.append('{}\t{}\n'.format('sys.argv[0]', sys.argv[0]))

    for i, p in enumerate(sys.path):
        buf.append('{}\t{}\n'.format('path %d' % i, p))

    buf.append('{}\t{}\n'.format('os.__file__', os.__file__))

    try:
        import flask
        buf.append('{}\t{}\n'.format('flask.__file__', flask.__file__))
    except Exception, e:
        buf.append('{}\t{}\n'.format('import error', e))

    output = '\n'.join(buf)

    response_headers = [('Content-type', 'text/plain'),
                    ('Content-Length', str(len(output)))]
    start_response(status, response_headers)

    return [output]
```

Next, let's modify ```httpd.conf``` to include configuration file for our sample app.
```
Include etc/apache2/my-sites/100-test.conf
```

We need to reload apache2 now, run:
```
sudo apachectl graceful
```

Now open a browser and go to ```localhost:8088```, you should be able to see our sample app is working.

Deploy your real app
--------------------
Our sample app is up and running. Let's see what we need to deploy a real app. A real app may need to use 3rd party libraries and often need to connect to database. We don't want to save configuration in a file but want to pass such information to our app as environment variables.  

### Create a new configuration for real app
Let's create it as ```/opt/local/etc/apache2/my-sites/900-real-app.conf```. The content should be similar to the sample one. Just make sure the file path is correct.


### Python library search path
Suppose your packages are not saved in the default search path of python, you can tell mod_wsgi where to find them by adding follow line to your configuration file:
```
WSGIPythonPath /path/to/my-app/venv/lib/python2.7/site-packages/
```
Please note directive ```WSGIPythonPath``` uses server config context, so you can't added it inside a ```VirtualHost```, you must add it to the root level. (Perhaps you want to add it directly into ```httpd.conf```)

```SetEnv``` can be used to pass environment variables into app. However, your app needs to copy them from ```environ``` into ```os.environ```. ([see more details in]()). An alternative is to use ```envvars``` which is in the same location as apache binaries. To find it, run:
 ```
 port notes apache2
 ```
 We can see it is in: ```/opt/local/sbin/```. Edit that file and add following line:
 ```
 source /path/to/my-app/env

 ```

Then you can define that file and add all your environment variables to be passed in that file. Once this is done, you must restart apache server. Reloading won't work.
```
apachectl stop
apachectl start
```

Reload sample-app. You should be able to see environment variables passed in.

### Change Database Entity ownerships
One last thing, if your app uses database, you have to make sure the connected user of Apache has enough privileges to do database operations. You can find that is the user log used by apache either by checking its configuration or check sample app output. You can then create a database and grant user proper privileges. 

### Using https
Create following files and then include them in your application's configuration. 
- ```wsgi.conf```
```apache
WSGIPAssAuthorization On
```

- ```ssl_rewrite.conf```
```apache
RewriteEngine On
RewriteCond %{SERVER_PORT} !^443$
RewriteRule ^(.*)$ https://%{HTTP_HOST}$1 [R=301,L]
```

- ```ssl.conf```
```apache
SSLEngine on
SSLCertificateFile /path/to/my-cert/server.crt
SSLCertificateKeyFile /path/to/my-cert/server.key
SSLCACertificateFile /path/to/my-cert/intermediate.crt
```

Finally check your apache's configuration to make sure it will listen to port 443. 