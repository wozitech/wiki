<!-- TITLE: strapi -->
<!-- SUBTITLE: A quick guide to strapi -->

# Reference
https://strapi.io/documentation/

# Database Connection
Having created the initial strpai application using the `strpai` CLI, I recommended using a local MongoDB database (with or without authentication).
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