# Create Database
*CREATE DATABASE — create a new database*

## Synopsis

```
CREATE DATABASE name
    [ [ WITH ] [ OWNER [=] user_name ]
           [ TEMPLATE [=] template ]
           [ ENCODING [=] encoding ]
           [ LOCALE [=] locale ]
           [ LC_COLLATE [=] lc_collate ]
           [ LC_CTYPE [=] lc_ctype ]
           [ TABLESPACE [=] tablespace_name ]
           [ ALLOW_CONNECTIONS [=] allowconn ]
           [ CONNECTION LIMIT [=] connlimit ]
           [ IS_TEMPLATE [=] istemplate ] ]
```

## Description

`CREATE DATABASE` creates a new PostgreSQL database.

To create a database, you must be a superuser or have the special `CREATEDB` privilege. See [CREATE ROLE](https://www.postgresql.org/docs/current/sql-createrole.html).

By default, the new database will be created by cloning the standard system database `template1`. A different template can be specified by writing `TEMPLATE` **name**. In particular, by writing `TEMPLATE` `template0`, you can create a pristine database (one where no user-defined objects exist and where the system objects have not been altered) containing only the standard objects predefined by your version of PostgreSQL. This is useful if you wish to avoid copying any installation-local objects that might have been added to `template1`.

## Parameters

* **name**
The name of a database to create.

* **user_name**
The role name of the user who will own the new database, or `DEFAULT` to use the default (namely, the user executing the command). To create a database owned by another role, you must be a direct or indirect member of that role, or be a superuser.

* **template**
The name of the template from which to create the new database, or `DEFAULT` to use the default template (`template1`).

* **encoding**
Character set encoding to use in the new database. Specify a string constant (e.g., `'SQL_ASCII'`), or an integer encoding number, or `DEFAULT` to use the default encoding (namely, the encoding of the template database). The character sets supported by the PostgreSQL server are described in [Section 23.3.1](https://www.postgresql.org/docs/current/multibyte.html#MULTIBYTE-CHARSET-SUPPORTED). See below for additional restrictions.

* **locale**
This is a shortcut for setting `LC_COLLATE` and `LC_CTYPE` at once. If you specify this, you cannot specify either of those parameters.


* **Tip**

The other locale settings [lc_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MESSAGES), [lc_monetary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY), [lc_numeric](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC), and [lc_time](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-TIME) are not fixed per database and are not set by this command. If you want to make them the default for a specific database, you can use ALTER DATABASE ... SET.

* **lc_collate**
Collation order `(LC_COLLATE)` to use in the new database. This affects the sort order applied to strings, e.g., in queries with `ORDER BY`, as well as the order used in indexes on text columns. The default is to use the collation order of the template database. See below for additional restrictions.

* **lc_ctype**
Character classification `(LC_CTYPE)` to use in the new database. This affects the categorization of characters, e.g., lower, upper and digit. The default is to use the character classification of the template database. See below for additional restrictions.

* **tablespace_name**
The name of the tablespace that will be associated with the new database, or `DEFAULT` to use the template database's tablespace. This tablespace will be the default tablespace used for objects created in this database. See [CREATE TABLESPACE](https://www.postgresql.org/docs/current/sql-createtablespace.html) for more information.

* **allowconn**
If false then no one can connect to this database. The default is true, allowing connections (except as restricted by other mechanisms, such as `GRANT/REVOKE CONNECT`).

* **connlimit**

How many concurrent connections can be made to this database. -1 (the default) means no limit.

* **istemplate**
If true, then this database can be cloned by any user with `CREATEDB` privileges; if false (the default), then only superusers or the owner of the database can clone it.

Optional parameters can be written in any order, not only the order illustrated above.

## Notes

`CREATE DATABASE` cannot be executed inside a transaction block.

Errors along the line of “could not initialize database directory” are most likely related to insufficient permissions on the data directory, a full disk, or other file system problems.

Use [DROP DATABASE](https://www.postgresql.org/docs/current/sql-dropdatabase.html) to remove a database.

The program [createdb](https://www.postgresql.org/docs/current/app-createdb.html) is a wrapper program around this command, provided for convenience.

Database-level configuration parameters (set via [ALTER DATABASE](https://www.postgresql.org/docs/current/sql-alterdatabase.html)) and database-level permissions (set via [GRANT](https://www.postgresql.org/docs/current/sql-grant.html)) are not copied from the template database.

Although it is possible to copy a database other than template1 by specifying its name as the template, this is not (yet) intended as a general-purpose `“COPY DATABASE”` facility. The principal limitation is that no other sessions can be connected to the template database while it is being copied. `CREATE DATABASE` will fail if any other connection exists when it starts; otherwise, new connections to the template database are locked out until `CREATE DATABASE` completes. See [Section 22.3](https://www.postgresql.org/docs/current/manage-ag-templatedbs.html) for more information.

The character set encoding specified for the new database must be compatible with the chosen locale settings (`LC_COLLATE` and `LC_CTYPE`). If the locale is C (or equivalently `POSIX`), then all encodings are allowed, but for other locale settings there is only one encoding that will work properly. (On `Windows`, however, `UTF-8` encoding can be used with any locale.) `CREATE DATABASE` will allow superusers to specify `SQL_ASCII` encoding regardless of the locale settings, but this choice is deprecated and may result in misbehavior of character-string functions if data that is not encoding-compatible with the locale is stored in the database.

The encoding and locale settings must match those of the template database, except when `template0` is used as template. This is because other databases might contain data that does not match the specified encoding, or might contain indexes whose sort ordering is affected by `LC_COLLATE` and `LC_CTYPE`. Copying such data would result in a database that is corrupt according to the new settings. `template0`, however, is known to not contain any data or indexes that would be affected.

The `CONNECTION LIMIT` option is only enforced approximately; if two new sessions start at about the same time when just one connection `“slot”` remains for the database, it is possible that both will fail. Also, the limit is not enforced against superusers or background worker processes.

## Examples

To create a new database:

`CREATE DATABASE lusiadas;`

To create a database sales owned by user salesapp with a default tablespace of salesspace:

`CREATE DATABASE sales OWNER salesapp TABLESPACE salesspace;`

To create a database music with a different locale:

```
CREATE DATABASE music
    LOCALE 'sv_SE.utf8'
    TEMPLATE template0;
```
In this example, the `TEMPLATE` `template0` clause is required if the specified locale is different from the one in `template1`. (If it is not, then specifying the locale explicitly is redundant.)

To create a database `music2` with a different locale and a different character set encoding:

```
CREATE DATABASE music2
    LOCALE 'sv_SE.iso885915'
    ENCODING LATIN9
    TEMPLATE template0;
```

The specified `locale` and encoding settings must match, or an error will be reported.

Note that locale names are specific to the operating system, so that the above commands might not work in the same way everywhere.

## Compatibility

There is no `CREATE DATABASE` statement in the `SQL standard`. Databases are equivalent to catalogs, whose creation is implementation-defined.

## See Also

[ALTER DATABASE](https://www.postgresql.org/docs/current/sql-alterdatabase.html), [DROP DATABASE](https://www.postgresql.org/docs/current/sql-dropdatabase.html)

[PostgreSQL Official Documentation](https://www.postgresql.org/docs/current/sql-createdatabase.html)