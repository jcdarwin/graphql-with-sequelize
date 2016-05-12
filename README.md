# What is this?

This is a fork of [Lee Benson's graphql-with-sequelize tutorial](https://github.com/leebenson/graphql-with-sequelize), as [covered in this video](https://www.youtube.com/watch?v=DNPVqK_woRQ). 

Lee's tutorial is extensive and easy to follow, and is a good way to get up to speed with the concept of using GraphQL as a server-side wrapper around existing APIs.
In this case, we expose a relational database via GraphQL, though this could as easily be a conventional REST API exposed via the [request-promise module](https://www.npmjs.com/package/request-promise).  

Instead of postgres, we've gone with sqlite as it's more lightweight, and doesn't require any prerequisites apart from what's in this repo.

## Installation

Install our node dependencies:

    # presumes you have node and npm
    npm install
    
Our editor of choice is [Visual Studio Code](https://code.visualstudio.com/Docs/languages/javascript#_javascript-projects-jsconfigjson), and as we want to use intellisense, we'll install the various typescript definitions:

    # install typings so we can use typescript type definitions
    # https://code.visualstudio.com/Docs/runtimes/nodejs#_typings
    npm install -g typings

    # Use typings to search for and install typescript definitions for node
    typings search node
    typings install node --ambient --save

    # Use typings to install type definitions for express
    typings install express serve-static express-serve-static-core --ambient --save

    typings search GraphQL
    typings install express-graphql --ambient --save

    typings search sequelize
    typings install sequelize --ambient --save

The typings definitions get used as we've got a `jsconfig.json` file that indicates to Visual Studio Code that we're using Typescript for ES6 commonjs modules:

    {
        "compilerOptions": {
            "target": "ES6",
            "module": "commonjs",
            "allowSyntheticDefaultImports": true
        },
        "exclude": [
            "node_modules"
        ]
    }

     
## Create a database with sample data:

    npm run db

To inspect the data using sqlite:

    sqlite3 database.sqlite

    # Following are commands entered at the sqlite command line    
    .help

    .schema posts
    .schema people

    select * from posts;
    select * from people;
    
    .q

## Start the server 

The following starts our express server, which populates our database afresh with dummy data, and serves GraphiQL at http://localhost:3000:  

    npm start

Once started, we can perform queries in the GraphiQL interface:

[A potted query for posts](http://localhost:3000/graphql?query=%7B%0A%20%20posts%20%7B%0A%20%20%20%20title%0A%20%20%20%20person%20%7B%0A%20%20%20%20%20%20firstName%0A%20%20%20%20%20%20lastName%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D%0A):

    {
        posts {
            title
            person {
                firstName
                lastName
            }
        }
    }

[A potted query for people](http://localhost:3000/graphql?query=%7B%0A%20%20people%20%7B%0A%20%20%20%20firstName%0A%20%20%20%20lastName%0A%20%20%20%20email%0A%20%20%20%20posts%20%7B%0A%20%20%20%20%20%20title%0A%20%20%20%20%20%20content%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D%0A):

    {
        people {
            firstName
            lastName
            email
            posts {
                title
                content
            }
        }
    }

[A potted mutation to create a new person](http://localhost:3000/graphql?query=mutation%20addPerson%20%7B%0A%20%20addPerson(firstName%3A%20%22Jason%22%2C%20lastName%3A%20%22Darwin%22%2C%20email%3A%20%22jcdarwin%40gmail.com%22)%20%7B%0A%20%20%20%20id%0A%20%20%7D%0A%7D%0A&operationName=addPerson): 

    mutation addPerson {
        addPerson(firstName: "Jason", lastName: "Darwin", email: "jcdarwin@gmail.com") {
            id
        }
    }

## Generate a GraphQL schema from a SQL database

We can use [sql-to-graphql](https://www.npmjs.com/package/sql-to-graphql) to generate our GraphQL schema from our SQLlite database: 

    npm install -g sql-to-graphql

    sql2graphql --output-dir test --es6 -b sqlite --database database.sqlite

    cd /Users/jasondarwin/workspace/graphql-with-sequelize/test
    npm install
    npm start

Then point your browser at http://localhost:3000


## Useful resources:

* The Faker API: https://github.com/FotoVerite/Faker.js