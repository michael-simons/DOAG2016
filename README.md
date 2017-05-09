# Database centric applications with Spring Boot and jOOQ

**Important:** _The commits and commit messages have been written very carefully to guide you through reading this project and understanding the talk. Please take your time to study them if you don't understand something_.

## Slides

The slides are inside this repository and also on Speakerdeck:

[Datenbankzentrische Anwendungen mit Spring Boot und jOOQ](https://speakerdeck.com/michaelsimons/datenbankzentrische-anwendungen-mit-spring-boot-und-jooq)

## Series of blog posts

I have created a series of blog posts around this project, make sure to check them out:

1. [Database centric applications with Spring Boot and jOOQ](http://info.michael-simons.eu/2016/10/28/database-centric-applications-with-spring-boot-and-jooq/)
2. [Create a Oracle Database Docker container for your Spring Boot + jOOQ application](http://info.michael-simons.eu/2016/10/30/create-a-oracle-database-docker-container-for-your-spring-boot-jooq-application/)
3. [Take control of your development databases evolution](http://info.michael-simons.eu/2016/10/31/take-control-of-your-development-databases-evolution/)
4. [An HTTP api for analytic queries](http://info.michael-simons.eu/2016/11/02/an-http-api-for-analytic-queries/)
5. [Oracle JET: JavaScript components for mere mortals?](http://info.michael-simons.eu/2016/11/14/oracle-jet-javascript-components-for-mere-mortals/)

Those posts cover the talk and the project in great detail. Please provide feedback and a star, if you like them.

## Talk at DOAG 2016

_Note: The talk and the slides are in German as DOAG con is a German conference, the demo, the readme and other artifacts inside this repo are in English in hope they are useful to a broader audience._

This repository contains the talk, including my notes and the complete demo, I've created for [DOAG Konferenz + Ausstellung](https://2016.doag.org/de/home/) in Nürnberg.

The basic idea of this demo is to create a "cloud native" app based on [Spring Boot](http://projects.spring.io/spring-boot) and then using [jOOQ](http://www.jooq.org) as "database first", SQL-centric approach to your database.

Functional wise the application deals with a simple database model storing the names, artists, genres and albums of tracks I listened to the last years. Those data in full comes from my daily foto project [Daily Fratze](https://dailyfratze.de) that I've been running this year for more than 12 years.

The application demonstrates the value of jOOQ when it comes to analysis of data, an area for which ORMs like hibernate weren't designed (see comment by Gavin King on ["What ORMs have taught me: just learn SQL"](https://www.reddit.com/r/programming/comments/2cnw8x/what_orms_have_taught_me_just_learn_sql/cjheyec). If you just deal with simple inserts, updates and deletes during OLTP, you're mostly fine using ORMs like JPA, even problems like the _n+1_ query problems are known and often solved.

But if you want to use [modern sql](https://modern-sql.com) and ["the index"](http://use-the-index-luke.com), to use powerful, analytic functions or if you have to deal with a database model that is a less than optimal fit for an ORM, than jOOQ will help you.

## Sample requests

Part of the application is in actual use (the schema and some of the queries) at [Daily Fratze](https://dailyfratze.de). You can implement your own scrobble application. This version supports the following requests for now

```
## Get all artists
curl -X "GET" "http://127.0.0.1:8080/api/artists"
```

```
## Get top n albums by some artists
curl -X "GET" "http://127.0.0.1:8080/api/artists/54,86/topNAlbums"
```

```
## Get top n tracks by some artists Duplicate
curl -X "GET" "http://127.0.0.1:8080/api/artists/54,86/topNTracks"
```

```
## Get cumulative plays by some artists
curl -X "GET" "http://127.0.0.1:8080/api/artists/54,86/cumulativePlays"
```

```
## Get charts for a month
curl -X "GET" "http://127.0.0.1:8080/api/charts/2016/5?n=40"
```

## Creating and running the demo database

As DOAG Konferenz is an Oracle database centric conference, I used Oracle throughout the demo and prepared some docker scripts to get you up and running quickly.

1. Go to [docker.com](http://www.docker.com/products/docker) and install docker on your machine
2. Download [Oracle Database 12c Release 2 Standard Edition 2 for Linux](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html)
3. Go to [Oracles docker images repository](https://github.com/oracle/docker-images/tree/master/OracleDatabase), download the _OracleDatabase_ scripts and follow the instructions. Remember to add the two files from step 2 to "dockerfiles/12.2.0.1". I used the following command: `./buildDockerImage.sh -v 12.2.0.1 -s`
4. Then, inside this repository, create a running container of this image with `mvn docker:start`. The first start will take a while. The reason behind this is that the Oracle database files are created during the first start and not during image creation. The files are storted inside `${project.basedir}/var`.  There's a timeout of 30 minutes. You may have to change this if you are on a slower machine
5. The container exposes the following ports to localhost: 1521, 5500 and 5501. The first for SQL*Plus, the later ones for the Oracle Enterprise Manager

You can access the Enterprise manager express for the root container at [https://localhost:5500/em](https://localhost:5500/em) and the container ORCLPDB1 at [https://localhost:5501/em](https://localhost:5501/em).

Use SQL*Plus to interact with the database. The admin password is a password generated during first startup and you can find it inside the container logs. The _doag2016_ account can be used with `sqlplus doag2016/doag2016@//localhost:1521/ORCLPDB1`, the password is _doag2016_, too.

## Configuring the Oracle Maven Repository

This demo uses the Oracle JDBC driver from the official [Oracle Maven Repository](http://www.oracle.com/webfolder/application/maven/index.html) under those coordinates:

```
<dependency>
	<groupId>com.oracle.jdbc</groupId>
	<artifactId>ojdbc8</artifactId>
</dependency>
<dependency>
	<groupId>com.oracle.jdbc</groupId>
	<artifactId>orai18n</artifactId>
</dependency>
```

Please follow those [instructions from Oracle](http://docs.oracle.com/middleware/1213/core/MAVEN/config_maven_repo.htm#MAVEN9010) to make this work for your Maven installation.

## Installing jOOQ Pro

This demo uses an Oracle Standard Edition 2 database, so it needs a jOOQ Professional edition to run. You can get a free, 30 days trial edition here: [jOOQ Download](http://www.jooq.org/download/).

## Ressourcen

* [Spring Initializr](http://start.spring.io)
* [jOOQ](http://www.jooq.org)
* [Modern SQL](https://modern-sql.com)
* [Java, SQL and jOOQ](https://blog.jooq.org)
* [Vlad on Hibernate](https://vladmihalcea.com/tutorials/hibernate/)
* [Thoughts on Java](http://www.thoughts-on-java.org/persistence/)
* [Flyway by Boxfuse](https://flywaydb.org)
* [Spring Data JPA](http://projects.spring.io/spring-data-jpa/)
* [Repository Pattern](http://martinfowler.com/eaaCatalog/repository.html)

## License

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons Lizenzvertrag" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">"Database centric applications with Spring Boot and jOOQ"</span> von <a xmlns:cc="http://creativecommons.org/ns#" href="https://github.com/michael-simons/DOAG2016" property="cc:attributionName" rel="cc:attributionURL">Michael J. Simons</a> ist lizenziert unter einer <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Namensnennung - Nicht-kommerziell - Weitergabe unter gleichen Bedingungen 4.0 International Lizenz</a>.

## Imagerights

* "Relentless Persistence": © Curtis Lassam, [Cube Drone](http://cube-drone.com/comics/c/relentless-persistence)
* "Electronic data": © iStockphoto.com/[shironosov](http://www.istockphoto.com/portfolio/shironosov)
* "Magnet und Blanko-Kühlschrank": © iStockphoto.com/[andy0man](http://www.istockphoto.com/de/portfolio/andy0man)
* "Oracle Databases": [Public Domain](https://commons.wikimedia.org/wiki/File:Oracle_Databases.jpg)
* "Young men with tablet": © iStockphoto.com/[boggy22](http://www.istockphoto.com/portfolio/boggy22)