1.

In order to load our file, we first made a docker neo4j docker container.
Then we ran it with username: neo4j and password: 123.
Then we downloaded the file, extracted it and copied it to the container with this command:
docker cp csvfile.csv neo4j:/var/lib/neo4j/import.

Then we could in the end load our csv file into neo4


```
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
```

match(n:Tweet) set n.tweetContent = replace(n.tweetContent, "@ ", "@" ) return n;

MATCH (n:Tweet) return extract( m in 
                filter(m in split(n.tweetContent," ") where m starts with "@" and size(m) > 1) 
                | right(m,size(m)-1))
                as mentions, n.userName as postedBy

LOAD CSV FROM 'file:///Tweets.csv' AS line
CREATE (:Tweets { mentions: line[0], postedBy: line[1]})

match(n:Tweets{postedBy:"postedBy"}) delete n

MATCH (a:Tweet),(b:Tweets)
WHERE a.userName = "Bridgette Parlma" AND b.postedBy = "Bridgette Parlma"
CREATE (a)-[r:Tweeters]->(b)
RETURN r

MATCH (a:Tweet)
WITH point({ longitude: toFloat(a.longitude), latitude: toFloat(a.latitude)}) AS aPoint,
    point({ longitude: 12.5700724, latitude: 55.6867243}) as cph, a
WITH round(distance(aPoint, cph)) / 1000 as distance, a
ORDER BY distance DESC
RETURN DISTINCT distance
