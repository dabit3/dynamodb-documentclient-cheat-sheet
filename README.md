## DynamoBD JavaScript DocumentClient cheat sheet

The DynamoDB Document Client is the easiest and most preferred way to interact with a DynamoDB database from a Nodejs or JavaScript application.

This cheat sheet will help you get up and running quickly building applications with DynamoDB in a Nodejs or JavaScript environment.

This is meant to be a concise version of the full documentation located [here](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html) to help you get productive as quickly as possible.

## Getting started

1. Make sure you have the `aws-sdk` JavaScript SDK installed or available in the environment. If you are using AWS Lambda, this should already be available.

```sh
npm install aws-sdk
```

2. Import the SDK and create a new DynamoDB document client with a set of configuration options.


```javascript
const AWS = require('aws-sdk')
const docClient = new AWS.DynamoDB.DocumentClient()
```

The `AWS.DynamoDB.DocumentClient()` constructor takes an optional hash of options. For instance, if you are wanting to set the location to a different region than the main AWS configuration, you could pass it in like this:

```javascript
const docClient = new AWS.DynamoDB.DocumentClient({ region: 'us-east-2' })
```

 Check out the documentation for those options [here](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB.html#constructor-property).

## Database operations

The main things you will be doing are interacting with the database in one of the following ways:

[put](https://github.com/dabit3/dynamodb-documentclient-cheat-sheet#put---creating-a-new-item) - [docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#put-property) - Creates a new item, or replaces an old item with a new item by delegating to AWS.DynamoDB.putItem().    
[scan](https://github.com/dabit3/dynamodb-documentclient-cheat-sheet#scan---scanning-and-returning-all-of-the-items-in-the-database) - [docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#scan-property) - Returns one or more items and item attributes by accessing every item in a table or a secondary index (limit of 1 MB of data).    
[get](https://github.com/dabit3/dynamodb-documentclient-cheat-sheet#get---getting-a-single-item-by-primary-key) - [docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#get-property) - Returns a single item given the primary key of that item    
[query](https://github.com/dabit3/dynamodb-documentclient-cheat-sheet#query---access-items-from-a-table-by-primary-key-or-a-secondary-index--gsi) - [docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#query-property) - Returns one or more items and item attributes by accessing every item in a table or a secondary index (maximum of 1 MB of data).    
[delete](https://github.com/dabit3/dynamodb-documentclient-cheat-sheet#delete---delete-a-single-item) - [docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#delete-property) - Deletes a single item in a table by primary key by delegating to AWS.DynamoDB.deleteItem().   
[update](https://github.com/dabit3/dynamodb-documentclient-cheat-sheet#update---update-a-single-item) - [docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#update-property) - Edits an existing item's attributes, or adds a new item to the table if it does not already exist by delegating to AWS.DynamoDB.updateItem().    

There are also other methods:

[batchGet](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#batchGet-property) - Returns the attributes of one or more items from one or more tables by delegating to AWS.DynamoDB.batchGetItem().    
[batchWrite](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#batchWrite-property) - Puts or deletes multiple items in one or more tables by delegating to AWS.DynamoDB.batchWriteItem().    
[createSet](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#createSet-property) - Creates a set of elements inferring the type of set from the type of the first element.    
[transactGet](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#transactGet-property) - Atomically retrieves multiple items from one or more tables (but not from indexes) in a single account and region.     
[transactWrite](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#transactWrite-property) - Synchronous write operation that groups up to 10 action requests.     

## Usage

### Put - Creating a new item

[full docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#put-property)

```javascript
const AWS = require('aws-sdk')
const docClient = new AWS.DynamoDB.DocumentClient()

var params = {
  TableName : 'ProductTable',
  Item: {
     id: '001',
     price: 100.00,
     inStock: true,
     name: 'Yeezys',
     sizes: [8, 8.5, 9, 10, 11, 12, 13]
  }
}

docClient.put(params, function(err, data) {
  if (err) console.log(err)
  else console.log(data)
})


// async function abstraction
async function createItem(itemData){
  var params = {
    TableName: 'ProductTable',
    Item: itemData
  }
  try {
    await docClient.put(params).promise()
  } catch (err) {
    return err
  }
  
}

// usage
exports.handler = async (event, context) => {
  try {
    const { data } = event.body
    await createItem(data)
    return { success: 'successfully created item' }
  } catch (err) {
    return { error: err }
  }
}

```

### scan - scanning and returning all of the items in the database

[full docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#scan-property)

```javascript
const AWS = require('aws-sdk')
const docClient = new AWS.DynamoDB.DocumentClient()

var params = {
  TableName : 'ProductTable',
  FilterExpression : '#shoename = :shoename', // optional
  ExpressionAttributeValues : {':shoename' : 'yeezys'}, // optional
  ExpressionAttributeNames: { '#shoename': 'name' } // optional
}

docClient.scan(params, function(err, data) {
  if (err) console.log(err)
  else console.log(data)
})

// async function abstraction
async function listItems(){
  var params = {
    TableName: 'ProductTable',
  }
  try {
    const data = await docClient.scan(params).promise()
    return data
  } catch (err) {
    return err
  }
}

// usage
exports.handler = async (event, context) => {
  try {
    const data = await listItems()
    return { data }
  } catch (err) {
    return { error: err }
  }
}
```

### get - getting a single item by primary key

[full docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#get-property)

```javascript
const AWS = require('aws-sdk')
const docClient = new AWS.DynamoDB.DocumentClient()

var params = {
  TableName : 'ProductTable',
  Key: {
    HashKey: 'hashkey'
  }
}

docClient.get(params, function(err, data) {
  if (err) console.log(err)
  else console.log(data)
})

// async function abstraction
async function getItem(id){
  var params = {
    TableName : 'ProductTable',
    Key: { id }
  }
  try {
    const data = await docClient.get(params).promise()
  } catch (err) {
    return err
  }
}

// usage
exports.handler = async (event, context) => {
  try {
    const data = await getItem(event.item.id)
    return { data }
  } catch (err) {
    return { error: err }
  }
}
```

### query - Access items from a table by primary key or a secondary index / GSI.

[full docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#query-property)

```javascript
const AWS = require('aws-sdk')
const docClient = new AWS.DynamoDB.DocumentClient()

var params = {
  TableName: 'ProductTable',
  IndexName: 'type-index',
  ExpressionAttributeNames: { '#typename': 'type' },
  KeyConditionExpression: '#typename = :typename',
  ExpressionAttributeValues: { ':typename': 'hat' }
}

docClient.query(params, function(err, data) {
   if (err) console.log(err);
   else console.log(data);
});

// async function abstraction
function queryItems(type){
  var params = {
    TableName: 'ProductTable',
    IndexName: 'type-index',
    ExpressionAttributeNames: { '#typename': 'type' },
    KeyConditionExpression: '#typename = :typename',
    ExpressionAttributeValues: { ':typename': type }
  }
  try {
    const data = await docClient.query(params).promise()
    return data
  } catch (err) {
    return err
  }
}

// usage
exports.handler = async (event, context) => {
  try {
    const data = await queryItems(event.item.type)
    return { data }
  } catch (err) {
    return { error: err }
  }
}
```

### delete - Delete a single item

[full docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#delete-property)

```javascript
const AWS = require('aws-sdk')
const docClient = new AWS.DynamoDB.DocumentClient()

var params = {
  TableName : 'ProductTable',
  Key: {
    id: 'my-item-id-to-delete',
  }
};

docClient.delete(params, function(err, data) {
   if (err) console.log(err);
   else console.log(data);
});


// async function abstraction
async function deleteItem(id){
  var params = {
    TableName : 'ProductTable',
    Key: { id }
  }
  try {
    await docClient.delete(params).promise()
  } catch (err) {
    return err
  }
}

// usage
exports.handler = async (event, context) => {
  try {
    await deleteItem(event.item.id)
    return { success: 'successfully deleted item' }
  } catch (err) {
    return { error: err }
  }
}
```

### update - Update a single item

[full docs](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#update-property)

```javascript
const AWS = require('aws-sdk')
const docClient = new AWS.DynamoDB.DocumentClient()

var params = {
  TableName: 'ProductTable',
  Key: { id : 'my-item-id' },
  UpdateExpression: 'set price = :newprice',
  ExpressionAttributeValues: { ':newprice': 100 }
}

docClient.update(params, function(err, data) {
   if (err) console.log(err);
   else console.log(data);
});

// async function abstraction
async function updateItem(id, price){
  var params = {
    TableName: 'ProductTable',
    Key: { id },
    UpdateExpression: 'set price = :newprice',
    ExpressionAttributeValues: { ':newprice': price }
  }
  try {
    await docClient.update(params).promise()
  } catch (err) {
    return err
  }
}

// usage
exports.handler = async (event, context) => {
  try {
    const { id, price } = event.item
    await updateItem(id, price)
    return { success: 'successfully updated item' }
  } catch (err) {
    return { error: err }
  }
}
```
