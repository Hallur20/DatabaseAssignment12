<h1>Database Assignment 12 </h1>

<h1>Setup <g-emoji class="g-emoji" alias="checkered_flag" fallback-src="https://github.githubassets.com/images/icons/emoji/unicode/1f3c1.png">🏁</g-emoji> </h1>



    
<h1>Exercise 1</h1>

<p>In order to load our file, we first made a docker neo4j docker container.
Then we ran it with username: neo4j and password: 123.
Then we downloaded the file, extracted it and copied it to the container with this command:
    
```bash
docker cp csvfile.csv neo4j:/var/lib/neo4j/import.
```

Then we could in the end load our csv file into neo4 with node name Tweet. Query:</p>

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
<img src="https://github.com/Hallur20/DatabaseAssignment12/blob/master/1.0.png"/>

<img src="https://github.com/Hallur20/DatabaseAssignment12/blob/master/1.1.png"/>

<p>Next we wanted to fix the problem that sometimes a space would be before the mentioned user. Query: </p>

```
match(n:Tweet) set n.tweetContent = replace(n.tweetContent, "@ ", "@" ) return n;
```

<p>After the fix we got the result we wanted. Query:</p>

```
MATCH (n:Tweet) return extract( m in 
                filter(m in split(n.tweetContent," ") where m starts with "@" and size(m) > 1) 
                | right(m,size(m)-1))
                as mentions, n.userName as postedBy
```
<img src="https://github.com/Hallur20/DatabaseAssignment12/blob/master/1.3.png"/>
<h1>Exercise 2</h1>
we took the result from the previous image, and exported it as csv file by clicking on this button:
<img src="https://github.com/Hallur20/DatabaseAssignment12/blob/master/2.0.png"/>
then we copied it to the docker container with this command:

```bash
docker cp Tweets.csv neo4j:/var/lib/neo4j/import
```

<p>then we loaded the csv file into neo4j. Query:</p>

```
LOAD CSV FROM 'file:///Tweets.csv' AS line
CREATE (:Tweets { mentions: line[0], postedBy: line[1]})
```

<p>we decided on making a relation between userName Bridgette Parlma and postedBy Bridgette Parlma.</p>

```
MATCH (a:Tweet),(b:Tweets)
WHERE a.userName = "Bridgette Parlma" AND b.postedBy = "Bridgette Parlma"
CREATE (a)-[r:Tweeters]->(b)
RETURN r
```
<img src="https://github.com/Hallur20/DatabaseAssignment12/blob/master/2.1.png"/>
<img src="https://github.com/Hallur20/DatabaseAssignment12/blob/master/2.2.png"/>

<h1>Exercise 3</h1>

<p>we were unsure of what the exercise was about, so what we ended up doing was just comparing the distance from each tweet, with the school location (nørgaardvej 30), the maximum distance ended up being 1509.863</p>

```
MATCH (a:Tweet)
WITH point({ longitude: toFloat(a.longitude), latitude: toFloat(a.latitude)}) AS aPoint,
    point({ longitude: 12.511735, latitude: 55.770179}) as cph, a
WITH round(distance(aPoint, cph)) / 1000 as distance, a
ORDER BY distance DESC
RETURN DISTINCT distance
```
<img src="https://github.com/Hallur20/DatabaseAssignment12/blob/master/3.0.png"/>
