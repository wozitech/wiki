![Nest Framework Logo](/uploads/logos/nest-framework-logo.png "Nest Framework Logo"){.pagelogo}
<!-- TITLE: nest.js -->
<!-- SUBTITLE: nest.js - Well structured MVC with dependency injection node.js REST/GraphQL progressive framework-->

# Introduction
Effectively Angular for the backend, a typescript framework with:
1. Controllers - routers
2. Providers (Services) - workers/model
3. Interfaces - well defined data structures for controllers and providers
4. Schema - GraphQL schema
5. Modules - for stitching controlers, services, GraphQL schema and interfaces to build more complex APIs (more complex the app, the less microservice it is)
6. Dependency Injection - loosely coupled

# Reference
1. [nest.js reference](https://nestjs.com/)
2. [Getting Started Tutorial](https://scotch.io/tutorials/getting-started-with-nestjs)
3. [nest.js, vue.js and MongoDB modern app example](https://scotch.io/tutorials/building-a-modern-app-using-nestjs-mongodb-and-vuejs)

# Tricks
## MongoDB
[TypeORM](https://github.com/typeorm/typeorm) is preferred in nest.js apps, owing to its support for both SQL and NoSQL databases, including MongoDB. nest.js also provides direct support for Mongoose.

### mongoose
https://docs.nestjs.com/techniques/mongodb

First, install the mongoose dependencies:
```
npm install --save @nestjs/mongoose mongoose
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

> TODO
> 1. Custom config including connection pool size, and wtiteConcern
> 2. mongoose likes to impose schema on data; write own module that uses the MongoDB driver directly, create the connection and 

