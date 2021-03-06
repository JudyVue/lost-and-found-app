<!-- Having your Travis badge here would have been a good feature -->
# Lost-and-FoundIt Api

## CF401 Midterm Project

# Description
The Lost and Found It App helps match lost items with their owners. It can be used by institutions or businesses; each location can designate an administrator who will keep track of physical items. The api uses MongoDb/Mongoose to store **Admin** and **User** accounts (accessed via a bearer token for secure authorization), plus the database of **Items** that are either lost (a request for an item, with its description) or found (which include a description and photo). The api implements **twilio** to send a dynamic SMS notification to a user based on a match from the database. The user is then able to claim their item or be referred to an administrator to identify a valuable item and then retrieve it via text response.

## Getting Started
These instructions will get you a copy of the project up and running on your local machine for development and testing purposes. See deployment for notes on how to deploy the project on a live system.

### Prerequisites
To install for use server-side, you will need a GitHub account and to install NodeJS npm.

### Installing: 
- Fork our repo at [lost-and-found-app] (https://github.com/team-finders/lost-and-found-app)
- Clone a copy to your local repository
- Initialize your repo
  ``` npm init -y ```
- Install dependencies (these will install the developer and non-dev files as included in the package.json):
  ``` npm i ```
- To utilize our code, you will need to implement Amazon Simple Storage Service. Set up an account at [Amazon AWS](https://aws.amazon.com/) then go here [Amazon S3](https://docs.aws.amazon.com/AmazonS3/latest/gsg/CreatingABucket.html) to create an S3 bucket.
- Incorporate your AWS_BUCKET name, AWS_ACCESS_KEY_ID, and your private AWS_SECRET_KEY into your dotenv file.
- Sign up for a twilio account [Twilio](https://www.twilio.com/). Incorporate the TWILIO_ACCOUNT_SID, 
TWILIO_AUTH_TOKEN, and TWILIO_NUM into your dotenv file.

---------------

## Entity Relationship
![erd diagram](https://raw.githubusercontent.com/team-finders/lost-and-found-app/staging/src/temp/erd.png)

## Schemas
- Admin (includes location) - one to many with Items and Users
- User - One to many with Items
- Item - One to one with User
- Assets (images of the items, unless security is needed)

## ROUTES 

### USER ACCOUNT

##### POST
/api/signup tp create a new Account, and enter the properties:
- Username
- Email
- Password
- First Name
- Last Name
- Phone Number
- Account ID (a unique number generated by Mongoose)

On success, we return a created account, we delete the password from the body of our Schema for security purposes. We then create a new promise for our token which will log success when a token is returned in JSON format to our database. If unsuccessful, our catch will move us to the next operation.

##### GET  
/api/login, logs us into an existing an account. On success, we receive a 200 status code, and create a new token promise which returns a token response in JSON format. Authentication to Bearer Token: set Token field to token retrieved from Account creation, then confirm with Preview Request

##### PUT (update)
/api/login/:id? Allows user to update their information in their account, utilizing a bearer auth token and the user's id. 200 status code for successful update of the user account.

##### DELETE (destroy)
/api/login/:id? Allows user to delete their account utilizing their account ID. We receive a 204 status code for successful deletion. 

## ADMIN ACCOUNT

##### POST (create)
/api/admin/create to create a new Admin, and enter the properties:
- Username 
- Password 
- Email 
- Location
- Phone Number

On success, we return a created Admin account, we delete the password from the body of our Schema for security purposes. We then create a new promise for our token which will log success when a token is returned in JSON format to our database. If unsuccessful, our catch will move us to the next operation.

##### GET (read)
/api/admin/login, logs us into an existing an admin account. On success, we receive a 200 status code, and create a new token promise which returns a token response in JSON format. Authentication to Bearer Token: set Token field to token retrieved from Account creation, then confirm with Preview Request

##### PUT (update)
/api/admin/login/:id? To update the information in an admininstrator's account.

##### DELETE (destroy)
/api/admin/login/:id? Allows an admin to delete either a user or an admin account using the account id to find the particular user/administrator. On success, we receive a 204 status code.

## ITEMS
/api/items to create a new Item, utilizing the properties:
- postType ('Lost' or 'Found')
- itemType (an enumerated list that can be added to or deleted from as you wish)
- locationId (references an Admin account)
- color
- material
- imageUrl (supplied by Amazon AWS)
- imageFileName (supplied by Amazon AWS)
- accountId (references a User account)

##### POST (create)
to /api/items, creates a new item in the database, via a user's account, and sends an image file, utilizing the node module, multer, to the AWS Bucket. We return a 200 status code for the successful creation of a new item. AWS returns the url and filename to the item in the database. These properties will be needed for DELETE requests.

##### GET (read)
to /api/items/:id? Allows a user to find an item via the ID that each item was assigned upon creation.

##### PUT (update)
to api/items/:id? Allows a user to go back and fill in any missing details about an item, such as color and material. We receive a 200 status code for a successful updating of the file.

##### DELETE (destroy)
to /api/items/:id? Uses the s3Remove function to reference an item by its ID to remove the associated image from its particular bucket. We receive a 204 status code upon successful deletion of an item.

## TWILIO

##### POST
/api/sms Creates our messages to the user for successful matching of an item to its user. Sends a 200 status code for successfully sending the SMS.

---------------
<!-- Your documentation for load tests are great -->
## Load Tests
- `scenariosCreated` - number of virtual users created in the preceding 10 seconds
- `scenariosCompleted` - number of virtual users that completed their scenarios in the preceding 10 seconds
- `requestsCompleted` - the number of HTTP requests and responses or WebSocket messages sent
- `RPS sent` - the average number of requests per second completed in the preceding 10 seconds (or throughout the test)
- `Request latency and Scenario duration`- calculated in milliseconds 
- `p95 and p99 values` - the 95th and 99th percentile values (a request latency p99 value of 500ms means that 99 out of 100 requests took 500ms or less to complete)
- `Codes` - will print out the reported codes, any error codes denote errors in the testing

### Summary report @ 19:14:31(-0700) 2018-07-15
  Scenarios launched:  3019
  Scenarios completed: 1
  Requests completed:  3020
  RPS sent: 243.74
  Request latency:
    min: 3
    max: 94.6
    median: 5.8
    p95: 23.5 
    p99: 41.4
  Scenario counts:
    Create Accounts: 3020 (100%)
  Codes:
    200: 1
    409: 3019

### Summary report @ 19:18:03(-0700) 2018-07-15
  Scenarios launched:  3020
  Scenarios completed: 3020
  Requests completed:  6040
  RPS sent: 449.07
  Request latency:
    min: 2
    max: 1507
    median: 576.6
    p95: 1285.8
    p99: 1395.6
  Scenario counts:
    Create Accounts: 3020 (100%)
  Codes:
    200: 3020
    400: 3020

## Load Test Charts and Graphs
<!--  I think your intended formatting for these links didn't come out right -->

file:///Users/noahisrael/codefellows/401/labs/lost-and-found-app/src/__test__/load%20tests/test-results.json.html

-----------------
## Built With:
* [NodeJS] (https://nodejs.org/en/) - Event-driven, JavaScript runtime environment for building scalable applications
* [Express] (https://expressjs.com/) - NodeJS application framework for creating web and mobile applications
* [MongoDB] (https://www.mongodb.com/) - NoSQL database for storing and retrieving objects
* [Mongoose] (http://mongoosejs.com/) - Object modeling for Node and utilizing MongoDB
* [AWS] (https://aws.amazon.com/) - Cloud storage for images
* [twilio] (https://www.twilio.com/) - API for sending and receiving programmable SMS messages

## Contributing
Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning
We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/your/project/tags). 

## Authors

* **Noah Israel** - https://github.com/noahmvf
* **Karen Lai** - https://github.com/karenbtlai
* **Liz Petersen** - https://github.com/LizabethPetersen

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments
<!-- Aw, you're welcome, remember TAs' help as well. :) -->
* Thanks to Code Fellows and Lead Instructor Judy Vue for inspiration and initial codebase.






