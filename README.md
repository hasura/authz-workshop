# Authorization workshop

## Getting started

You will work with local Docker containers for this tutorial; see the services "postgres" and "graphql-engine" in `docker-compose.yaml`

To begin, use `docker-compose up -d`

View your new Hasura GraphQL Engine Console at [http://localhost:8080](http://localhost:8080) (admin secret from docker-compose.yaml: `adminsecret`)

_Note_ To end a session, use `docker-compose down`

In case there is difficulty with Docker locally, use a free Heroku deployment instead:

[![Deploy to
Heroku](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy?template=https://github.com/hasura/graphql-engine-heroku)

![Create New App - Heroku](https://graphql-engine-cdn.hasura.io/heroku-repo/assets/create_new_app_heroku_3.png)


## Loading initial data

#### For local Docker setup

Use the following command to set up initial tables and data into your postgres container:

```
psql postgres://postgres:mypassword@localhost:6432/postgres < chinook.sql

```

If you do not have `psql` available, you can copy the chinook.sql file to the postgres container and execute the `psql` command via inside it:

```
docker cp chinook.sql <postgres-container-ID>:/
docker exec -ti <postgres-container-ID> /bin/bash
psql -U postgres < chinook.sql

```

_Note_ You can find `<postgres-container-ID>` with `docker ps`

#### For a Heroku deployment

From the Heroku app dashboard (`dashboard.heroku.com/apps/<my-app-name>`), navigate to the Settings tab -> Reveal config vars -> DATABASE_URL. Use the following command:

```
psql <DATABASE_URL> < chinook.sql

```

or, lacking psql, use the following (might need to run `heroku login` first):

```
heroku pg:psql -a <my-app-name> < chinook.sql

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

