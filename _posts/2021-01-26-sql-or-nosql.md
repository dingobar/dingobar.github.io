---
layout: post
title: DynamoDB or PostgreSQL for microservices?
subtitle: My journey to the land of NoSQL and back
cover-img: /assets/img/path.jpg
thumbnail-img: /assets/img/thumb.png
share-img: /assets/img/path.jpg
tags: [sql, nosql, databases, aws, dynamodb, postgresql]
---

A couple of years ago, I got really excited about the serverless database offering by AWS,
[DynamoDB](https://aws.amazon.com/dynamodb/). It is a NoSQL database, which was (is?) a huge
buzz-word, and the good-old relational databases seemed to have a snowballs chance in hell to keep
up. I have since created microservices using both DynamoDB and
[RDS for PostgreSQL](https://aws.amazon.com/rds/postgresql/) (postgres) databases as backends, which
both excel in certain areas.

A DynamoDB table (DynamoDB has no concept of databases) is defined by a partition key (analogos to a
primary key in the SQL-world), and an optional sort key. The combination of these two keys have to
be unique for each entry, and are by default indexed. There is also the possibility of creating
secondary indexes, albeit they are a bit expensive for what they offer. It has no schema or
datatypes beyond the two key attributes, which are also the only restrictions that one can create.
In other words, DynamoDB forces the developer to validate their data-models in their code before
writing anything to the table.

At first, the thing that lured me into the NoSQL-world was the simplicity. You don't have to define
a strict schema, which is great for prototyping as your schema is likely to evolve quickly in the
beginning. The database also has an SDK for pretty much any language of your choosing. If you are
working with dynamically typed languages like JavaScript or Python, these advantages are probably
even more apparent. Lastly, the SDK communicates with the database over HTTP, which is familiar to
most developers, and significantly more simple and approachable than ODBC or JDBC drivers,
especially for new developers (like myself). Using DynamoDB Streams, you can even pipe your data
straight from the table into a data lake as it comes in, which allows for live analytics down the
line using something like AWS Athena or Presto.

However, as time went on, the sacrifices I made choosing DynamoDB over postgres have been becoming
more and more clear. As your project matures, the flexible data model is unlikely to give you much
other than headache. The code you have written in your app to protect your model also seems to annoy
you more and more. The thing is that static typing still works really well, and database
transactions are really useful. Being able to freely define indexes without extra cost is nice, and
SQL is much nicer than the proprietory query syntax that AWS have defined.

The biggest issue I have with DynamoDB, however, is that it doesn't work well for batch-oriented
analytics workloads. It scales automatically (unless on the free tier), but not fast enough to
accommodate data scientist use (ie. low frequency high volume). I mentioned earlier that I can
stream my entries from DynamoDB to S3 to decouple the database from the data, but the benefits of
doing analytics in that way really only come into play when we have hundrets of new entries every
minute, and this is not the case for most services that I deal with. Alternatively I can set the
minimum or provisioned capacity of the table higher, but that just makes Jeff Bezos richer and costs
the same as a relational database in the end. Furthermore, I can use
[Debezium](https://debezium.io/) and CDC on my postgres database and stream the data into Kafka, and
in that way get the same decoupling of the analytics-data and the service itself as DynamoDB Streams
would give. With S3 or Lambda sinks for Kafka it is even possible to further chain the change events
with other AWS services. My realization is thus that DynamoDB works best for high frequency low
volume use-cases such as e-commerce or other web-based services.

Relational databases have been around for ever, and the fact that they are still the most popular
backend for microservices after all those years speaks volumes for its security and community
support. I will still consider NoSQL databases for some very specific use-cases or in situations
where price is a big factor (ie. hobby projects), but for microservices in a professional context I
think I still prefer postgres in most cases.
