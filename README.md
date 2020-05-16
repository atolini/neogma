# neogma

### Object-Graph-Mapping neo4j framework, fully-typed with TypeScript, for easy and flexible node and relationship operations

## Overview
Neogma uses Model definitions to simplify and automate lots of operations. Alternatively, a query runner is also provided for running operations directly with Javascript object, without a Model definition.

By using Typescript, a user can also benefit from Neogma's type safety in all its parts. The types are build-in and used in neogma's core, so no external typings are needed.

## [The full documentation can be found here](https://themetalfleece.github.io/neogma-docs)

A simple example is the following:
```ts
// creates a new Users node (with the labels specified in the Users model definition)
const user = await Users.createOne({
    name: 'John',
    email: 'john@email.com'
});

console.log(user.name); // 'John'

user.name = 'Jack';
// updates the node's name in the database
await user.save();
```

Another feature is to associate the created nodes with other nodes, which will either be created now or by matched by a where clause. This supports infinite nesting for maximum flexibility.
```ts
await Users.createMany([
    {
        name: 'John',
        email: 'john@email.com',
        RelatedNodesToAssociate: {
            Orders: {
                attributes: [{
                    // creates a new Order node with the following properties, and associates it with John
                    id: '1',
                    status: 'confirmed'
                }],
                where: {
                    params: {
                        // matches an Order node with the following id and associates it with John
                        id: '2'
                    }
                }
            }
        }
    }
]);

// find the Order node which is created in the above operation
const order = await Orders.findOne({
    where: {
        id: '1'
    }
});

console.log(order.status); // confirmed
```
All of the above run in a single statement for max performance. The association alias (here `Orders`) is defined in the model along with the relationship name, direction and properties.

All the user-specified values are automatically used in the query with bind parameters. Neogma also offers helpers to easily create your own queries with generated where clauses and bind parameters.