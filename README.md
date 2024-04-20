# MONGODB AGGREGATION PIPELINES

### Operators used and syntax : 

1. $match - `{ $match: { <query> } }`

2. $count - `{ $count: '<string>' }`

3. $group - ``` javascript
            {
                $group:
                {
                    _id: <expression>, // Group key 
                    <field1>: { <accumulator1> : <expression1> },
                    ...
                }
            }```

4. $avg - `{ $avg: <expression> }`

5. $sum - `{ $sum: <expression> }`

6. $sort - `{ $sort: { <field1>: <sort order>, <field2>: <sort order> ... } }`

7. $limit - `{ $limit: <positive 64-bit integer> }`

### QUESTIONS : 

1. How many users are active?

```javascript
    [
        // stage 1 : checking which users are active (having isActive value as true)
        // $match is used to filter documents to pass only the documents that match the specified conditon(s) to the next pipeline stage
        {
            '$match': {
            'isActive': true
            }
        }, 
        // stage 2 : count the number of documents
        // Passes a document to the next stage that contains a count of the number of documents input to the stage.
        {
            '$count': 'activeUsers'
        }
    ]
```

2. What is the average of all the users?

```javascript
    [
        {
            // The $group stage separates documents into groups according to a "group key".
            '$group': {
                //? note : _id is required
            '_id': 'null', // If we wanted avg age grouped by gender then change 'null' to '$gender'
            'averageAge': {
                // Returns the average value of the numeric values. $avg ignores non-numeric values.
                '$avg': '$age'
                }
            }
        }
    ]
```
3. List the top 5 most common favourite fruits among the users?

```javascript
    [
        {
            '$group': {
                '_id': '$favoriteFruit', 
                'count': {
                    // Calculates and returns the collective sum of numeric values. $sum ignores non-numeric values.
                    '$sum': 1
                }
            }
        }, 
        {
            // Sorts all input documents and returns them to the pipeline in sorted order.
            '$sort': {
                'count': -1 //Sort descending
            }
        }, 
        {
            //Limits the number of documents passed to the next stage in the pipeline.
            '$limit': 2
        }
    ]
```
4. Find the total number of males and females?

```javascript
    [
        {
          '$group': {
            '_id': '$gender', 
            'count': {
              '$sum': 1
            }
          }
        }
    ]
```

5. Which country has the highest number of registered users?

```javascript
    [
        {
          '$group': {
            '_id': '$company.location.country', 
            'registeredUsers': {
              '$sum': 1
            }
          }
        }, {
          '$sort': {
            'registeredUsers': -1
          }
        }, {
          '$limit': 1
        }
    ]
```

6. List all unique eye colors present in the collection.

```javascript
    [
        {
          '$group': {
            '_id': '$eyeColor'
          }
        }
    ]
```

7. What is the average number of tags per user?

> Method 1

```javascript
    [
        {
          '$unwind': '$tags'
        }, {
          '$group': {
            '_id': '$_id', 
            'numberOfTags': {
              '$sum': 1
            }
          }
        }, {
          '$group': {
            '_id': null, 
            'avgNumberOfTags': {
              '$avg': '$numberOfTags'
            }
          }
        }
    ]
```

> Method 2

```javascript
    [
        {
          '$addFields': {
            'numberOfTags': {
              '$size': {
                '$ifNull': [
                  '$tags', []
                ]
              }
            }
          }
        }, {
          '$group': {
            '_id': null, 
            'averageNumberOfTags': {
              '$avg': '$numberOfTags'
            }
          }
        }
    ]
```

8. How many users have 'enim' as one of their tags?

```javascript
    [
        {
          '$match': {
            'tags': 'enim'
          }
        }, {
          '$count': 'userWithTheString'
        }
    ]
```

9. What are the names and age of users who are inactive and have 'velit' as a tag?

```javascript
    [
        {
          '$match': {
            'isActive': false, 
            'tags': 'velit'
          }
        }, {
          '$project': {
            'name': 1, 
            'age': 1
          }
        }
    ]
```

10. How many users have a phone number starting with '+1 (940)'?

```javascript
    [   
        {
          '$match': {
            'company.phone': {
              '$regex': '^\\+1\\s?\\(940\\)\\s?\\d{3}-?\\d{4}$'
            }
          }
        }, {
          '$count': 'usersWIthPhone'
        }
    ]   
```