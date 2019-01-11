# GraphQL Tutorial with Mongo DB and SQLite DB

A GraphQL server built with NodeJS using ExpressJS. Data persistence on the backend is provided by either a MongoDB DB or an SQLite DB.

![App Screenshot](https://raw.githubusercontent.com/smerth/graphql-mongo-mysql-tutorial/master/screenshot.png)

## Use

* clone the repo
* install dependancies

```bash
yarn install
```

* run the GraphQL server

```bash
yarn start
```

Running server on port `localhost:8080/graphiql`

You can perform queries and mutations through the GraphiQL interface for Freind entities and Alien entities.

For a full explanation of the build see the docs folder.

## Releases

* 1.0.0 - express server with es6 support.
* 1.1.0 - initial GraphQL setup.
* 1.2.0 - basic GraphQL schema.
* 1.3.0 - object types and fields.
* 1.4.0 - query and mutation types.
* 1.5.0 - the resolver and its role.
* 1.6.0 - scalar types.
* 1.7.0 - ennumeration types.
* 1.8.0 - list of types inside another.
* 1.9.0 - setup of graphql with mongodb.
* 1.10.0 - setup of eslint and prettier.
* 1.11.0 - data persistence with sql.
* 1.12.0 - update items with mutations.
* 1.13.0 - delete items with mutations.
* 1.14.0 - simple query to SQLite DB.
* 1.15.0 - query with arguments.
