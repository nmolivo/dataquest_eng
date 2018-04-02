Have you ever taken an online course and wondered why it didn't all stick?
------
Here's how to get DataQuest's Data Engineering Track missions' content to work on your localhost.
Using data from my <a href = "https://github.com/nmolivo/valencia-data-projects/tree/master/valenbisi">Valenbisi ARIMA modeling project</a>, I will walk through steps using PostgreSQL, Postico, and the Command Line to get our DataQuest exercises running out of a Jupyter Notebook. 

This will not be a repeat of the many resources I used, so be sure to look out for any links I include if it seems I've skipped a few steps.

### Getting started with PostgreSQL and Postico:
PostgreSQL <a href = "https://www.postgresql.org/download/">download</a>
Postico <a href = "https://eggerapps.at/postico/">download</a>

<b>1. Configure Postgres</b><br>
I found <a href = "https://www.codementor.io/engineerapart/getting-started-with-postgresql-on-mac-osx-are8jcopb">this source</a> incredibly helpful.<br>
It walks through installation, creating users, and connecting to a local database.<br><br>

To access the CLI, where you create users, manage permissions, and create your first table: click the database created, "valenbisi2018", for this example.
<img src = ""></img><br>
<i>Each line will start with</i> `valenbisi2018#=`<br><br>

Here are the points I found challenging, so they are documented here.<br>
<b>1a. How to fill a database with a csv file:</b><br>
```
\copy vbstatic(id,update,available,free,name,long,lat,total) 
FROM '~/Documents/Repos/data_quest_data_eng/postgres_mission/vb_table.csv' 
DELIMITER ',' 
CSV HEADER
```
Note that I use `\copy`, not `COPY`<br>
<i>"The syntax for \COPY is slightly different: (a) being a psql command, it is not terminated by a semicolon (b) file paths are relative the current working directory."</i><br><Br>
Source: One of the answers to <a href = "https://stackoverflow.com/questions/16618299/postgres-copy-from-csv-file-no-such-file-or-directory">this StackOverflow Question</a>, which linked to <a href="https://wiki.postgresql.org/wiki/COPY">here.</a><br><br>

<b>1b. How to give permissions to your user [vbuser]</b>
```
GRANT SELECT
ON ALL TABLES IN SCHEMA public
TO vbuser;
```
<Br>
Source: <a href="https://www.digitalocean.com/community/tutorials/how-to-use-roles-and-manage-grant-permissions-in-postgresql-on-a-vps--2#how-to-grant-permissions-in-postgresql">How to Grant Permissions in PostgreSQL</a><br><Br>

Alright, now you're ready to follow along in my first Jupyter Notebook, <a href="">01_postgres_mission</a>
