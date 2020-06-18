# Authorization workshop

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

