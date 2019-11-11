<!-- TITLE: strapi -->
<!-- SUBTITLE: A quick guide to strapi -->

[Back To Project](projects/cms)
# Reference
https://strapi.io/documentation/

# Database Connection
Having created the initial strpai application using the `strpai` CLI, I recommended using a local MongoDB database (without authentication) because the code will be committed back to github, and you don't want everyone knowing your database credentials.

In a multiple environment deployment, as is the case with WOZiTech CMS, different database credentials will be required in each environment, and certainly for the production instance, that will be a clustered database in MongoDB Atlas.

When creating the initial app, `strapi` assumes three deployable environments, each having its own configuration directory:
* development (`.../config/environments/development`)
* staging (`.../config/environments/staging`)
* production (`.../config/environments/production`)

In each of those directories is a `database.json`.

`.../config/environments/development/database.json`:
```
{
  "defaultConnection": "default",
  "connections": {
    "default": {
      "connector": "strapi-hook-mongoose",
      "settings": {
        "database": "wozitech-cms",
        "host": "127.0.0.1",
        "srv": false,
        "port": 27017,
        "username": "",
        "password": ""
      },
      "options": {
        "authenticationDatabase": ""
      }
    }
  }
}
```

This will be the values as enter during CLI, which is why its important to normalise when creating the initial application.

Staging/Production `database.json` are then same:
```
{
  "defaultConnection": "default",
  "connections": {
    "default": {
      "connector": "strapi-hook-mongoose",
      "settings": {
        "host": "${process.env.DATABASE_HOST || '127.0.0.1'}",
        "port": "${process.env.DATABASE_PORT || 27017}",
        "srv": "${process.env.DATABASE_SRV || false}",
        "database": "${process.env.DATABASE_NAME || 'strapi'}",
        "username": "${process.env.DATABASE_USERNAME || ''}",
        "password": "${process.env.DATABASE_PASSWORD || ''}",
        "ssl": "${process.env.DATABASE_SSL || false}"
      },
      "options": {
        "ssl": "${process.env.DATABASE_SSL || false}",
        "authenticationDatabase": "${process.env.DATABASE_AUTHENTICATION_DATABASE || ''}"
      }
    }
  }
}
```

Here, it can be seen that all settings are taken from environment variables. This more than sufficient for WOZiTech CMS as it will be deployed as a docker image, and docker supports passthrough on environment variables.

_No need to bootsrap any specific sensisble data loading solution._
# Custom Types
The primary purrpose of strapi as a CMS is to define custom types (effectively data tables) to hold a custom list of data. Each custom type becomes an API, to which you can then assign RESTful endpoints and look down access to those endpoint with authentication.

You can use the admin UI to build new custom types (it is easy) - (TBC - you can also create them manually -  but why would you when you have a good Admin UI to make it easier).

## Database
When creating a new custom type, nothing is created in the database, until you add one record.

With a new installation, only the following collections are created within the MongoDB instance:
* `core_store`
* `strapi_administrator`
* `users-permissions_permission`
* `users-permissions_role`
* `users-permissions_user`

The `strapi_administrator` collection are the users that can login to the admin UI; these are special users.

`users-permissions_*` collections are all associated with the API users, and their permissions and to what custom content they can access via RESTful endpoints.

`core_store` lists the plugins installed and their configuration.

## Code
When you "save" a new custom type, for this example lets call it `MyCustomType`, the strapi admin creates the following five files (and then restarts the strapi backend instance to pick up on the new content:
* `api/mycustomtype/config/routes.json`
* `api/mycustomtype/controllers/MyCustomType.js`
* `api/mycustomtype/models/MyCustomType.js`
* `api/mycustomtype/models/MyCustomType.settings.js`
* `api/mycustomtype/services/MyCustomType.js`

There are no core files touched. `strapi` will rip through everything in `api/*` to process the custom types.

This means you create content types in development, test the APIs, commit to git and then redeploy to any target environment. 

## Database Again
If the table associated with the custom type does not exist in the target environment's database, then the table will be created once data is added.

Usual database backup and restore can manage the data in each environment.

## Changing A Custom Type
**Need to explore this in more detail.**

For example:

> what happens to data after a deployment when a field has been deleted from the custom type?

> what happens when restoring data after a custom type has been modifed?

> how to update data to backfill on new fields?


# Custom Config
Note - each of the strapi environments (`...config/environments/<env>`) has a `custom.json` file which can be used to add environment specific customised definitions; viz. target WOZiTech CV microservice API  endpoints.

Don't forget to use `process.env.<var>` to hold anything sensitive.