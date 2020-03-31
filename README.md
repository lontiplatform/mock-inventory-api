# Mock Inventory API
A simple inventory api with CRUD operations

### Prerequisites

  - Apache Maven 3+
  - [Martini Desktop](https://www.torocloud.com/martini/download)

### Building the Martini Package

```
$ mvn clean package
```
This will create a ZIP file named `mock-inventory-api-bin.zip` containing all the files (services, configurations, etc.) needed under the `target` folder. This ZIP file is what we call a [Martini Package](https://docs.torocloud.com/martini/latest/developing/package/) which then you can import in Martini Desktop to get started. You can learn more how to import a Martini Package by visiting our [documentation](https://docs.torocloud.com/martini/latest/developing/package/importing/)

### Usage
This package exposes operations for a simple inventory REST API that allows you to do CRUD operations. You can find the [Gloop REST API](https://docs.torocloud.com/martini/latest/developing/gloop/api/rest/) file at `io/toro/mock/inventory/api/Inventory` under the `code` folder after importing the package to your Martini Desktop application.

### Setup
This Martini Package automatically sets up the required resources for you. During the package startup, Martini will execute the configured _Startup Service_ that initializes the database to be used for storing the record that will be creating for using this mock api.

This package uses HSQL as the default datastore but you can change this to MySQL by updating the [package properties](https://docs.torocloud.com/martini/latest/developing/package/properties/) located at [conf](https://docs.torocloud.com/martini/latest/developing/package/directory/) folder. Below is what the contents of the properties file look like

```
# Flag used for testing
debug.enabled=true

# The database type to be used
database.type=hsql

# HSQL database configuration to be used for testing environment
hsql.driver=org.hsqldb.jdbc.JDBCDriver
hsql.connection.url=jdbc:hsqldb:file:${toroesb.home}data/hsql/mock_inventory_api.db;hsqldb.tx=MVCC;sql.syntax_mys=true
hsql.username=sa
hsql.password=

# MySQL database configuration to be used in production environment
mysql.driver=com.mysql.cj.jdbc.Driver
mysql.connection.url=jdbc:mysql://<host>/<database-to-use>
mysql.username=root
mysql.password=
```
* `debug.enabled` when set to `true` initializes the database with dummy data when the package starts. The initialized data are also removed when the package stops or is unloaded. Set the value of this property to `false` if you want to keep your data when doing a restart. You can also set this property to `true` when the package starts and then set to `false` afterwards so that you'll have the data initialized on startup, and keep the data when the package or the Martini instance do a restart
* `database.type` sets the database provider the Martini package will use. If you will use the default hsql config, you don't need to change anything in the hsql configuration. **Note**: If you will use a different hsql database, make sure that you add `sql.syntax_mys=true` in the connection properties. This ensures that the SQL query from the SQL Services in this package will be compatible with hsql.
 
### Custom Fields

The inventory API supports custom fields but note that you can only add and edit custom fields as of this time.

### Operations

The base url is `<host>/api/mock-inventory-api` where `host` is the location where the Martini is deployed. By default, it's `localhost:8080`.

`GET /items`

Returns a list of inventory items

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-inventory-api/items \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
[
    {
        "itemName": "iMac",
        "sku": "1006",
        "dateCreated": 1583254718000,
        "dateUpdated": 1583254718000,
        "quantityOnHand": 50,
        "quantityAvailable": 50,
        "customFields": {

        },
        "id": "4933145d-41a9-45a7-8c92-d895696037cc"
    },
    {
        "itemName": "Mouse",
        "sku": "1002",
        "dateCreated": 1583254718000,
        "dateUpdated": 1583254718000,
        "quantityOnHand": 50,
        "quantityAvailable": 50,
        "customFields": {

        },
        "id": "81fe47cc-67d1-4ba0-9490-74253fffc5a2"
    },
    ...
    {
        "itemName": "Monitor",
        "sku": "1001",
        "dateCreated": 1583254718000,
        "dateUpdated": 1583254718000,
        "quantityOnHand": 50,
        "quantityAvailable": 50,
        "customFields": {

        },
        "id": "c6b5a092-bf44-4eb9-9594-b4d4707ecff2"
    },
    {
        "itemName": "Mousepad",
        "sku": "1004",
        "dateCreated": 1583254718000,
        "dateUpdated": 1583254718000,
        "quantityOnHand": 50,
        "quantityAvailable": 50,
        "customFields": {

        },
        "id": "ea7c85e5-a9b1-435e-94d3-eae83497101f"
    }
]
```

`POST /items`

Create a new inventory item

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-inventory-api/items \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "itemName": "Shampoo",
    "sku": "smp-1",
    "quantityOnHand": "30",
    "quantityAvailable": "20"
}'
```

**Creating an inventory item with custom field**
```
curl -X POST \
  http://localhost:8080/api/mock-inventory-api/items \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "itemName": "Shampoo",
    "sku": "smp-1",
    "quantityOnHand": "30",
    "quantityAvailable": "20",
    "customField": {
		"testField": "test"
    }
}'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `201` with the response payload similar below.
```
{
    "result": "SUCCESS",
    "message": "Successfully created item with id: 82ba2d7a-a40b-42cf-9e52-1d86fe37896c."
}
```

`GET /items/<itemId>`

Returns a single inventory item that matches the given `itemId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-inventory-api/items/82ba2d7a-a40b-42cf-9e52-1d86fe37896c \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "itemName": "Shampoo",
    "sku": "smp-1",
    "dateCreated": 1583256393000,
    "dateUpdated": 1583256808000,
    "quantityOnHand": 30,
    "quantityAvailable": 20,
    "customFields": {
        "testField": "test"
    },
    "id": "82ba2d7a-a40b-42cf-9e52-1d86fe37896c"
}
```

`PATCH /items/<itemId>`

Updates the inventory item that matches the given `itemId`

**Sample Request with custom field**

**curl**
```
curl -X PATCH \
  http://localhost:8080/api/mock-inventory-api/items/82ba2d7a-a40b-42cf-9e52-1d86fe37896c \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "name": "Industrial Shampoo",
    "customFields": {
        "testField": "updated field"
    }
}'
```

**Sample Response** 

If the request is successful, it will return an HTTP status code of `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully updated item with id \"82ba2d7a-a40b-42cf-9e52-1d86fe37896c\"."
}
```

`PATCH /items/<itemId>/<type>`

Used for updating either `quantity-on-hand` or `quantity-available` of an inventory item that matches the `<itemId>`.

| Name | Type        |                                                                                                         |
|------|-------------|---------------------------------------------------------------------------------------------------------|
| type | Path        | applicable values: `quantity-on-hand` or `quantity-available`                                           |
| item | JSON        | `quantity` refers to the amount of change in quantity, and `action` is for doing an `increase` or `decrease` in `quantity`   |

**Sample Request**

**curl**
```
curl -X PATCH \
  http://localhost:8080/api/mock-inventory-api/items/82ba2d7a-a40b-42cf-9e52-1d86fe37896c/quantity-on-hand \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
    "quantity": "10",
    "action": "increase"
}'
```

**Sample Response**

If the requst is successful, it will return an HTTP status code of `200` with the response payload below:

```
{
    "result": "SUCCESS",
    "message": "Successfully updated quantity-on-hand of item with id: 82ba2d7a-a40b-42cf-9e52-1d86fe37896c by 10."
}
```

`DELETE /items/<itemId>`

Deletes a inventory item that matches the `itemId`

**Sample Request**

**curl**
```
curl -X DELETE \
  http://localhost:8080/api/mock-inventory-api/items/82ba2d7a-a40b-42cf-9e52-1d86fe37896c
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `204`.
