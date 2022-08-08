# Notes for mongodb

## Login:
- user: m001-student
- pass: m001-mongodb-basics
- type this: `mongo "mongodb+srv://<username>:<password>@<cluster>.mongodb.net/admin"`

## Chapter 1 - Setting up MongoDB
- document: has a field (key) and a value
- collection: has one or many documents
- database: has one or more collection
- cluster: group of servers that store your data
- How to set up:
  - connect to mongo shell
  - 
## Chapter 2 - Import/Export, Query Data
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
  
## Chapter 3 - Creating and Editing documents
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
  
## Chapter 4 - Comparisons, Logic Operators, Arrays
  - review MQL operators:
    - update operators: `$inc`, `$set`, `$unset`
    - query operators: `$eq` (equal to), `$ne` (not equal to), `$gt` (greater than), `$lt` (less than), `$gt`e (>=), `$lte` (<=)
###### Logical operators:
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
###### Variables:
- $expr can be used as a variable
- `{ $expr: { <expression> } }`
- ex: `{"$expr": {"$eq": ["$start station name", "$end station id"]}}`
- here, `$start station name` is the VALUE of start station name, and `end station id` is the VALUE
- to add an element to an array OR turn a field into an array field: `$push` 
###### Arrays:
- when looking for a field in an array, you should list the elements the way they are shown in the document
- `<array field> : {"$size": <number>}}` returns documents where the array field is the length
- `<array field> : {"$all": <array>}}` returns documents with given elements REGARDLESS of their order in array
- ex: find documents with 20 amenities, including all of the amenities in the query array: 
  - `db.listingsAndReviews.find({ "amenities": {
                                "$size": 20,
                                "$all": [ "Internet", "Wifi",  "Kitchen",
                                         "Heating", "Family/kid friendly",
                                         "Washer", "Dryer", "Essentials",
                                         "Shampoo", "Hangers",
                                         "Hair dryer", "Iron",
                                         "Laptop friendly workspace" ]
                                       }
                          }).pretty()`
- find a listing and review that accommodates more than 6 people and has 50 reviews: 
  - `db.listingsAndReviews.find({"accommodates": {"$gt": 6}, "reviews": {"$size": 50}}).count()`
- Projection: specifies the fields that SHOULD or SHOULDNT be in the result
  -ex: db.<collection>.find({ <query> }, {<projection>}})
- Projection syntax:
   `db.<collection>.find({<query>}, {<projection>})`
  - 1: include in the field, 0: exclude the field
  - ex: `db.<collection>.find({<query>}, {<field1>: 0, <field2>: 0})`
  - another ex: What if you wanted to find ONLY the names of companies with 8 funding rounds?
    `db.companies.find({ "funding_rounds": { "$size": 8 } }, { "name": 1, "_id": 0 })`
- what if you want to find a SPECIFIC field in an array?
  - we use `{<field>: "$elemMatch": {<field>: <value>}}}`
    - where field is the name of the array, and <field>: <value> is some item in the array
  - ex: `db.grades.find({ "class_id": 431 },
               { "scores": { "$elemMatch": { "score": { "$gt": 85 } } }
             }).pretty()`
- what if you want to find all students who got extra credit?:
  `db.grades.find({ "scores": { "$elemMatch": { "type": "extra credit" } }
               }).pretty()`
- what if you want to find how many offices companies have in seattle?
  `db.companies.find({"offices": {"$elemMatch": {"city": "Seattle"}}}).count()`
###### Array operators and sub documents:
- we can use dot notation to get the value of an item in a field:
  `db.trips.findOne({ "start station location.type": "Point" })` here, start station location.type: "Point" is acccessed    through dot notation
- ***dot notation is way is FASTER than $elemMatch***
- to query an element in subdocument, use ***dot notation***
- ex: how many trips start at locations that are west of -74 longitude?
  - longitude decreases as you move west, and is the 0th element in the array
  `db.trips.find({"start station location.coordinates.0": {"$lt": -74}}).count()`
  
## Chapter 5 - Indexing and Aggregation Pipeline
###### Aggregation:
- ***Aggregation Framework***: its another way to query in mongodb
- aggregation syntax: `db.listingsAndReviews.aggregate([
                                  { "$match": { "amenities": "Wifi" } },
                                  { "$project": { "price": 1,
                                                  "address": 1,
                                                  "_id": 0 }}]).pretty()`
- aggregation FILTERS the data in a pipeline
- $match and $project are filters:
  `$match`: filters anything that isn't in `amenities`
  `$project`: filters fields that are not `price` or `address` (because they both equal 1, include them)
- `$group`: can find an array of elements in a document
   ex: ` $group: { _id: address.country,....}`
   another ex: `db.listingsAndReviews.aggregate([ { "$project": { "address": 1, "_id": 0 }}, { "$group": { "_id": "$address.country" }}])`
   another ex: `db.listingsAndReviews.aggregate([
                                  { "$project": { "address": 1, "_id": 0 }},
                                  { "$group": { "_id": "$address.country",
                                                "count": { "$sum": 1 } } }
                                ])`
  this is saying: we only want the address from each document, group them all together in a new document, then use dot notation as: "_id" : "$address.country", and for each field, ONLY get a different country!!
- ***REMEMBER***: this does not modify the data!!
- so basically, we can look for very specific things in the data and output an array of them!!
  - $project is WHAT we are looking for
  - $group is HOW we print out the data in an array (using the variable operator)
  ex: what room types are present in the collection?
  use: `db.listingsAndReviews.aggregate([{"$project": {"room_type": 1, "_id": 0}}, {"$group": {"_id": "$room_type"}}]).pretty()`
###### sort() and limit()
- `$sort` sorts data from ascending/descending order
  - syntax: `db.zips.find().sort({<field>: <order>})`
    - where order is 1, 0, or -1
- `$limit` specifies HOW MANY results we get
- syntax for "$sort" and "$limit": `db.<collection>.find().sort().limit()`
- what if we want to find top result?
  ex: lets find the city with the least number of people:
  `db.zips.find().sort({ "pop": 1 }).limit(1)`
  why is this? it sorts by population from least people to most people
  - `"pop": 1` means to look for populations ***increasing*** (gives us the first least populated)
  - `"pop": 0` gives us ALL zip codes with a population of 0
  - `"pop": -1` means search for populations ***decreasing*** (gives us the first most populated)
    - ex: `db.zips.find().sort({ "pop": -1 }).limit(1)`
- what if we want to find the top 3 or top 10 results?
  - for top 10: `db.zips.find().sort({ "pop": -1 }).limit(10)` (gives top 10 results in decreasing order)
  - for top 3: `db.zips.find().sort({ "pop": -1 }).limit(3)`
###### Indexes:
- this is ***FASTER*** than using $sort!!
- an ***index*** is placed alphabetically/numerically
- syntax: 
  1) `db.trips.createIndex({"birth year": 1})`
  2) `db.trips.find({"birth year": 1989})`
  - this sorts birthdays so querying is faster!
- helps make querying efficient BUT only good for single field indexes
- for compound indexes (needs more than one field):
  `db.trips.find({"start station id": 476).sort("birth year": 1)`
