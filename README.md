# MONGODB AGGREGATION PIPELINES

### Operators used and syntax : 

1. $match - `{ $match: { <query> } }`

2. $count - `{ $count: '<string>' }`

3. $group

4. $avg

5. $sum

6. $sort

7. $limit

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
            '$group': {
            '_id': 'null', // If we wanted avg age grouped by gender then change 'null' to '$gender'
            'averageAge': {
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
                    '$sum': 1
                }
            }
        }, 
        {
            '$sort': {
                'count': -1
            }
        }, 
        {
            '$limit': 2
        }
    ]
```


