<a href = "https://www.dataquest.io/home">DATAQUEST</a>'s Become a Data Engineer: Production Databases
------
Here's how to get DataQuest's Data Engineering Track missions' content to work on your localhost.
Using data from my <a href = "https://github.com/nmolivo/valencia-data-projects/tree/master/valenbisi">Valenbisi ARIMA modeling project</a>, I will walk through steps using PostgreSQL, Postico, and the Command Line to get our DataQuest exercises running out of a Jupyter Notebook. 

This will not be a complete repitition of the many resources I used, so be sure to look out for any links I include if it seems I've skipped a few steps.

Important notes: 
1. In DataQuest, each exercise re-initiates the connection and cursor class of `psycopg2` when interacting with the Postgres DB, with no deliberate closing of the connection. When we productionize our scripts, it will be more efficient and correct to use a `with` statement, which will close the connection once the operations are complete. For the sake of the exercises, I will follow DataQuest's format. I will switch to the `with` statement as we approach production.<br>
2. If there is something that I do not wish to perform on my database, I still complete the exercises, but just leave the code as markdown, rather than executing it. DataQuest does a great job of forshadowing future concepts in earlier lessons, so in cases like these, I may conquer the task in the earlier lesson rather than the later. For example, in mission 2, about creating tables, I use SQLAlchemy to write to a table using a Pandas DataFrame object. Because I've already completed my desired operation in Mission 2, I do not re-execute code which would re-do the task when it is covered in more detail in Mission 4.
3. All projects will contain fully executable code.

This directory will contain the following:
* <a href = "https://github.com/nmolivo/dataquest_eng/tree/master/1_production_databases">Production Databases</a>
  * Postgres For Data Engineers (<a href = "https://www.kaggle.com/egrinstein/20-years-of-games/data">Click here</a> to download data used in these exercies; `ign.csv` from Kaggle)
    * <a href = "https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/01_intro_postgres.ipynb">Intro to Postgres</a>
    * <a href = "https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/02_opt_tables.ipynb">Creating Tables</a>
    * <a href = "https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/03_manage_tables.ipynb">Managing Created Tables</a>
    * <a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/04_extract_data.ipynb">Loading and Extracting Data with Tables</a>
    * <a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/05_db_mgmt.ipynb">User and Database Management</a>
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

<b>Configure Postgres and Postico</b><br>
I found <a href = "https://www.codementor.io/engineerapart/getting-started-with-postgresql-on-mac-osx-are8jcopb">this source</a> incredibly helpful.<br>
It walks through installation, creating users, and connecting to a local database.<br>

1. To create a database, open Postgres, double click the bin called <b>postgres</b>. <Br>
2. Once a Command Line Interface pops up, you can type the following command to create a database:<br>
`CREATE DATABASE db_name;`<br>
3. Next, configure Postico. Open Postico, click <b>New Favorite</b> and the following should pop up:<br>
<img src = "https://github.com/nmolivo/dataquest_eng/blob/master/images/004_postico.png?raw=true"></img><br>
4. Set your password, make sure there are no hyphens in the database name, and click <b>Connect</b><br>
Postico will be helpful when it comes to checking that your code is working. It allows you to see (and edit, but the point of these exercises is to learn to code our edits!) the contents and datatypes of your tables. 

To access the CLI, where you can create users, manage permissions, and create your first table: Go to Postgres and click the database created, "valenbisi2018", for this example.<br><br>
<img src = "https://github.com/nmolivo/dataquest_eng/blob/master/images/001_postgres.png?raw=true"></img><br>
In the CLI, each line will start with whatever you named your database, so for me it's `valenbisi2018#=`<br><br>
<b>How to fill a database with a csv file: (We will cover how to do this using SQLAlchemy in <a href = "https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/02_opt_tables.ipynb">Mission 2</a>, as well as 4 other ways to do it in <a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/04_extract_data.ipynb">Mission 4</a>.)</b><br>
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
                                         'long': sqlalchemy.types.NUMERIC(precision=10, scale = 8, asdecimal = True), \
                                         'lat': sqlalchemy.types.Float(precision=10, scale = 8, asdecimal = True)})
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
<br>After seeing that my final two variables stored as `REAL` I changed them to `NUMERIC` before capturing the next screenshot.<br><br>
<b>You will also be able to inspect your tables using the Postico GUI:</b><br>
If you look to the top left, you can see I changed my connection 'nickname' to <b>local</b> and I am in the <b>valenbisi2018</b> database.<br>
If you look to the side, you see I'm on table <b>vbstatic</b><br>
If you look to the bottom left, you see I'm on <b>Structure</b>, as opposed to </b>Contents</b><br>
<img src = "https://github.com/nmolivo/dataquest_eng/blob/master/images/005_postico.png?raw=true"></img><br><Br>
In this mission, the following concepts are covered:
* Changing table name with `ALTER TABLE current_name RENAME TO new_name`
* Delete variables with `ALTER TABLE table_name DROP COLUMN col_name`
* Renaming variables with `ALTER TABLE table_name RENAME COLUMN current_col_name TO new_col_name`
* Change variable datatype with `ALTER TABLE table_name ALTER COLUMN bigint_col_name TYPE BIGINT`
* Creating new variables and optionally: setting a default value with `ALTER TABLE table_name ADD COLUMN new_date_col DATE DEFAULT 01-01-1991`
* Populating a new variable with information from other variables using `UPDATE table_name SET new_date_col = to_date(col_day || '-' || col_month || '-' || col_year, 'DD-MM-YYYY')`<br>

### Loading and Extracting Data with Tables (<a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/04_extract_data.ipynb">04_extract_data</a>):
------
In this mission we learn three different methods to write to an SQL table from a CSV file.
* Multiple single insert statements
* Multiple mogrify insert 
* Copy expert method

The `copy_expert()` method is the fastest, but when our database gets huge, we'll need to use an SQL command:
```
INSERT INTO ign_reviews (id, score_phrase, title, url, platform, score, genre, editors_choice, release_date)

SELECT id, score_phrase, title_of_game_review, url, platform, score, genre, editors_choice, to_date(release_day || '-' || release_month || '-' || release_year, 'DD-MM-YYYY') as release_date 

FROM old_ign_reviews
```
And as a refresher, here are the different options for the `mode` argument of Python's Built-in Function <b>open()</b>


|Character|Meaning|
|------|------|
|'r'|open for reading (default)|
|'w'|open for writing, truncating the file first|
|'x'|open for exclusive creation, failing if the file already exists|
|'a'|open for writing, appending to the end of the file if it exists|
|'b'|binary mode|
|'t'|text mode (default)|
|'+'|open a disk file for updating (reading and writing)|
|'U'|universal newlines mode (deprecated)|

Source: <a href = "https://docs.python.org/3/library/functions.html#open">Built-In Function Documentation</a>

### User and Database Management (<a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/05_db_mgmt.ipynb">05_db_mgmt</a>):
------
If you've been following along, you may need to delete users in order to keep your db clean and practice these exercises. To drop users, I use the following SQL commands, thanks to <a href = "https://stackoverflow.com/questions/3023583/postgresql-how-to-quickly-drop-a-user-with-existing-privileges">this StackOverflow question:</a><br><Br>
You'll want to reassign any privleges old_user had and drop any privleges before you can remove old_user without encountering an error:<br>
 `REASSIGN OWNED BY old_user TO user;`<br>
 `DROP OWNED BY old_user;`<br>
 `DROP USER old_user;`

### Project: Storing Tropical Storm Data (<a href="https://github.com/nmolivo/dataquest_eng/blob/master/1_production_databases/06_proj_storm.ipynb">06_proj_storm</a>):
------

For Non-Commercial Use Only
------
I highly reccommend participating in this course as a member of DATAQUEST.
