# Relational Database Access From Custom Scripts 

## Overview 
  Sometimes, it is necessary from custom scripts to access data in a relational database. This tutorial covers 
the two main ways in which it can be done.


## Installing the database driver using pip 
  This option can be used if and only if the python package being used does not rely on `cython` , meaning it must 
be a pure python package. An example of a python database driver which won't work with `jython` is `psycopg2`which 
is a postgresql python driver. Unfortunately, it does depend on `cython`, so it won't work with `jython`.
Follow the instructions [here](../admin-guide/custom-script.md#using-pip-to-install-additional-python-packages) in order to install any package/ database driver using pip.


## Installing and using a JDBC driver

 Since the custom scripts run on `jython` , it's possible to use Java libraries , and the standard way of 
 accessing databases in java is JDBC. You can read more about it [here](https://en.wikipedia.org/wiki/Java_Database_Connectivity).
 In addition, `jython` comes with `zxJDBC` which is a a bridge between python and JDBC. It provides a near 100% compatible Python 
 DB 2.0 interface. It's either that or , directly using the JDBC driver if one is acquainted to Java and JDBC. 
 First , download the driver jar (or jars) from the database vendor or third party. After that , do the following:
1. Copy the jar or jars for the driver into `/opt/gluu/jetty/oxauth/custom/libs/`

1. Open the file `/opt/gluu/jetty/oxauth/webapps/oxauth.xml`. If necessary uncomment the line with the option
`<Set name="extraClassPath"></Set>`

1. Add the path to the jar or jars for the JDBC driver to said option. E.g.
    `<Set name="extraClassPath">/opt/gluu/jetty/mysql-connector-java-8.0.18.jar</Set>` in the case of the mysql driver. 

1. Restart `oxauth`


  Now, in order to use xzJDBC , use this line in your python code 
  `from com.ziclix.python.sql import zxJDBC`

  In order to connect to a database , use this line of code 
  `conn = zxJDBC.connect(jdbcUrl, username, password, driverClass)`
  A short description of the parameters goes as follows:
  - `jdbcUrl` is the url of the datasource. Check the documentation of the JDBC driver you plan to use. An example 
  for mysql would be `jdbc:mysql://localhost/?mydb` where `mydb` is the schema/database to be used by the connection.
  - `username` and `password` are the database authentication credentials 
  - `driverClass` is the name of the driver class for the JDBC driver. Consult the appropriate driver documentation.
  In recent mysql drivers, it will be `com.mysql.cj.jdbc.Driver`.

  Once connected , a query can be issued as follows 
  ```
  cursor = conn.cursor()
  params = ("admin","admin")
  cursor.execute("SELECT id FROM my_table WHERE username=? AND password=?",(params))
  user = cursor.fetchone()
  ``` 

  ## Further references 

  More about the JDBC documentation can be found [here](https://www.jython.org/jython-old-sites/archive/21/docs/zxjdbc.html).