# kix18sync

To provide simple tools to import data into KIX18 by using its REST-API.

## kix18.DBSync

Provides a client for importing data from a remote DB to KIX18 REST-API, supporting Contact and Organisation (so far).


### Contents
- directory `bin`: scripts for data synchronization
- directory `config`: default configuration


### Requirements

#### Perl-Packages and Environment Settings

The script has been developed using CentOS8 as target plattform. Following packages must be installed:

- CentOS/RHEL
```
shell> sudo yum install perl-Config-Simple perl-Text-CSV perl-REST-Client perl-JSO perl-LWP-Protocol-https perl-DBI perl-URI perl-Pod-Usage perl-Getopt-Long
```
- Ubuntu/Debian
```
shell> sudo apt install libconfig-simple-perl libtext-csv-perl librest-client-perl libjson-perl liblwp-protocol-https-perl libdbi-perl liburi-perl perl-doc libgetopt-long-descriptive-perl
```


Depending on the DBMS to be connected, additional packages might be required, e.g.
- CentOS
```
shell> sudo yum install perl-DBD-Pg
shell> sudo yum install perl-DBD-MySQL
shell> sudo yum install perl-DBD-ODBC
```
- Ubuntu/Debian
```
shell> sudo apt install libdbd-pg-perl
shell> sudo apt install libdbd-mysql-perl
shell> sudo apt install libdbd-odbc-perl
```

### Configuration

The major configuration has to be placed in a separate config file which is read upon script execution. A sample config might look like this:

```
# KIXAPI configuration
[KIXAPI]
KIXUserName        = "API-User"
KIXPassword        = "API-User-Password"
KIXURL             = http://localhost:20000
Proxy              = ""
APITimeOut         = 30

# DB configuration
[DB]
# DSN is the full DB connection string without username/password
#DSN         = "DBI:mysql:database=MyCRMDB;host=mariadb.server.local;"
#DSN         = "DBI:ODBC:MyODBCDBName"
DSN         = "DBI:Pg:dbname=kix17;host=kix.company.de;"
DBUser      = "SomeDBUser"
DBPassword  = "PASSWORD"

# limit might be useful for testing (MySQL/MariaDB or PostgreSQL)...
DBLimit     = "100"

# following sections define the mapping of DB-tables to KIX-API resources
# Table        = "some_customer_user_table"
# Condition is optional and allows to filter relevant DB entries
# Condition    = "WHERE login != ''"
# OtherRessourceAttribute = "DB column name"
# if an attribute is not given in the DB-table it may be set to a fixed value by
# SomeRessourceAttribute = "SET:<fixedvaluehere>"

# Mapping configuration for contact items...
[Contact]
Table        = "some_customer_user"
Condition    = ""
# use condition if you want to sync. only newer entries, e.g.
# Condition    = " create_time > (current_timestamp - 86400)"
Login        = "login"
# use custom row name if a DB row should be used in multiple attributes
# Login        = "email0 AS login"
Email        = "email0"
Firstname    = "first_name"
Lastname     = "last_name"
Title        = "title"
Street       = "addr_street"
City         = "addr_city"
Zip          = "addr_zip"
Country      = "addr_country"
Phone        = "phone1"
Mobile       = "phone2"
Fax          = "fax1"
Comment      = "businessfnct"
PrimaryOrgNo = "customer_id"
ValidID      = "SET:1"

# Mapping configuration for organisation items...
[Organisation]
Table        = "some_org"
Condition    = ""
Number       = "customer_id"
Name         = "name_org"
Comment      = "comments"
Street       = "addr_street"
City         = "addr_city"
Zip          = "addr_zip"
Country      = "addr_country"
Url          = "url"
ValidID      = "SET:1"
```


### Data Synchronization

`./bin/kix18.DBSync.pl --config ./config/kix18.DBSync.cfg --ot Contact|Organisation`

The script can be used by referring to a configuration and object type only. Any parameter given by command line overwrites values specified in the config file. Use `kix18.DBSync.pl --help` for a detailed parameter listing.

- `config`: path to configuration file instead of command line params
- `ot`: object to be imported (Contact|Organisation)
- `url`: URL to KIX backend API (e.g. https://t12345-api.kix.cloud)
- `u`: KIX user login
- `p`: KIX user password
- `du`: DBUser  (if not given by config)
- `dp`: DBPassword (if not given by config)
- `verbose`: makes the script verbose
- `help`: show help message
