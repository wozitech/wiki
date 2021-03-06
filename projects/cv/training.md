<!-- TITLE: WOZiTech CV Training Service -->
<!-- SUBTITLE: python RESTful API -->

# Repo
https://github.com/wozitech-cv/service-training.git

# TODO
https://realpython.com/python-testing/ - python testing
https://www.toptal.com/python/an-introduction-to-mocking-in-python - python test mocking
https://www.peterbe.com/plog/jsonschema-validate-10x-faster-in-python - Fast JSON schema validation


1. [GET] - query parameters: limit, since, total, facetted "scope"
2. [POST], [PUT], [DELETE] - authorization (JWT)
3. serverlesss framework - refactoring to allow reuse
4. Docker file
5. swagger - strict schema - especially around dates and objects
6. POST/PUT - data validation
7. Unit Testing
8. Integration (Interface) Testing - jest
9. Security Testing
10. Code Static Testing
11. Monitoring - Zipkin/promoetheus

# Dependencies
## Flask
https://www.fullstackpython.com/flask.html
Uses Flask as the web server.

## Connexion
On top of flask, `connexion` is used to create well documented (swagger) RESTful endpoint: https://github.com/zalando/connexion.

https://realpython.com/flask-connexion-rest-api/

# Data
https://swagger.io/docs

JSON data - training record:
```
{
	id: <int>,
	title: <string>,
	achieved: <ISO Date only - YYYY-MM-DD>,
	expires?: <ISO Date only - YYYY-MM-DD>,
	description: <string>,
	scope: <string>,
	provider: {
		name: <string> | "other",
		other?: <string>
	},
	tags: [<string>]
}
```
# RESTful Endpoints
## List
`[GET] /api` - returns all training records as a JSON array, in reverse chronological order on date `achieved`:
```
[
		{ training #1 },
		{ training #2 },
		...
		{ training #n }
]
```

## Get One
`[GET] /api/:id` - returns a single training record

## Create
`[POST] /api` - creates a single training record

Requires valid JWT.

## Update
`[PUT] /api/:id` - updates a single training record

Requires valid JWT.

## Delete
`[DELETE] /api/:id` - deletes the training record

Requires valid JWT.

## Swagger Documentation