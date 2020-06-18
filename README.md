# Authorization workshop

## Getting started

We will work with your Heroku app, if you haven't created a Heroku app already then you can deploy using:

[![Deploy to
Heroku](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/hasura/graphql-engine-heroku)

![Create New App - Heroku](https://graphql-engine-cdn.hasura.io/heroku-repo/assets/create_new_app_heroku_3.png)

#### Add admin secret

1. From the Heroku app dashboard (`dashboard.heroku.com/apps/<my-app-name>`), navigate to the Settings tab -> Reveal config vars. 

2. Add a new config var:  `HASURA_GRAPHQL_ADMIN_SECRET: adminsecret` 


#### For local Docker setup

In case you prefer to work locally with docker containers instead:

Use `docker-compose up -d`

View your new Hasura GraphQL Engine Console at [http://localhost:8080](http://localhost:8080) (admin secret from docker-compose.yaml: `adminsecret`)

*Note*: To end a session, use `docker-compose down -v`


## Loading initial data

#### Via console
- Head to the Data tab and go to the Run SQL window
- Run northwind_ddl.sql
- Run northwind_data.sql

#### For a Heroku deployment

From the Heroku app dashboard (`dashboard.heroku.com/apps/<my-app-name>`), navigate to the Settings tab -> Reveal config vars -> DATABASE_URL. Use the following command:

```
psql <DATABASE_URL> < northwind_ddl.sql
psql <DATABASE_URL> < northwind_data.sql

```

or, lacking psql, use the following with the [heroku CLI](https://devcenter.heroku.com/articles/heroku-cli): (might need to run `heroku login` first):

```
heroku pg:psql -a <my-app-name> < northwind_ddl.sql
heroku pg:psql -a <my-app-name> < northwind_data.sql

```

#### For local Docker setup

Use the following command to set up initial tables and data into your postgres container:

```
psql postgres://postgres:mypassword@localhost:6432/postgres < northwind_ddl.sql 
psql postgres://postgres:mypassword@localhost:6432/postgres < northwind_data.sql 

```

If you do not have `psql` available, you can copy the `northwind_ddl.sql` and `northwind_data.sql` file to the postgres container and execute the `psql` command via inside it:


*Note*:  You can find `<postgres-container-ID>` with `docker ps`

```
docker cp northwind_ddl.sql <postgres-container-ID>:/
docker cp northwind_data.sql <postgres-container-ID>:/
docker exec -ti <postgres-container-ID> /bin/bash
psql -U postgres < northwind_ddl.sql
psql -U postgres < northwind_data.sql
```

## Track tables and foreign-key relations

![Track tables in console](images/Hasura_setup_track_tables.png)

Return to the Hasura GraphQL Engine console and select the Data tab. In the central view, there should be a section "Untracked tables or views" with several tables listed and a "Track All" option available. Select "Track All", and then "Track All" again for untracked foreign-key relations.

Now you're all set! You should see your tables listed in the left-hand panel.

Go to the Graphiql tab and start trying out queries, mutations, and subscriptions.


## Dataset

The Northwind dataset includes sample data for the following.

- Suppliers: Suppliers and vendors of Northwind
- Customers: Customers who buy products from Northwind
- Employees: Employee details of Northwind traders
- Products: Product information
- Shippers: The details of the shippers who ship the products from the traders to the end-customers
- Orders and Order_Details: Sales Order transactions taking place between the customers & the company


## External App

**Table**: Customer, Order, Shipper
**Role**: customer

Rules:

1. Customer can only select their own row in customer table
2. Customer can get their orders.
3. Customers can't view the employee_id of the order.
4. Customer can view the phone number of their shippers.


## Internal App

**Table**: employees
**Role**: employee, hr

Rules:

1. Employee can see and edit their own information.
2. Employee can see the information of their reportees.
3. HRs can see and edit information of all employees

## Public facing app

**Table**: products
**role**: api

Rules:

1. Only consumers with valid api key can see the products table

## RBAC

A role is a collection of permissions. Permissions determine what operations are allowed on a resource. 
When you grant a role to a user, all permissions in the role are automatically granted to the user.

Hasura has role-based schemas.

## ABAC

Attributes are values that are associated with a user or resource. 

Hasura can use user attributes and data attributes

## AWS IAM

- Action
- Resource
- Condition

## GCP IAM

https://hasura.io/blog/authorization-rules-for-multi-tenant-system-google-cloud/

- Every resource instance also has a role: Each bucket has a storage_admin, storage_editor, storage_viewer role associated with it

## Example JWT Payload

```
{
  "sub": "1234567890",
  "name": "John Doe",
  "admin": true,
  "iat": 1516239022,
  "https://hasura.io/jwt/claims": {
    "x-hasura-allowed-roles": ["employee"],
    "x-hasura-default-role": "employee",
    "x-hasura-employee-id": "2"
  }
}
```

[Encrypt with jwtsecret here](https://jwt.io/)
- Every resource instance also has a role: Each bucket has a storage_admin, storage_editor, storage_viewer role associated with it 


## Use this key

```
HASURA_GRAPHQL_JWT_SECRET: '{"type": "HS256", "key": "myjwtsecretkey111111111111111111111111111111111"}'
```
