![2018 Wit Kyn](/uploads/corporate/2018-wit-kyn.png "2018 Wit Kyn"){.pagelogo}<!-- TITLE: wit-kyn -->
<!-- SUBTITLE: Know Your Numbers - collaboration with Jazz Holverson -->

[wit-kyn repo](https://github.com/wozitech/wit-kyn.git)
# wit-kyn
This is the server side application for "Know Your Numbers"; a mobile application to support corporate well-being and fitness.

# Technologies
* nest.js
	* Using [dotenv-extended](https://www.npmjs.com/package/dotenv-extended) to read properties from env/file
* mongodb

![Nest Framework Logo](/uploads/logos/nest-framework-logo.png "Nest Framework Logo") ![Mongodb Logo 1](/uploads/logos/mongodb-logo-1.png "Mongodb Logo 1")

# Project Initialisation
`wit-kyn` uses `nest.js`. Initialise the project, which includes add to github repo, integrating `dotenv-extended` to handle environment configuration and `Jest` unit testing. More detail [here](/projects/wit-kyn/init).

Common to all `wit` projects is `bunyan`/`winston` [logging](/projects/wit-projectLogging) and [JWT](/projects/wit-projectAuth). These are provided by the `wit-domain` and `wit-serverDomain` common libraries (npm modules); pure Javascript implementations. .

# `nest.js` typescript Common Behaviour
`nest.js` is a typescript application; a precompiler for javascript with strong types. It formalises aspect of class and inheritence.

`wit` use of `nest.js` provides its own specialised classes extending `nest.js` default behaviour for:
* module
* service (component)
* interface (domain types)
* guard (protection - authentication, schema validation)

A common theme throughout `wit` projects is data storage. `wit-kyn` uses our preferred data store, [MongoDB](https://www.mongodb.com/). As a common

## gun.js (Graph distributed database)
Realtime 

