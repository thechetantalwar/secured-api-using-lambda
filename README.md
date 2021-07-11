## Secured API using APIGateway backed by Lambda Function
![Use Case Diagram](https://github.com/thechetantalwar/secured-api-using-lambda/blob/master/diagram.png?raw=true)
* Create your account at auth0.com
* Creating Dyanmo DB Table
    * Go to AWS Management Console
    * Browse DynamoDB Dashboard
    * Click Create Table
    * Put table name as Users
    * Put partiton key as id
    * Click on Create Button at the bottom
* Creating Lambda Function, with the help of which we will add data into the table
    * Go to AWS Management Console
    * Browse Lambda Dashboard
    * Create Function
    * Define a function name
    * Keep Runtime as Node.js.14.x
    * Click on Change default execution role
    * Use Create a new role from AWS policy templates
    * Define a role name
    * Under policy templates select "Simple microservice permissions"  (or else create your new role with necessary permissions to deal with DynamoDB)
    * Click on Create Function
    * Under the code, paste the code provided in sample.js
    * Click on Deploy button to save and deploy the code
* Creating Auth0 authorizer now
    * Go to Auth0 dashboard
    * Go to Applications -> APIs -> Create API
    * Define a name
    * Put identifier as "https://auth0-jwt-authorizer"
    * Click on Create button
    * Click on Test button
    * Kindly note down URL in curl sample command
    * On the top, there will be identifier address, kindly note that down as well
* Let's create API endpoint for our Lambda Function now
    * Go to your Lambda Function page
    * Add Trigger
    * Under trigger dropdown select API Gateway
    * Create New HTTP API
    * Under Security, select JWT authorizer
    * Under Identity Source, put "$request.header.Authorization"
    * Under issuer, put the URL you noted from CURL command, and remove "oauth/token" from the end
    * Under Audince, put the address you noted from Identfier
    * Click on Add at the bottom
* Configure API Gateway now to create different methods for POST and GET
    * Go to API Gateway dashboard
    * Click on your recently created API
    * In the left pane, click on Routes
    * Click on ANY
    * Under Route Details, edit it
    * Change the method to GET and Save it
    * Under route details, kindly note down the path specified after your method Name
    * Click on Create, to create a new Route
    * Choose method as POST, and paste the path copied and Save it
    * Now click on POST method
    * Click on Attach Integrations to attach Lambda Function to the method
    * Click on dropdown stating Choose an Existing Integraiton, and the select already existing one, which we created for first method and Click on Attach Integration
    * In the left pane go to Routes once again
    * Click on Attach Authorizations to attach Authorizer to the method
    * Click on dropdown stating Select existing Authorizer, and the select already existing one, which we created for first method and Click on Attach Authorizer
* Now we are all done, let's test it out.
    * Go to your Lambda Function page, to get the API address
    * Click on API Gateway
    * Below you will see your API Endpoint
    * To test the get method
    ```curl -i YOUR_API_ENDPOINT```
    * You will get unauthorized error
    * Let's get the token first to authorize our request
    * Go to your Auth0 dashboard, under your API page, click on Test
    * Now you will see curl URL to get the Tokens, copy it and execute it, you will get the output as below
    ```{"access_token":"YOUR_ACCESS_TOKEN","expires_in":86400,"token_type":"Bearer"}```
    * Kindly copy your access token, we will put this in our requests
    * Let's try it out on GET method now
    ```curl -H "Authorization: Bearer YOUR_ACCCESS_TOKEN" -i YOUR_API_ENDPOINT```
    * As no record is there, you will get an empty response
    * Let's add some data using POST method
    ```curl -H "Content-Type: application/json" -H "Authorization: Bearer YOUR_ACCESS_TOKEN" -X POST -d '{"name": "Chetan Talwar", "designation": "FilmMaker", "city": "Jalandhar"}' -i  YOUR_API_ENDPOINT```
    * Your response will be like below
    ```
    HTTP/2 201 
    date: Sun, 11 Jul 2021 06:55:51 GMT 
    content-type: application/json 
    content-length: 45
    apigw-requestid: SOME_ID
    {"id":"SOME_ID"}
    ```
    * You have successfully added the record, let's use GET method to read this 
    ```curl -H "Authorization: Bearer YOUR_ACCCESS_TOKEN" -i YOUR_API_ENDPOINT```
    * Your response will be like below
    ```
    {"Items":[{"designation":"FilmMaker","city":"Las Vegas","id":"SOME_ID","name":"Chetan Talwar"}],"Count":1,"ScannedCount":1}
    ```
* That's it, you have successfully integrated Lambda function with API Gateway using Auth0 as auhtorizer.
* Happy Learning
