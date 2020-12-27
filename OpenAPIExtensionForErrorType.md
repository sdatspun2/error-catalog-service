# OpenAPI Extension for Error Type

[OpenAPI](https://swagger.io/specification/) specification (OAS) does not provide any schema element(s) (fields, objects) to list errors that various operations of the service can emit. However, OAS allows to define [extensions](https://swagger.io/specification/#specification-extensions). To relate error types used by an API to those defined in [Error Catalog Service](README.md), we have defined an extension.

Using an [OpenAPI Document](https://swagger.io/specification/#definitions) (API contract) annotated with this extension, various tools or software systems including but not limited to API portal, service implementing the API contract, CI/CD utilities and QA tests can retrieve error types from the Error Catalog Service.


### `x-error-types`

This extension lists an array of URIs of error types that correspond to the errors emitted by operations annotated using this extension.

```
{
      "type": "array",
      "description": "An array of error types, each identified using a URI.",
      "items": {
        "type": "string",
        "format": "uri",
        "description": "A URI reference that identifies an error type in an error catalog."
      }
}
```

#### Example

The following example of API contract relates five error types to an API operation identified as `post-card`. This relationship is annotated using the extension `x-error-types`. Error types are defined in error catalog identified as `1` for generic error types and in error catalog identied as `2` for payment method specific errors.

```
  "paths": {
    "/v1/issuing/cards": {
      "post": {
        "summary": "Create a card",
        "tags": [
          "post-card"
        ],
        "operationId": "post-card",
        "x-error-types": [
          "/v1/error/error-catalogs/1/error-types/resource_not_found", 
          "/v1/error/error-catalogs/2/error-types/card_type_invalid", 
          "/v1/error/error-catalogs/2/error-types/brand_invalid", 
          "/v1/error/error-catalogs/2/error-types/last4_invalid", 
          "/v1/error/error-catalogs/2/error-types/expiry_invalid"
       ],
        "responses": {
          "201": {
            "description": "OK",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "#/components/schemas/card"
                }
              }
            }
          },
          "default": {
            "description": "unexpected error",
            "content": {
              "application/json": {
                "schema": {
                  "$ref": "./models/error.json"
                }
              }
            }
          }
        },
        "description": "Create a card"
      },
      "parameters": []
    }
```

### Use Cases

1. API portals can show a list of error types associated with an API using the Error Catalog Service.
2. Linters can validate if an API contract annotated with this extension relates to correct error types in the Error Catalog Service.
3. API automation tests can validate if the error responses emitted by the service implmenting the API are as documented in the API contract.

### Example API definition

The [API contract](error-catalog-service.json) for the Error Catalog Service itself is annotated with `x-error-types` extension.

**Note**: Since this is just an API (no implementation), The API contract uses a fictitous variable called `{ecid}` for identifying error catalogs.

## Community

* Contribute via issues and pull requests.
* Contributors: people and organizations who helped us get started or are actively working on the definition of Error Catalog Service API, various extensions and implementation of the service in various languages.
* Demos & open source -- if you have something to share about your use of Error Catalog Service, please submit a PR!
