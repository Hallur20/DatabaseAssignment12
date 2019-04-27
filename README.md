
´´´neo4j
USING PERIODIC COMMIT
LOAD CSV WITH HEADERS FROM "file:///some2016UKgeotweets.csv" AS row 
    FIELDTERMINATOR ";"
    create(tweets:Tweet{id:row["Tweet Id"],date:row.Date, hour:row.Hour,
    userName:row["User Name"], nickName:row.Nickname, bio:row.Bio,
    tweetContent:row["Tweet content"], rts:row.RTs, latitude: row.Latitude,
    longitude: row.Longitude, country: row.Country, place: row["Place (as appears on Bio)"],
    followers: row.Followers, following: row.Following, listed: row.Listed, tweetLanguage: row["Tweet language (ISO 639-1)"], tweetUrl: row["Tweet Url"]
    })
return row
´´´
