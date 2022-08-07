# Notes for mongodb

## Login:
- user: m001-student
- pass: m001-mongodb-basics
- type this: `mongo "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/admin"`

## Chapter 1
- document: has a field (key) and a value
- collection: has one or many documents
- database: has one or more collection
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
  
- HOW TO FIND SOMETHING IN THE COLLECTION:
  1) `mongo "mongodb+srv://m001-student:m001-mongodb-basics@sandbox.mongodb.net/admin"`
  2) `show dbs` (shows you all of the databases you have, ex: grades, students, teachers)
  3) `use sample_training` select one of the databases
  4) `show collections` shows you the group of JSON files in the database
  5) `db.zips.find({"state": "NY"})` searches collection for documents with "state" : "NY"
  6) `db.zips.find({"state": "NY"}).pretty()` formats the JSON in a readable way
  7) `it` iterates to the next page of results in the collection
  
## Chapter 3
- every document MUST have a unique _id value
  - this allows for the same exact fields in different document
- HOW TO INSERT A SINGLE DOCUMENT IN THE COLLECTION:
  1) `mongoimport --uri="mongodb+srv://m001-student:m001-mongodb-basics@sandbox.mongodb.net/sample_supplies" --drop sales.json`
  2) connect to atlas cluster: `mongo "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/admin"`
  3) look for the collection we need: `use sample_training`
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
- HOW TO INSERT MUTLIPLE DOCUMENTS:
  1) `db.inspections.insert([ { "test": 1 }, { "test": 2 }, { "test": 3 } ])` inserts 3 documents
  2) `db.inspections.insert([{ "_id": 1, "test": 1 },{ "_id": 1, "test": 2 },{ "_id": 3, "test": 3 }])` inserts documents with id
  3) to insert SIMILAR documents, "ordered": false -> `db.inspections.insert([{ "_id": 1, "test": 1 },{ "_id": 1, "test": 2 },{ "_id": 3, "test": 3 }],{ "ordered": false})`
  4) to insert UNIQUE documents, "ordered": true -> `db.inspection.insert([{ "_id": 1, "test": 1 },{ "_id": 3, "test": 3 }])` 
- *** IMPORTANT **** : if you accidentally make a new field, it won't give you an error, but instead give you a new field!!
- findOne() looks for a document that matches the query
- updateOne("_id":<number>) if multiple documents match the query, only ONE will be updated,
- updateMany() updates all documents that match the query
- HOW TO UPDATE A DOCUMENT:
  1) connect to atlas cluster: `mongo "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/admin"`
  2) select the collection: `use sample_training`
  3) finds documents with zip codes `db.zips.find({"zip":"1234"}).pretty()`
  4) counts how many documents have the city HUDSON -> `db.zips.find({"city":HUDSON"}).count()`
  5) to update ALL of them -> `db.zips.updateMany({"city":HUDSON"}), {"$inc":{"pop":10}})`
      - $inc is an update operator that increments the value in a field by some amount
      - allows us to update MANY documents at the same time
      - format -> {"$inc": {"pop": 10, "field2": increment value, ...}}
  6) to update ONLY ONE document: `db.zips.updateOne({"zip":"1234"}, {"$set": {"pop":17630}})`
      - $set updates the given value of a field 
  7) to ADD a new field: `db.grades.updateOne({"student_id":250, "class_id":339}, {"$push": {"scores": {"type": "extra credit", "score": 100}}}`
- deleteOne() deletes ONE document, RECOMMENDED to use deleteOne("_id":<number>) to verify you delete a single document
- deleteMany() deletes MULTIPLE documents
- HOW TO DELETE MULTIPLE DOCUMENTS: 
  1) get the collection: `use sample_training`
  2) look for the document to delete: `db.inspections.find({"test":1}).pretty()`
  3) look for another document: `db.inspections.find({"test":3}).pretty()`
  4) delete documents w/ test 1: `db.inspections.deleteMany({"test":1})`
  5) delete documents w/ test 3: `db.inspections.deleteMany({"test":3})`
  6) to drop a collection from a database: `db.collection.drop()`
- ** IMPORTANT ** when all collections are dropped from a DB, the DB NO LONGER APPEARS in the list of databases
  
  ## Chapter 4
  - review MQL operators:
    - update operators: `$inc`, `$set`, `$unset`
    - query operators: `$eq` (equal to), `$ne` (not equal to), `$gt` (greater than), `$lt` (less than), `$gt`e (>=), `$lte` (<=)
  - logical operators:
    - used for more than one statement: `{{<operator> : [{statement1}, {statement2}, ...]}`
    - where <operator> is:
        - `$and`
        - `$or`
        - `$nor`
    - used to negate: `{$not {statement}}`
        - `$not`
  - IMPLICIT $and is different!! can write it as:
    - `{"$and" : [{"student_id": {"$gt": 25}}, {"student_id": {"$lt": 100}}] }`
    - OR (the better way) -> `{"student_id": {"$gt": 25, "$lt": 100}}`
  - $expr can be used as a variable
  - `{ $expr: { <expression> } }`
  - ex: `{"$expr": {"$eq": ["$start station name", "$end station id"]}}`
  - here, `$start station name` is the VALUE of start station name, and `end station id` is the VALUE
