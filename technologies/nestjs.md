![Nest Framework Logo](/uploads/logos/nest-framework-logo.png "Nest Framework Logo"){.pagelogo}
<!-- TITLE: nest.js -->
<!-- SUBTITLE: nest.js - Well structured MVC with dependency injection node.js REST/GraphQL progressive framework-->

# Introduction
Effectively Angular for the backend, a typescript framework with:
1. Controllers - routers
2. Providers (Services) - workers/model; siginficantly, services are used to ensure the controller does little more than routing
3. Interfaces - well defined data structures for controllers and providers
4. Schema - GraphQL schema
5. Modules - for stitching controlers, services, GraphQL schema and interfaces to build more complex APIs (more complex the app, the less microservice it is)
	6.  _nest.js encourages jest test specifications for each service, locating the test file within the same module directory_
7. Dependency Injection - loosely coupled

# Reference
1. [nest.js reference](https://nestjs.com/)
2. [Getting Started Tutorial](https://scotch.io/tutorials/getting-started-with-nestjs)
3. [nest.js, vue.js and MongoDB modern app example](https://scotch.io/tutorials/building-a-modern-app-using-nestjs-mongodb-and-vuejs)

# Tricks
Use nest.js scaffolding: `npm install -g @nestjs/cli`.

## MongoDB
[TypeORM](https://github.com/typeorm/typeorm) is preferred in nest.js apps, owing to its support for both SQL and NoSQL databases, including MongoDB. nest.js also provides direct support for Mongoose.

### mongoose
https://docs.nestjs.com/techniques/mongodb

First, install the mongoose dependencies:
```
npm install --save @nestjs/mongoose mongoose @types/mongoose
```

And then injest a connection to the database in `app.module.ts`, where `mydatabase` is the name of database for MongoDB resolved against localhost and default port (27001):
```
...
import { MongooseModule } from '@nestjs/mongoose';

@Module({
  imports: [
    MongooseModule.forRoot('mongodb://localhost/mydatabase', { useNewUrlParser: true })
  ],
	....
})
export class AppModule {}
```

Having bound the database connection, need to define one of more mongoose schema files. Using nest.js good structure, one of more schemas can be defined for each module. Create a `schemas` directory within the module directory, and create a mongoose schema file, e.g.:
```
import * as mongoose from 'mongoose';

export const CustomerSchema = new mongoose.Schema({
    first_name: String,
    last_name: String,
    email: String,
    phone: String,
    address: String,
    description: String,
    created_at: { type: Date, default: Date.now }
});
```

> TODO
> 1. Custom config including connection pool size, and wtiteConcern
> 2. mongoose likes to impose schema on data; write own module that uses the MongoDB driver directly, create the connection and expose the MongoDB driver
> 3.A folder `schemas` for mongoose schemas will clash with GraphQL schemas.

## CLI Generators
Module: `nest generate module <mymodule>`
Service: `nest generate service <myservice>`