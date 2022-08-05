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

mongodump --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies"

mongoexport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --collection=sales --out=sales.json

mongorestore --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies"  --drop dump

mongoimport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --drop sales.json
