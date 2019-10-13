## DynamoBD JavaScript DocumentClient cheat sheet

This cheat sheet will help you get up and running quickly building applications with DyanamoDB in a Nodejs or JavaScript environment.

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

[put](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#put-property)
[scan](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#scan-property) -
[get](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#get-property) -
[query](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#query-property) -
[delete](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#delete-property) -
[update](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#update-property) - 

There are also other methods that you may be using:

[batchGet](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#batchGet-property) -
[batchWrite](https://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB/DocumentClient.html#batchWrite-property)
