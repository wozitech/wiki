![2018 Wit Kyn](/uploads/corporate/2018-wit-kyn.png "2018 Wit Kyn"){.pagelogo}<!-- TITLE: wit-kyn -->
<!-- SUBTITLE: Know Your Numbers - collaboration with Jazz Holverson -->

[wit-kyn repo](https://github.com/wozitech/wit-kyn.git)
# wit-kyn
This is the server side application for "Know Your Numbers"; a mobile application to support corporate well-being and fitness.

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

These specialised classes allow for `wit` specific behaviour around logging, authentication, validation, monitoring, metrics, caching, refreshing et al. More information on these specialised classes can be found here: [wit-nestjsSpecialisms](/projects/wit-nestjsSpecialisms).

A common theme throughout `wit` projects is data storage. `wit-kyn` uses our preferred data store, [MongoDB](https://www.mongodb.com/). As a common store for `wit` projects, we provide our own MongoDB nest.js module allowing for its reuse across any project specific module: [wit-nestjsMongoDB](/projects/wit-nestjsMongoDB).

# gun.js (Graph distributed database)
Realtime distributed database both reads and writes; perfect for team based updates and accumulations - e.g. leaderboards. Requires a `wit` specialised module.
![Gun Js](/uploads/logos/gun-js.png "Gun Js")

# Technologies
* nest.js
	* Using [dotenv-extended](https://www.npmjs.com/package/dotenv-extended) to read properties from env/file
* mongodb

![Nest Framework Logo](/uploads/logos/nest-framework-logo.png "Nest Framework Logo") ![Mongodb Logo 1](/uploads/logos/mongodb-logo-1.png "Mongodb Logo 1")
