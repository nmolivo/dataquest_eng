<a href = "https://www.dataquest.io/home">DATAQUEST</a>'s Become a Data Engineer: Data Pipelines
------
Here's how to get DataQuest's Data Engineering Track missions' content to work on your localhost.
Using data from my <a href = "https://github.com/nmolivo/valencia-data-projects/tree/master/valenbisi">Valenbisi ARIMA modeling project</a>, I will walk through steps using PostgreSQL, Postico, and the Command Line to get our DataQuest exercises running out of a Jupyter Notebook. 

This will not be a complete repitition of the many resources I used, so be sure to look out for any links I include if it seems I've skipped a few steps.

Important notes: 
1. In DataQuest, each exercise re-initiates the connection and cursor class of `psycopg2` when interacting with the Postgres DB, with no deliberate closing of the connection. When we productionize our scripts, it will be more efficient and correct to use a `with` statement, which will close the connection once the operations are complete. For the sake of the exercises, I will follow DataQuest's format. I will switch to the `with` statement as we approach production.<br>
2. If there is something that I do not wish to perform on my database, I still complete the exercises, but just leave the code as markdown, rather than executing it. DataQuest does a great job of forshadowing future concepts in earlier lessons, so in cases like these, I may conquer the task in the earlier lesson rather than the later. For example, in mission 2, about creating tables, I use SQLAlchemy to write to a table using a Pandas DataFrame object. Because I've already completed my desired operation in Mission 2, I do not re-execute code which would re-do the task when it is covered in more detail in Mission 4.
3. All projects will contain fully executable code.

This directory will contain the following:
  * Building a Data Pipeline
    * Functional Programming
    * Pipeline Tasks
    * Building a Pipeline Class
    * Multiple Dependency Pipeline
    * Guided Project: Hackernews Pipeline

For Non-Commercial Use Only
------
I highly reccommend participating in this course as a member of DATAQUEST.
