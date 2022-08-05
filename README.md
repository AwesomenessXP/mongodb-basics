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

`mongoexport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --collection=sales --out=sales.json`

`mongorestore --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies"  --drop dump`

`mongoimport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --drop sales.json`
  
- to find something in a collection:
  1) `mongo "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/admin"`
  2) `show dbs` (shows you all of the databases you have, ex: grades, students, teachers)
  3) `use sample_training` select one of the databases
  4) `show collections` shows you the group of JSON files in the database
  5) `db.zips.find({"state": "NY"})` searches collection for documents with "state" : "NY"
  6) `db.zips.find({"state": "NY"}).pretty()` formats the JSON in a readable way
  7) `it` iterates to the next page of results in the collection
  
## Chapter 3
- every document MUST have a unique _id value
  - this allows for the same exact fields in different document
- to insert a NEW document in a collection:
  1) `mongoimport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/sample_supplies" --drop sales.json`
  2) connect to atlas cluster: `mongo "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/admin"`
  3) look for the database we need: `use sample_training`
  4) get a random document from collection: `db.inspections.findOne()`
  5) `db.inspections.insert({
      "_id" : ObjectId("56d61033a378eccde8a8354f"),
      "id" : "10021-2015-ENFO",
      "certificate_number" : 9278806,
      "business_name" : "ATLIXCO DELI GROCERY INC.",
      "date" : "Feb 20 2015",
      "result" : "No Violation Issued",
      "sector" : "Cigarette Retail Dealer - 127",
      "address" : {
              "city" : "RIDGEWOOD",
              "zip" : 11385,
              "street" : "MENAHAN ST",
              "number" : 1712
         }})` insert the document
  6) to check if it is inserted: `db.inspections.find({"id" : "10021-2015-ENFO", "certificate_number" : 9278806}).pretty()`
- identical documents CAN exist in the collection as long as their _id values are different!
- to insert MULTIPLE documents in the collection:
  1) `db.inspections.insert([ { "test": 1 }, { "test": 2 }, { "test": 3 } ])` inserts 3 documents
  2) `db.inspections.insert([{ "_id": 1, "test": 1 },{ "_id": 1, "test": 2 },{ "_id": 3, "test": 3 }])` inserts documents with id
  3) to insert SIMILAR documents, "ordered": false -> `db.inspections.insert([{ "_id": 1, "test": 1 },{ "_id": 1, "test": 2 },{ "_id": 3, "test": 3 }],{ "ordered": false})`
  4) to insert UNIQUE documents, "ordered": true -> `db.inspection.insert([{ "_id": 1, "test": 1 },{ "_id": 3, "test": 3 }])` 
  
