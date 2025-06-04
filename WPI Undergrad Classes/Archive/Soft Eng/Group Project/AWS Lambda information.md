## Install Stuff
1. AWS VScode Toolkit
2. AWS SAM CLI
3. Docker

## AWS Toolkit for VS code
go to the AWS tab
![[Screenshot 2023-11-25 at 11.32.09 AM.png]]

Create a Lambda SAM (Serverless application management) function
![[Screenshot 2023-11-25 at 11.32.29 AM.png]]

- choose `node.js 16`
- choose `x86` architecture
- use the Hello World template

## Components
1. app.js
	1. 
2. template.toml
	- includes info about how the Lambda is run
	- take note of
1. (in root folder) vscode/launch.json
	- Debug info, include sample request for local testing
	- include environment variables here too

### Changes for local testing
- use environment vars to connect to the database, not the `db_access` layer
```js
require("dotenv").config()

var pool = mysql.createPool({
	host: process.env.DBHOST,
	user: process.env.DBUSER,
	password: process.env.DBPASS
});
```
- launch.json can only be set to debug one function at a time
- create a variable called `payload` that consists of the event body. We will use this to reference the JSON objects in the request
```js
const payload = event.body
```
- switch the handler in the `template.toml` to `index/handler`. The change the event header to `exports.handler = async (event, context, callback) => {`. This will make transferring from local to deployment easier