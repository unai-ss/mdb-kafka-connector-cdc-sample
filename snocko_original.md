{
"name": "MongoDbAtlasSourceConnector_test_ambbet_10_partitions",
"config": {
"connector.class": "MongoDbAtlasSource",
"name": "MongoDbAtlasSourceConnector_test_ambbet_10_partitions",
"kafka.auth.mode": "KAFKA_API_KEY",
"topic.prefix": "",
"topic.namespace.map": "{"*": "trial_ambbet_pipeline"}",
"connection.host": "amb-ambbet.tvdmx.mongodb.net",
"connection.user": "confluent_amb",
"database": "ambbet",
"collection": "bettransactions",
"poll.await.time.ms": "1000",
"poll.max.batch.size": "1000",
"pipeline": "[{ "$match": { "fullDocument.source": "PG_SLOT2", { $second: "fullDocument.updatedDate"}: {"$mod": [3, 0]} } }]",
"copy.existing": "false",
"output.data.format": "JSON",
"publish.full.document.only": "true",
"json.output.decimal.format": "NUMERIC",
"change.stream.full.document": "updateLookup",
"output.json.format": "ExtendedJson",
"tasks.max": "1"
}
}

Hi MongoDB team,

Recently we were working with the Confluent product team and we were stuck on splitting the pipeline inside the settings.

This was the following thing that we tried:

[{
    "$match": {
        "fullDocument.source": "PG_SLOT2",
        {{ $second: "fullDocument.updatedDate"}: {"$mod": [3, 0]}}
    }
}]

We are trying to get an output of an int(or we can cast it if it is a string) so that we can split the pipeline into 3 sections with %mod.

How do we actually acheive this? Please guide us or give us some documents for this because from the documentation, there weren't much material that we could get examples with.

####
db.snocko7.insert( { item: "card", qty: 15, updatedDate: Date("2012-11-06T00:14:20") } )


{"schema":{"type":"string","optional":false},"payload":"{\"_id\": {\"$oid\": \"62915f40cac316420f8e7d17\"}, \"item\": \"card\", \"qty\": 15.0, \"updatedDate\": {\"$date\": 1388564210736}, \"qty_mod\": 2}"}
{"schema":{"type":"string","optional":false},"payload":"{\"_id\": {\"$oid\": \"62915fa3cac316420f8e7d18\"}, \"item\": \"card\", \"qty\": 15.0, \"updatedDate\": {\"$date\": 1388564215736}, \"qty_mod\": 1}"}
{"schema":{"type":"string","optional":false},"payload":"{\"_id\": {\"$oid\": \"62915fadcac316420f8e7d19\"}, \"item\": \"card\", \"qty\": 15.0, \"updatedDate\": {\"$date\": 1388564217736}, \"qty_mod\": 0}"}



replset:PRIMARY> db.snocko9.insert( { item: "card", qty: 15, updatedDate: ISODate("2014-01-01T08:16:50.736Z") } )
WriteResult({ "nInserted" : 1 })
replset:PRIMARY> db.snocko9.insert( { item: "card", qty: 15, updatedDate: ISODate("2014-01-01T08:16:55.736Z") } )
WriteResult({ "nInserted" : 1 })
replset:PRIMARY> db.snocko9.insert( { item: "card", qty: 15, updatedDate: ISODate("2014-01-01T08:16:57.736Z") } )
WriteResult({ "nInserted" : 1 })
replset:PRIMARY>


{"schema":{"type":"string","optional":false},"payload":"{\"_id\": {\"$oid\": \"62916147cac316420f8e7d1a\"}, \"item\": \"card\", \"qty\": 15.0, \"updatedDate\": {\"$date\": 1388564210736}, \"date_mod\": 50}"}
{"schema":{"type":"string","optional":false},"payload":"{\"_id\": {\"$oid\": \"6291615dcac316420f8e7d1b\"}, \"item\": \"card\", \"qty\": 15.0, \"updatedDate\": {\"$date\": 1388564215736}, \"date_mod\": 55}"}
{"schema":{"type":"string","optional":false},"payload":"{\"_id\": {\"$oid\": \"62916166cac316420f8e7d1c\"}, \"item\": \"card\", \"qty\": 15.0, \"updatedDate\": {\"$date\": 1388564217736}, \"date_mod\": 57}"}

