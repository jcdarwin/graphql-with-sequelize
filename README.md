# What is this?

This is a fork of [Lee Benson's graphql-with-sequelize tutorial](https://github.com/leebenson/graphql-with-sequelize), as [covered in this video](https://www.youtube.com/watch?v=DNPVqK_woRQ). 

Lee's tutorial is extensive and easy to follow, and is a good way to get up to speed with the concept of using GraphQL as a server-side wrapper around existing APIs.
In this case, we expose a relational database via GraphQL, though this could as easily be a conventional REST API exposed via the [request-promise module](https://www.npmjs.com/package/request-promise).  

Instead of postgres, we've gone with sqlite as it's more lightweight, and doesn't require any prerequisites apart from what's in this repo.


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

A potted query for posts:
http://localhost:3000/graphql?query=%7B%0A%20%20posts%20%7B%0A%20%20%20%20title%0A%20%20%20%20person%20%7B%0A%20%20%20%20%20%20firstName%0A%20%20%20%20%20%20lastName%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D%0A

    {
        posts {
            title
            person {
                firstName
                lastName
            }
        }
    }

A potted query for people:
http://localhost:3000/graphql?query=%7B%0A%20%20people%20%7B%0A%20%20%20%20firstName%0A%20%20%20%20lastName%0A%20%20%20%20email%0A%20%20%20%20posts%20%7B%0A%20%20%20%20%20%20title%0A%20%20%20%20%20%20content%0A%20%20%20%20%7D%0A%20%20%7D%0A%7D%0A

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

A potted mutation to create a new person: 
http://localhost:3000/graphql?query=mutation%20addPerson%20%7B%0A%20%20addPerson(firstName%3A%20%22Jason%22%2C%20lastName%3A%20%22Darwin%22%2C%20email%3A%20%22jcdarwin%40gmail.com%22)%20%7B%0A%20%20%20%20id%0A%20%20%7D%0A%7D%0A&operationName=addPerson

    mutation addPerson {
        addPerson(firstName: "Jason", lastName: "Darwin", email: "jcdarwin@gmail.com") {
            id
        }
    }


## Useful resources:

* The Faker API: https://github.com/FotoVerite/Faker.js