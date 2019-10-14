## DynamoBD JavaScript DocumentClient cheat sheet

The DynamoDB Document Client is the easiest and most preferred way to interact with a DynamoDB database from a Nodejs or JavaScript application.

This cheat sheet will help you get up and running quickly building applications with DynamoDB in a Nodejs or JavaScript environment.

This is meant to be a concise version of the full documentation located [here](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#constructor-property) to help you get productive as quickly as possible.

## Getting started

1. Make sure you have the `aws-sdk` JavaScript SDK installed or available in the environment. If you are using Lambda, this should already be available.

```sh
npm install aws-sdk
```

2. In the function, import the SDK and create a new DynamoDB document client with a set of configuration options.


```javascript
const AWS = require('aws-sdk')
const docClient = new AWS.DynamoDB.DocumentClient({ region: 'us-east-1' })
```

## Database operations

The main things you will be doing are interacting with the database in one of the following ways:

[put](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#put-property) - Creates a new item, or replaces an old item with a new item by delegating to AWS.DynamoDB.putItem().    
[scan](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#scan-property) - Returns one or more items and item attributes by accessing every item in a table or a secondary index.    
[get](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#get-property) - Returns a single item given the primary key of that item    
[query](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#query-property) - Returns one or more items and item attributes by accessing every item in a table or a secondary index.    
[delete](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#delete-property) - Deletes a single item in a table by primary key by delegating to AWS.DynamoDB.deleteItem().   
[update](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#update-property) - Edits an existing item's attributes, or adds a new item to the table if it does not already exist by delegating to AWS.DynamoDB.updateItem().    

There are also other methods that you may be using:

[batchGet](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#batchGet-property) - Returns the attributes of one or more items from one or more tables by delegating to AWS.DynamoDB.batchGetItem().    
[batchWrite](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#batchWrite-property) - Puts or deletes multiple items in one or more tables by delegating to AWS.DynamoDB.batchWriteItem().    
[createSet](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#createSet-property) - Creates a set of elements inferring the type of set from the type of the first element.    
[transactGet](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#transactGet-property) - Atomically retrieves multiple items from one or more tables (but not from indexes) in a single account and region.     
[transactWrite](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#transactWrite-property) - Synchronous write operation that groups up to 10 action requests.     

## Usage

### Creating an item (`putItem`)

[put](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#put-property)

```javascript
const AWS = require('aws-sdk')
const docClient = new AWS.DynamoDB.DocumentClient({ region: 'us-east-1' })

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

documentClient.put(params, function(err, data) {
  if (err) console.log(err);
  else console.log(data);
})


// async function abstraction
function createItem(itemData){
  var params = {
    TableName: process.env.DDB_TABLE_NAME,
    Item: itemData
  }
  return new Promise((resolve, reject) => {
    docClient.put(params, function(err) {
      if (err) {
        console.log('error adding item to dynamo!: ', err)
        reject(err)
      } else {
        resolve()
      }
    })
  })
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