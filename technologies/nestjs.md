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
	_nest.js encourages jest test specifications for each module, adding service and controller default tests within the same module directory, when using the `cli` scaffolding_
6. Dependency Injection - loosely coupled

# Reference
1. [nest.js reference](https://nestjs.com/)
2. [Getting Started Tutorial](https://scotch.io/tutorials/getting-started-with-nestjs)
3. [nest.js, vue.js and MongoDB modern app example](https://scotch.io/tutorials/building-a-modern-app-using-nestjs-mongodb-and-vuejs)

> TODO:
>   1. Validation on input to return HTTP 400 and not 500

4.[Angluar, nest.js, fastify and MongoDB modern app example]( https://www.djamware.com/post/5d2898430707cc5968d9d57f/build-a-web-app-using-nestjs-fastify-mongodb-and-angular-8)

# Tips
## HTTP Error Codes
Out of the box, the nest.js controllers properly handle the HTTP errors; such as, _findById_ returning a 404 (NOT FOUND) or 500 (service error). But it doesn't fail so well on cast conversion errors with the mongoose schema: returning 500 rather than 400:

> Need to trap on `CastError` exception  from `mongoose` library.

## __v
Mongoose adds a `__v` property to every document on create. This is aligned with MongoDB Data Modelling best practice to always version your documents, allowing for schema changes going forward.

## Fasitfy
`@nestjs/platform-fastify` plugin swaps the default express framework for fastify; see the exmaple in the reference.

## Class cast/validation
https://docs.nestjs.com/techniques/validation

# Tricks
Use nest.js scaffolding: `npm install -g @nestjs/cli`.

## MongoDB
[TypeORM](https://github.com/typeorm/typeorm) is preferred in nest.js apps, owing to its support for both SQL and NoSQL databases, including MongoDB. nest.js also provides direct support for Mongoose.

### mongoose
https://docs.nestjs.com/techniques/mongodb

Read also, strongly typed models with Mongoose and typescript: https://medium.com/@tomanagle/strongly-typed-models-with-mongoose-and-typescript-7bc2f7197722.

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

Having bound the database connection, need to define one of more mongoose schema files. Using nest.js good structure, one of more schemas can be defined for each module. Create a `schemas` subdirectory within the module directory, and create a mongoose schema file, e.g.:
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

And then within the module's definition file `.../myname.module.ts`, bind the schema with the global `mongoose` module:
```
...
import { MongooseModule } from '@nestjs/mongoose';
import { CustomerSchema } from './schemas/customer.schema';
@Module({
  imports: [
    MongooseModule.forFeature([{ name: 'Customer', schema: CustomerSchema }])
  ],
	...
})
```

> TODO
> 1. Custom config including connection pool size, and wtiteConcern
> 2. mongoose likes to impose schema on data; write own module that uses the MongoDB driver directly, create the connection and expose the MongoDB driver
> 3.A folder `schemas` for mongoose schemas will clash with GraphQL schemas.

## CORS
Cross Origin Resource Sharing (CORS); add `app.enableCors();` to the `main.ts` file.

## CLI Generators
Note - in the examples below, "myname" is the context name for the module and is passed when creating the module, the module's default service and the module's controller:
Module: `nest generate module <myname>`
Service: `nest generate service <myname>`
Controller: `nest generate controller <myname>`


### Modules
When creating a module and then a service/controller, the CLI registers the module in `app.module.ts`, but does not register the controller sep\arately.

If creating a controller without a module, the CLI registers the controller in `app.module.ts`.