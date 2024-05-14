# MQL

## MongoDB

* Data is stored in documents
* Documents are stored in Collections
* Document here refers to JSON
* Redundant copies of data are stored in replica set
* JSON is stored as BSON internally in MongoDB

```bash
# FOR BSON, use dump (backup) and restore (restore backup)

mongodump --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/<database name>"

# drop will delete the stuff already in and create the new object from restore

mongorestore --uri "mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/<database name>"  --drop dump

# FOR JSON, use export (backup) and import (import backup)

# collection to specify which collection
# out to specify the file name to export to

mongoexport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/<database name>" --collection=sales --out=sales.json

mongoimport --uri="mongodb+srv://<your username>:<your password>@<your cluster>.mongodb.net/<database name>" --drop sales.json
```

```bash
# to look at all databases available
show dbs

# to connect to a database
use sample_training

# to look at collections inside a database
show collections
```

## Queries

### Find

```bash
db.zips.find({"state": "NY"})

# Use 'it' : iterates through a cursor
# cursor : A pointer to a result set of query
# pointer : A direct address of memory location

db.zips.find({"state": "NY"}).count()

db.zips.find({"state": "NY", "city": "ALBANY"})

db.zips.find({"state": "NY", "city": "ALBANY"}).pretty()

# get random one
db.inspections.findOne()
```

* Each Document has a unique object `_id` which is set by default if not specfied

### Insert

```bash
db.inspections.insert({
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
         }
  })

db.inspections.insert({
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
         }
  })

db.inspections.find(
      {"id" : "10021-2015-ENFO", "certificate_number" : 9278806}
).pretty()
```

### Insert conflicts

```bash
# Insert three test documents
db.inspections.insert([ { "test": 1 }, { "test": 2 }, { "test": 3 } ])

# Insert three test documents but specify the _id values
# Error in 2 docs
# Insert operation halts when an error is in-countered
db.inspections.insert([{ "_id": 1, "test": 1 },{ "_id": 1, "test": 2 },
                       { "_id": 3, "test": 3 }])

# Find the documents with _id: 1
db.inspections.find({ "_id": 1 })

# Insert multiple documents specifying the _id values, 
# and using the "ordered": false option
# Ordered False will allow to insert all docs where id doesnt match, 
# and give errors for those which failed
db.inspections.insert([{ "_id": 1, "test": 1 },{ "_id": 1, "test": 2 },
                       { "_id": 3, "test": 3 }],{ "ordered": false })

# Insert multiple documents with _id: 1 with the default "ordered": true setting
db.inspection.insert([{ "_id": 1, "test": 1 },{ "_id": 3, "test": 3 }])
```

### Updates

[https://docs.mongodb.com/manual/reference/operator/update/#id1](https://docs.mongodb.com/manual/reference/operator/update/#id1)

```bash
# Find all documents in the zips collection 
# where the zip field is equal to "12434".

db.zips.find({ "zip": "12534" }).pretty()

# Find all documents in the zips collection 
# where the city field is equal to "HUDSON".

db.zips.find({ "city": "HUDSON" }).pretty()

# Update all documents in the zips collection 
# where the city field is equal to "HUDSON" 
# by adding 10 to the current value of the "pop" field.
# Increment Operation

db.zips.updateMany({ "city": "HUDSON" }, { "$inc": { "pop": 10 } })

# Update a single document in the zips 
# collection where the zip field is 
# equal to "12534" by setting the value 
# of the "pop" field to 17630.
# Update / Set operation

db.zips.updateOne({ "zip": "12534" }, { "$set": { "pop": 17630 } })

# Update a single document in the zips 
# collection where the zip field is equal 
# to "12534" by setting the value of 
# the "popupation" field to 17630.
# Update / Set operation

db.zips.updateOne({ "zip": "12534" }, { "$set": { "population": 17630 } })

# Find all documents in the grades 
# collection where the student_id 
# field is 151 , and the class_id field is 339.

db.grades.find({ "student_id": 151, "class_id": 339 }).pretty()

# Update one document in the grades 
# collection where the student_id is 
# `250` *, and the class_id field is 339, 
# by adding a document element to the "scores" array.

db.grades.updateOne({ "student_id": 250, "class_id": 339 },
                    { "$push": { "scores": { "type": "extra credit",
                                             "score": 100 }
                                }
                     })

db.grades.find({ "student_id": 250, "class_id": 339 })
```

### Upsert

```bash
db.iot.updateOne({ "sensor": r.sensor, "date": r.date,
                   "valcount": { "$lt": 48 } },
                         { "$push": { "readings": { "v": r.value, "t": r.time } },
                        "$inc": { "valcount": 1, "total": r.value } },
                 { "upsert": true })
```

### Delete

```bash
# Look at all the docs that have test field equal to 1.
db.inspections.find({ "test": 1 }).pretty()

# Delete all the documents that have test field equal to 1.
db.inspections.deleteMany({ "test": 1 })

# Delete one document that has test field equal to 3.
db.inspections.deleteOne({ "test": 3 })

# Inspect what is left of the inspection collection.
db.inspection.find().pretty()
# View what collections are present in the sample_training collection.
show collections

# Drop the inspection collection.
db.inspection.drop()
```

### Operators

```bash
# Find all documents where the tripduration 
# was less than or equal to 70 seconds and 
# the usertype was not Subscriber:
# LESS THAN EQUAL
# NOT EQUAL

db.trips.find({ "tripduration": { "$lte" : 70 },
                "usertype": { "$ne": "Subscriber" } }).pretty()

# Find all documents where the tripduration
# was less than or equal to 70 seconds and 
# the usertype was Customer using a redundant equality operator:
# LESS THAN EQUAL
# EQUAL

db.trips.find({ "tripduration": { "$lte" : 70 },
                "usertype": { "$eq": "Customer" }}).pretty()

# Find all documents where the tripduration
# was less than or equal to 70 seconds and 
# the usertype was Customer using the implicit equality operator:
# LESS THAN EQUAL

db.trips.find({ "tripduration": { "$lte" : 70 },
                "usertype": "Customer" }).pretty()

# Find all documents where airplanes CR2 or A81 
# left or landed in the KZN airport:

# AND, OR operator

db.routes.find({ "$and": [ { "$or" :[ { "dst_airport": "KZN" },
                                    { "src_airport": "KZN" }
                                  ] },
                          { "$or" :[ { "airplane": "CR2" },
                                     { "airplane": "A81" } ] }
                         ]}).pretty()
```

* AND operator is present in your qureies when not specified

### EXPR

```bash
# Find all documents where the trip started
# and ended at the same station:
# here $ denotes the value of the field specified

db.trips.find(
    { "$expr": { "$eq": [ "$end station id", "$start station id"] }
}).count()

# replacing id with name

db.trips.find(
    { "$expr": { "$eq": [ "$end station name", "$start station name"]}
}).count()


# Find all documents where the trip lasted 
# longer than 1200 seconds, and started 
# and ended at the same station:

db.trips.find({ "$expr": { "$and": [ { "$gt": [ "$tripduration", 1200 ]},
                         { "$eq": [ "$end station id", "$start station id" ]}
                       ]}}).count()
```

## Array

```bash
# using ALL operator

db.listingsAndReviews.find(
    { "amenities": 
        {
          "$size": 20,
          "$all": [ "Internet", "Wifi",  "Kitchen",
                   "Heating", "Family/kid friendly",
                   "Washer", "Dryer", "Essentials",
                   "Shampoo", "Hangers",
                   "Hair dryer", "Iron",
                   "Laptop friendly workspace" ]
        }
    }).pretty()
```

### Array operators and Projection

```bash
# Find all documents with exactly 20 amenities which include 
# all the amenities listed in the query array, 
# and display their price and address:

db.listingsAndReviews.find({ "amenities":
        { "$size": 20, "$all": [ "Internet", "Wifi",  "Kitchen", "Heating",
                                 "Family/kid friendly", "Washer", "Dryer",
                                 "Essentials", "Shampoo", "Hangers",
                                 "Hair dryer", "Iron",
                                 "Laptop friendly workspace" ] } },
                            {"price": 1, "address": 1}).pretty()

# Find all documents that have Wifi as one of the amenities 
# only include price and address in the resulting cursor:

db.listingsAndReviews.find({ "amenities": "Wifi" },
                           { "price": 1, "address": 1, "_id": 0 }).pretty()

# Find all documents that have Wifi as one of the amenities 
# only include price and address in the resulting cursor, 
# also exclude ``"maximum_nights"``. **This will be an error:*

db.listingsAndReviews.find({ "amenities": "Wifi" },
                           { "price": 1, "address": 1,
                             "_id": 0, "maximum_nights":0 }).pretty()

# nested projection

db.listingsAndReviews.find({ "amenities": "Wifi" }, { "price": 1, "address": { "country" : 1 }, "_id": 0 }).pretty()


# Switch to this database:
use sample_training

# Get one document from the collection:
db.grades.findOne()

# Elematch Example

# Find all documents where the student in class 431 received 
# a grade higher than 85 for any type of assignment:

db.grades.find({ "class_id": 431 },
               { "scores": { "$elemMatch": { "score": { "$gt": 85 } } }
             }).pretty()

# Find all documents where the student had an extra credit score:

db.grades.find({ "scores": { "$elemMatch": { "type": "extra credit" } }
               }).pretty()
```

* `Elematch` : matches documents that contains an array field with at least one element that matches the specified query creteria
* `Elematch` : Projects only the array elements with at least one element that matches the specified criteria

### Array Operators and Sub-Documents

```bash
use sample_training

db.trips.findOne({ "start station location.type": "Point" })

db.companies.find({ "relationships.0.person.last_name": "Zuckerberg" },
                  { "name": 1 }).pretty()

db.companies.find({ "relationships.0.person.first_name": "Mark",
                    "relationships.0.title": { "$regex": "CEO" } },
                  { "name": 1 }).count()


db.companies.find({ "relationships.0.person.first_name": "Mark",
                    "relationships.0.title": {"$regex": "CEO" } },
                  { "name": 1 }).pretty()

db.companies.find({ "relationships":
                      { "$elemMatch": { "is_past": true,
                                        "person.first_name": "Mark" } } },
                  { "name": 1 }).pretty()

db.companies.find({ "relationships":
                      { "$elemMatch": { "is_past": true,
                                        "person.first_name": "Mark" } } },
                  { "name": 1 }).count()
```



## Sort and Limit

* Use sort before limit always

```bash
db.zips.find().sort({ "pop": 1 }).limit(1)

db.zips.find({ "pop": 0 }).count()

db.zips.find().sort({ "pop": -1 }).limit(1)

db.zips.find().sort({ "pop": -1 }).limit(10)

db.zips.find().sort({ "pop": 1, "city": -1 })
```
