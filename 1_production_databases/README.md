<a href = "https://www.dataquest.io/home">DATAQUEST</a>'s Become a Data Engineer: Production Databases
------
Here's how to get DataQuest's Data Engineering Track missions' content to work on your localhost.
Using data from my <a href = "https://github.com/nmolivo/valencia-data-projects/tree/master/valenbisi">Valenbisi ARIMA modeling project</a>, I will walk through steps using PostgreSQL, Postico, and the Command Line to get our DataQuest exercises running out of a Jupyter Notebook. 

This will not be a complete repitition of the many resources I used, so be sure to look out for any links I include if it seems I've skipped a few steps.

Important note: In DataQuest, each exercise re-initiates the connection and cursor class of `psycopg2` when interacting with the Postgres DB, with no deliberate closing of the connection. When we productionize our scripts, it will be more efficient and correct to use a `with` statement, which will close the connection once the operations are complete. For the sake of the exercises, I will follow DataQuest's format. I will switch to the `with` statement as we approach production.

This directory will contain the following:
* <a href = "https://github.com/nmolivo/dataquest_eng/tree/master/1_production_databases">Production Databases</a>
  * Postgres For Data Engineers
    * <a href = "https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/01_intro_postgres.ipynb">Intro to Postgres</a>
    * <a href = "https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/02_opt_tables.ipynb">Creating Tables</a>
    * <a href = "https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/03_manage_tables.ipynb">Managing Created Tables</a>
    * Loading and Extracting Data with Tables
    * User and Database Management
    * Project: Storing Tropical Storm Data
  * Optimizing Postgres Databases
    * Exploring Postgres Internals
    * Debugging Postgres Queries
    * Using an Index
    * Advanced Indexing
    * Vacuuming Postgres Databases

### Getting started with PostgreSQL and Postico (<a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/01_intro_postgres.ipynb">01_intro_postgres</a>):
------
PostgreSQL <a href = "https://www.postgresql.org/download/">download</a><br>
Postico <a href = "https://eggerapps.at/postico/">download</a>

<b>Configure Postgres</b><br>
I found <a href = "https://www.codementor.io/engineerapart/getting-started-with-postgresql-on-mac-osx-are8jcopb">this source</a> incredibly helpful.<br>
It walks through installation, creating users, and connecting to a local database.<br><br>

This Repository will be covering how to do almost all the exercises out of a Jupyter Notebook. However, examples of how to complete some exercises in the CL will also be covered. To access the CLI, where you can create users, manage permissions, and create your first table: click the database created, "valenbisi2018", for this example.<br><br>
<img src = "https://github.com/nmolivo/dataquest_eng/blob/master/images/001_postgres.png?raw=true"></img><br>
In the CLI, each line will start with whatever you named your database, so for me it's `valenbisi2018#=`<br><br>
<b>How to fill a database with a csv file:</b><br>
First create the database:
```
valenbisi2018#= CREATE TABLE vbstatic (id BIGSERIAL PRIMARY KEY, update VARCHAR(255), available INT, 
                                       free INT, total INT, name VARCHAR(255), long NUMERIC, lat NUMERIC);
```
Notice I made column `update` into data type `VARCHAR`. This is because when working with CSVs, DateTime Objects sometimes get converted to strings. Postgres cannot handle data type misgivings, so it was simplest to do this. <a href="https://www.techonthenet.com/postgresql/datatypes.php">Here is a guide</a> to all the different Postgres data types you can encounter.<br><br>
Then fill the database with data from a csv file containing only the columns you created in your table.<br><Br>
```
valenbisi2018#= \copy vbstatic(id,update,available,free,name,long,lat,total) 
FROM '~/Documents/Repos/data_quest_data_eng/postgres_mission/vb_table.csv' 
DELIMITER ',' 
CSV HEADER
```
Note that I use `\copy`, not `COPY`<br>
>The syntax for `\COPY` is slightly different: (a) being a psql command, it is not terminated by a semicolon (b) file 
>paths are relative the current working directory.<br><Br>
>Source: One of the answers to <a href = "https://stackoverflow.com/questions/16618299/postgres-copy-from-csv-file-no-such-file-or-directory">this StackOverflow Question</a>, which linked to <a href="https://wiki.postgresql.org/wiki/COPY">here.</a><br><br>

<b>How to give permissions to your user [vbuser]</b>
```
valenbisi2018#= GRANT SELECT
ON ALL TABLES IN SCHEMA public
TO vbuser;
```
<Br>
Source: <a href="https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps--2#how-to-grant-permissions-in-postgresql">How to Grant Permissions in PostgreSQL</a><br><Br>

Alright, now you're ready to follow along in my first Jupyter Notebook, <a href="https://github.com/nmolivo/dataquest_eng/blob/master/01_intro_postgres.ipynb">01_intro_postgres</a><br>
Some additional notes to keep in mind:
1. Make sure when you are loading in your data using a csv, that all the columns in the csv are in the same order as in your `CREATE TABLE` statement<br>
2. If you need to delete a table, enter your Postgres CLI and type:
```
valenbisi2018#= DROP TABLE table_name;
```
3. To see a description of tables, type into the CLI: `\dt`, short for 'describe tables'<br>
<img src = "https://github.com/nmolivo/dataquest_eng/blob/master/images/002_describetables.png?raw=true"></img><br>

### Optimizing Your Postgres Database (<a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/02_opt_tables.ipynb">02_opt_tables</a>)
------
In this mission we review making tables, datatype selection, and I use SQLAlchemy to write a table from a pandas DataFrame object. This solves the porblem I ran into during the first mission: I no longer need to store my date column `update` as `VARCHAR`. It's now a proper `TIMESTAMP` object.

Datatypes from the PostGres Documentation:

>### Numeric Types
>|Name|Storage Size|Description|Range|
>|------|------|------|------|
>|`smallint`|2 bytes|small-range integer|-32768 to +32767|
>|`integer`|4 bytes|typical choice for integer|-2147483648 to +2147483647|
>|`bigint`|8 bytes|large-range integer|-9223372036854775808 to 9223372036854775807|
>|`decimal`|variable|user-specified precision, exact|up to 131072 digits before the decimal point; up to 16383 digits after the decimal point|
>|`numeric`|variable|user-specified precision, exact|up to 131072 digits before the decimal point; up to 16383 digits after the decimal point|
>|`real`|4 bytes|variable-precision, inexact|6 decimal digits precision|
>|`double precision`|8 bytes|variable-precision, inexact|15 decimal digits precision|
>|`serial`|4 bytes|autoincrementing integer|1 to 2147483647|
>|`bigserial`|8 bytes|large autoincrementing integer|1 to 9223372036854775807|
>
><a href = "https://www.postgresql.org/docs/9.1/static/datatype-numeric.html">Postgres Documentation: Numeric Types</a>
>### Character Types
>|Name|Description|
>|-----|-----|
>|`character varying(n), varchar(n)`|variable-length with limit|
>|`character(n), char(n)`|fixed-length, blank padded|
>|`text`|variable unlimited length|
>
> <a href = "https://www.postgresql.org/docs/9.1/static/datatype-character.html">Postgres Documentation: Character Types</a>
>### Datetime Types
>|Name|Storage Size|Description|Low Value|High Value|Resolution|
>|-----|-----|-----|-----|-----|-----|
>|`timestamp [ (p) ] [ without time zone ]`|8 bytes|both date and time (no time zone)|4713 BC|294276 AD|1 microsecond / 14 digits|
>|`timestamp [ (p) ] with time zone`|8 bytes|both date and time, with time zone|4713 BC|294276 AD|1 microsecond / 14 digits|
>|`date`|4 bytes|date (no time of day)|4713 BC|5874897 AD|1 day|
>|`time [ (p) ] [ without time zone ]`|8 bytes|time of day (no date)|00:00:00|24:00:00|1 microsecond / 14 digits|
>|`time [ (p) ] with time zone`|12 bytes|times of day only, with time zone|00:00:00+1459|24:00:00-1459|1 microsecond / 14 digits|
>|`interval [ fields ] [ (p) ]`|16 bytes|time interval|-178000000 years|178000000 years|1 microsecond / 14 digits|
>
> <a href = "https://www.postgresql.org/docs/9.1/static/datatype-datetime.html">Postgres Documentation: DateTime Types</a>
<br><br>


You will need SQLAlchemy to create an SQL database from a pandas dataframe. The final code, for our example, will look as follows:<br><Br>
```
from sqlalchemy import create_engine
engine = create_engine('postgresql+psycopg2://nmolivo:MYPASSWORD@localhost/valenbisi2018')
data.to_sql('vbstatic', engine, dtype = {'id': sqlalchemy.types.BIGINT, \
                                         'update':sqlalchemy.types.TIMESTAMP(timezone=False), \
                                         'available':sqlalchemy.types.INT, \
                                         'free':sqlalchemy.types.INT, \
                                         'total':sqlalchemy.types.INT, \
                                         'name':sqlalchemy.types.CHAR(length=55), \
                                         'long': sqlalchemy.types.Float(precision=15), \
                                         'lat': sqlalchemy.types.Float(precision=15)})
```
To get this code to compile, I used the following sources:
  - <a href = "http://docs.sqlalchemy.org/en/latest/core/engines.html">To configure the engine:</a> `dialect+driver://username:password@host:port/database`<br>
  - <a href = "http://docs.sqlalchemy.org/en/latest/core/type_basics.html#sql-standard-and-multiple-vendor-types">To create the `to_sql(dtype)` dictionary</a><br>

### Managing Tables (<a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/03_manage_tables.ipynb">03_manage_tables</a>):
------

<b>How to inspect your tables: </b><br>
For this, I am heading back to the command line, rather than the Jupyter notebook. Remember, because my database name is `valenbisi2018`, all lines of code I do in the CL will start with `valenbisi2018=#`. This will help differentiate code snippets I share from the CL vs. from my Jupyter notebook.<br><br>
```
valenbisi2018=# SELECT column_name, data_type
FROM information_schema.columns
WHERE table_name = 'staticvb'
ORDER BY ordinal_position;
```
Will output:<br>
<img src = "https://github.com/nmolivo/dataquest_eng/blob/master/images/003_describetables.png?raw=true"></img>
<br><br>

In this mission, the following concepts are covered:
* Changing table name with `ALTER TABLE current_name RENAME TO new_name`
* Delete variables with `ALTER TABLE table_name DROP COLUMN col_name`
* Renaming variables with `ALTER TABLE table_name RENAME COLUMN current_col_name TO new_col_name`
* Change variable datatype with `ALTER TABLE table_name ALTER COLUMN bigint_col_name TYPE BIGINT`
* Creating new variables and optionally: setting a default value with `ALTER TABLE table_name ADD COLUMN new_date_col DATE DEFAULT 01-01-1991`
* Populating a new variable with information from other variables using `UPDATE table_name SET new_date_col = to_date(col_day || '-' || col_month || '-' || col_year, 'DD-MM-YYYY')`<br>

### Loading and Extracting Data with Tables (<a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/04_extract_data.ipynb">04_extract_data</a>):
------

For Non-Commercial Use Only
------
I highly reccommend participating in this course as a member of DATAQUEST. A summary of the curriculum is outlined below.<br>
* Postgres for Data Engineers
* Optimizing Postgres Databases
* Processing Large Datasets in Pandas
* Optimizing Code performance on Large Datasets
* Algorithms and Data Structures
* Recursion Trees
* Building a Data Pipeline
