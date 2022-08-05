# mongodb-basics
Notes for mongo-db:

## Login:
- user: m001-student
- pass: m001-mongodb-basics

## Chapter 1
- document: has a field (key) and a value
- collection: has one or many documents
- cluster: group of servers that store your data
- How to set up:
  - connect to mongo shell

## Chapter 2
- BSON is a converted, more compact form of JSON
- JSON has:
  - mongoimport
  - mongoexport
- BSON has:
  - mongodump
  - mongorestore

`mongodump --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies"`

`ongoexport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --collection=sales --out=sales.json`

`ongorestore --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies"  --drop dump`

`ongoimport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --drop ales.json`
  
- find command:
  1) `mongo "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/admin"`
  2) `show dbs` (shows you all of the databases you have, ex: grades, students, teachers)
  3) `use sample_training` select one of the databases
  4) `show collections` shows you the group of JSON files in the database
  5) `db.zips.find({"state": "NY"})` searches collection for documents with "state" : "NY"
  6) `db.zips.find({"state": "NY"}).pretty()` formats the JSON in a readable way
  7) `it` iterates to the next page of results in the collection
  
