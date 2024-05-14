# MongoDB Aggregation

## Aggregate Framework

* Queries are written inside `[]` operator, denoting the order in which hey execute
* `$group` : An operator that takes in multiple streams of data and distributes it into multiple reservoirs

```bash
# MQL Query
db.listingsAndReviews.find({ "amenities": "Wifi" },
                           { "price": 1, "address": 1, "_id": 0 }).pretty()

# MQL Query ith aggregation framework
db.listingsAndReviews.aggregate(
    [
      { "$match": { "amenities": "Wifi" } },
      { "$project": { "price": 1,
                      "address": 1,
                      "_id": 0 }}
    ]).pretty()
```

```bash
# Find one document in the collection 
# and only include the address field in the resulting cursor.
db.listingsAndReviews.findOne({ },{ "address": 1, "_id": 0 })

# Project only the address field value for each document, 
# then group all documents into one document per address.country value.
db.listingsAndReviews.aggregate(
    [   { "$project": { "address": 1, "_id": 0 }},
        { "$group": { "_id": "$address.country" }}
    ])

# Project only the address field value for each document, 
# then group all documents into one document per address.country value, 
# and count one for each document in each group.
db.listingsAndReviews.aggregate(
    [
          { "$project": { "address": 1, "_id": 0 }},
          { "$group": { "_id": "$address.country",
                        "count": { "$sum": 1 } } }
    ])
```
