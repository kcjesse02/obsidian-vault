REST Architechtural Style
- Representational State Transfer
- Is stateless (unlike TCP and HTTP)(no connected/interrupted)
	- Requests from Client contains all info
	- Server state is held in the client
	- Client initiated request and is "in transition" until response
	- Representation of application state in response contains embedded links for next connection
- Scalable framework, allows many many people to 


#### Simplest Web Site
- Manually start apache server

### Quiz: Turn App into an API
- explain how to connect the controller (on an AWS server) to the boundary
- given Use Cases
- Array: Modifiable Collection
- Associative Array: Searchable Collection
	- JSON

```js
/guess
	{"Letter":"L"}

	200:
	{"Locations":["idx":0, "idx":4],
	"guesses": ["A", "C", "W", "L"]
	}

	400:
	{"error": "<2> is not a letter"}
```

- determine what contoller is on the client side, and what is on the server side

### Steps for Calc App
1. `create-react-app` project for calculator
2. create Lambda function `class_adder` with `executeCode` role
	1. when the Lambda function works, make sure to save it to github
3. 

