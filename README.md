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

### Getting the Martini Package from TORO Marketplace

You can also get this package via TORO Marketplace. See our documentation on [How to import a Martini Package from Marketplace](https://docs.torocloud.com/martini/latest/developing/package/importing/#from-the-marketplace).

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

### Sending Authenticated Requests

You can use your ECC account to send authenticated request to the API. Your ECC credentials must be sent in the `Authorization` header in the HTTP request

#### To authenticate a request with basic authentication

1. Combine your email and password with a colon (`:`). e.g. `jdoe@mailinator.com:pa$$w0rd`
2. Encode the resulting string in Base64
3. Include an Authorization header in the HTTP request containing the base64-encoded string. Example: ```
Authorization: Basic amRvZUBtYWlsaW5hdG9yLmNvbTpwYSQkdzByZA==```

### Operations

The base url is `<host>/api/mock-inventory-api` where `host` is the location where the Martini is deployed. By default, it's `localhost:8080`.

`GET /items`

Returns a list of inventory items

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-inventory-api/items \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below:
```
{
    "result": "SUCCESS",
    "message": "Successfully fetched items.",
    "item": [
        {
            "itemName": "Mouse",
            "sku": "1002",
            "dateCreated": 1605745246000,
            "dateUpdated": 1605745246000,
            "quantityOnHand": 50,
            "quantityAvailable": 50,
            "customFields": {

            },
            "id": "59dc3c20-6c47-4800-baaa-1a2fa0d176e0"
        },
        ...
        {
            "itemName": "Monitor",
            "sku": "1001",
            "dateCreated": 1605745246000,
            "dateUpdated": 1605745246000,
            "quantityOnHand": 50,
            "quantityAvailable": 50,
            "customFields": {

            },
            "id": "fddb1ff0-1b10-4c75-b44d-aa2338ad3b65"
        }
    ]
}
```

`POST /items`

Create a new inventory item

**Sample Request**

**curl**
```
curl -X POST \
  http://localhost:8080/api/mock-inventory-api/items \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
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
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
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
    "message": "Successfully created item with id: 5cd2ac33-7004-4e74-9200-b3b1112a6f25.",
    "item": {
        "itemName": "Mac-mini",
        "sku": "1005",
        "dateCreated": 1605745246000,
        "dateUpdated": 1605745246000,
        "quantityOnHand": 50,
        "quantityAvailable": 50,
        "customFields": {

        },
        "id": "5cd2ac33-7004-4e74-9200-b3b1112a6f25"
    }
}
```

`GET /items/<itemId>`

Returns a single inventory item that matches the given `itemId`

**Sample Request**

**curl**
```
curl -X GET \
  http://localhost:8080/api/mock-inventory-api/items/82ba2d7a-a40b-42cf-9e52-1d86fe37896c \
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
  -H 'Accept: application/json'
```

**Sample Response**

If the request is successful, it will return an HTTP status code `200` with the response payload below.
```
{
    "result": "SUCCESS",
    "message": "Successfully fetched inventory item.",
    "item": {
        "itemName": "Mac-mini",
        "sku": "1005",
        "dateCreated": 1605745246000,
        "dateUpdated": 1605745246000,
        "quantityOnHand": 50,
        "quantityAvailable": 50,
        "customFields": {

        },
        "id": "82ba2d7a-a40b-42cf-9e52-1d86fe37896c"
    }
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
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
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
    "message": "Successfully updated item with id \"82ba2d7a-a40b-42cf-9e52-1d86fe37896c\".",
    "item": {
        "itemName": "Industrial Shampoo",
        "sku": "1005",
        "dateCreated": 1605745246000,
        "dateUpdated": 1605857738000,
        "quantityOnHand": 50,
        "quantityAvailable": 50,
        "customFields": {

        }
    }
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
  -H 'Authorization: Basic <base64-encoded-credentials-string>' \
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
  http://localhost:8080/api/mock-inventory-api/items/5cd2ac33-7004-4e74-9200-b3b1112a6f24 \
  -H 'Accept: application/json' \
  -H 'Authorization: Basic <base64-encoded-credentials-string>'
```

**Sample Response**

If the request is successful, it will return an HTTP status code of `200`.
```
{
    "result": "Success",
    "message": "Successfully deleted item with id 5cd2ac33-7004-4e74-9200-b3b1112a6f24"
}
```
