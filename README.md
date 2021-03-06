Require SQL Where Clause
========================

`safeupdate` is a simple extension to PostgreSQL that raises an error if
`UPDATE` and `DELETE` are executed without specifying conditions.  This
extension was initially designed to protect data from accidental obliteration of
data that is writable by [PostgREST].

Installation
------------

Build from source using

    gmake
    gmake install

Or from [PGXN]

    pgxn install safeupate

Activate per-session by running

    load 'safeupdate';

Make this mandatory for all databases and connections by adding the following to
`postgresql.conf`:

    shared_preload_libraries=safeupdate

Or enable for a specific database using

    ALTER DATABASE mydb SET session_preload_libraries = 'safeupdate';

Examples
--------

Try to update records without `WHERE` clause

    :::sql
    UPDATE FROM rack SET fan_speed=70;
    -- ERROR:  UPDATE requires a WHERE clause

Select results from a CTE that attempts to modify data:

    :::sql
    WITH updates AS (
      UPDATE rack SET fan_speed=70
      RETURNING *
    )
    SELECT * FROM updates;
    -- ERROR:  UPDATE requires a WHERE clause

Set a column value for a range of records

    :::sql
    UPDATE rack SET fan_speed=90 WHERE fan_speed=70;

Set a column value for all the records in a table

    :::sql
    UPDATE rack SET fan_speed=90 WHERE 1=1;

Options
-------

Once loaded this extension can be administratively disabled by setting

    :::sql
    SET safeupdate.enabled=0;


Requirements
------------

* PostgreSQL 9.6

News
----

A release history as well as features in the upcoming release are covered in the
[NEWS] file.

[NEWS]:  https://raw.githubusercontent.com/eradman/pg-safeupdate/master/NEWS
[PostgREST]: http://postgrest.com
[PGXN]: http://pgxn.org
