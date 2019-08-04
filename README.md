# Project Summary

### Background
This project is to help a startup called **Sparkify** to understand what songs users are listening to. Sparkify has been collecting on songs and user activity on their new music streaming app. Currently, there is no easy way to query the data to generate the results, since the data reside in a directory of CSV files on user activity on the app. So data engineer needs to create an Apache Cassandra database which can create queries on song play data to answer the questions.
### Datasets
Work with one dataset: `event_data`. The directory of CSV files partitioned by date. Here are examples of filepaths to two files in the dataset:
```
event_data/2018-11-08-events.csv
event_data/2018-11-09-events.csv
```
    
### Tasks
1. **data pre-processing** in _Python_
- iterate through each event file in `event_data` to process and create a new CSV file `event_datafile_new.csv`
2. **data modeling** with NoSQL database _Apache Cassandra_
- design denormalized tables based on analysis needs
- create cluster and kespace
- develop `CREATE` statement for each of the tables to address each business question
3. **buidling ETL pipeline** with _Python_
- develop `INSERT` statements to load processed records into relevant tables in the data model

---
# Files in the repository
- _**data_modeling_with_nosql.ipynb**_ is the file to do data modeling and ETL pipeline
- _**event_datafile_new.csv**_ is the combined csv file
- _**event_data**_ contains original csv files
- _**images**_ stores screenshots of query results
- _**README.md**_ provides discussion on the project

---
# CQL Queries

**Query 1: Give the artist, song title and song's length in the music app history that was heard during sessionId = 338, and itemInSession = 4**
```
CREATE TABLE IF NOT EXISTS music_history_session (
        sessionid int, iteminsession int, artist text, 
        songtitle text, songlength float,
        PRIMARY KEY ((sessionid, iteminsession)))
```
```
INSERT INTO music_history_session (sessionid, 
        iteminsession, artist, songtitle, songlength)
        VALUES (%s, %s, %s, %s, %s)
```
```
SELECT artist, songtitle, songlength 
FROM music_history_session 
WHERE sessionid=338 and iteminsession=4
```

**Query 2: Give the name of artist, song (sorted by itemInSession) and user (first and last name) for userid = 10, sessionid = 182**
```
CREATE TABLE IF NOT EXISTS music_history_user (
        userid int, sessionid int, iteminsession text, 
        artist text, songtitle text, firstname text, 
        lastname text,
        PRIMARY KEY ((userid, sessionid),iteminsession))
```
```
INSERT INTO music_history_user (userid, sessionid, 
        iteminsession, artist, songtitle, firstname, lastname)
        VALUES (%s, %s, %s, %s, %s, %s, %s)
```
```
SELECT artist, songtitle, firstname, lastname 
FROM music_history_user 
WHERE userid=10 and sessionid=182
```

**Query 3: Give the every user name (first and last) in music app history who listened to the song 'All Hands Against His Own'**
```
CREATE TABLE IF NOT EXISTS music_history_song (
        songtitle text, userid int, 
        firstname text, lastname text,
        PRIMARY KEY (songtitle, userid))
```
```
INSERT INTO music_history_song (
        songtitle, userid, firstname, lastname)
        VALUES (%s, %s, %s, %s)
```
```
SELECT firstname, lastname 
FROM music_history_song 
WHERE songtitle='All Hands Against His Own'
```
