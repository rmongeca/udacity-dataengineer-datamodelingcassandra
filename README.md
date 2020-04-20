# Data Modeling with Cassandra

Second data modeling project in the context of Udacity's Data Engineering
Nanodegree.

In this project, we build a Cassandra DB for a mock startup called
***Sparkify*** for its music streaming app. The idea is to build a database to
help the company perform song play analysis.

We will design the database, implement it and build an ETL pipeline using
Python, loading mock play event history data to perform test queries.

## Database design

As No-SQL DB design highly depends on the queries we want to execute, the
company provides a list of the queries they will perform:

1. Give me the artist, song title and song's length in the music app history
    that was heard during  sessionId = 338, and itemInSession  = 4

2. Give me only the following: name of artist, song (sorted by itemInSession)
    and user (first and last name) for userid = 10, sessionid = 182

3. Give me every user name (first and last) in my music app history who
    listened to the song 'All Hands Against His Own'

Given that the three queries above do not share a single WHERE clause
structure, we decide to create different tables for every query. As such, we
will create three tables:

- **event_history** to hold the data of the different session items,
    partitioning by sessionId and clustering by itemInSession. This table will
    serve **Query1**
```sql
SELECT artist_name, song_title, song_length
FROM event_history
WHERE sessionID=338
    AND itemInSession=4
```

- **user_history** to hold the data of the different session items,
    partitioning by userId and clustering by sessionId and itemInSession. This
    table will serve **Query2**:
```sql
SELECT artist_name, song_title, user_first_name, user_last_name
FROM user_history
WHERE userId=10
    AND sessionID=182
```

- **song_history** to hold the data of the different songs played by user in a
    given session, partitioning by song_name and clustering by userId,
    sessionId and itemInSession. This table will serve **Query3**:
```sql
SELECT user_first_name, user_last_name
FROM song_history
WHERE song_name='All Hands Against His Own'
```

## ETL design

To fill our DB with mock data from the event data files, we first create a new
CSV file which will contain all the event data to use in the insertion process,
creating the file **event_datafile_new.csv**.

Afterwards, for each table we have to fill we will iterate over the lines of
the CSV (**event_datafile_new.csv**) and perform *INSERT INTO ### VALUES ###*
statements for each row. To keep the code in the associated Notebook simple,
we decide not to include *ON CONFLICT* clauses, also because our mock data
does not have non-unique rows (with the considerd primary keys).


## Project structure

The project is divided in three parts:

- Jupyter Notebook *Project_1B_ Project_Template.ipynb* which contains all the
    code to be executed to process the event data files, connect to the DB,
    create tables, insert the data and test the queries.

- Event data folder which holds the mock event data, CSV files, which we
    process to get a new single CSV file *event_datafile_new.csv*.

- Miscellaneous files, such as the *images* directory with example of the
    processed CSV event file, or repository files such as README and gitignore
    files.

## Project run instructions

To run the project, follow the steps:

1. Make sure the Cassandra deamon/service is running with correct access and
    permissions to create a new keystore.

2. Open the **Project_1B_ Project_Template.ipynb** Jupyter Notebook and
    execute the different code cells.
