# RAML API Specification

## Specification

The main RAML document that defines the API, including its resources, methods, and data types. It serves as the primary blueprint for how the API should function and what it exposes to consumers.

### Main RAML File (`api.raml`)

```raml
#%RAML 1.0
title: Comprehensive API
version: v1
baseUri: http://api.example.com/{version}
protocols: [ HTTPS ]
mediaType: application/json

uses:
  CommonTypes: types.raml
  Examples: examples.raml
  Annotations: annotations.raml
  SecuritySchemes: security.raml

documentation:
  - title: Overview
    content: |
      This API provides comprehensive information and operations on various resources.

securitySchemes:
  OAuth_2_0: !include security.raml

traits:
  paginated: !include traits.raml

resourceTypes:
  collection: !include resourceTypes.raml

types:
  UserRecord: CommonTypes.UserRecord
  UserResponse: CommonTypes.UserRecord

/createrecord:
  post:
    description: Endpoint to create a user record.
    body:
      application/json:
        type: UserRecord
        description: User record details to be created.
        example: Examples.UserExample
    responses:
      200:
        description: Successful response containing the created user record.
        body:
          application/json:
            type: UserResponse
            example: Examples.UserExample

/users:
  type: collection
  get:
    description: Retrieve a list of users.
    responses:
      200:
        body:
          application/json:
            type: UserRecord[]
            example: Examples.UserListExample
  post:
    description: Create a new user.
    body:
      application/json:
        type: UserRecord
        example: Examples.UserExample
    responses:
      201:
        body:
          application/json:
            type: UserResponse
            example: Examples.UserExample
  /{userId}:
    uriParameters:
      userId:
        type: integer
        description: The ID of the user.
        example: 123
    get:
      description: Retrieve a specific user.
      responses:
        200:
          body:
            application/json:
              type: UserResponse
              example: Examples.UserExample
    put:
      description: Update a specific user.
      body:
        application/json:
          type: UserRecord
          example: Examples.UserExample
      responses:
        200:
          body:
            application/json:
              type: UserResponse
              example: Examples.UserExample
    delete:
      description: Delete a specific user.
      responses:
        204:
          description: User deleted successfully.

annotationTypes:
  deprecated: Annotations.deprecated
  since: Annotations.since
```

## Trait

A reusable set of properties that can be applied to multiple methods across different resources. Traits typically include elements like query parameters, headers, and responses that are common to multiple endpoints.

### Traits File (`traits.raml`)

```raml
#%RAML 1.0 Library
traits:
  paginated:
    queryParameters:
      page:
        description: The page number to retrieve.
        type: integer
        example: 1
      pageSize:
        description: The number of items per page.
        type: integer
        example: 10
```

## Resource Type

A reusable blueprint for a resource, defining common behavior and properties that multiple resources can inherit. Resource types can include methods, descriptions, and other elements that are shared across different resources.

### Resource Types File (`resourceTypes.raml`)

```raml
#%RAML 1.0 Library
resourceTypes:
  collection:
    get:
      is: [ paginated ]
      responses:
        200:
          body:
            application/json:
              type: <<resourcePathName | !singularize>>[]
```

## Data Type

A definition of a structure for the data used in the API. Data types describe the shape of request and response bodies, including their properties, types, and any validation rules.

### Types File (`types.raml`)

```raml
#%RAML 1.0 Library
types:
  CommonTypes.UserRecord:
    type: object
    properties:
      id:
        type: integer
        required: true
      name:
        type: string
        required: true
      phone:
        type: integer
        required: true
      address:
        type: string
        required: true
```

## Example

Concrete instances of data that illustrate how request and response bodies should look. Examples are used to demonstrate valid data structures and values for different types and resources in the API.

### Examples File (`examples.raml`)

```raml
#%RAML 1.0 Library
examples:
  UserExample:
    value:
      id: 123
      name: "Gaurav"
      phone: 378466474
      address: "Dwarka Nagar"
  UserListExample:
    value:
      - id: 123
        name: "Gaurav"
        phone: 378466474
        address: "Dwarka Nagar"
      - id: 124
        name: "John Doe"
        phone: 1234567890
        address: "Another Street"
```

## Annotation Type

Custom metadata that can be applied to various parts of the RAML specification. Annotation types allow API designers to add extra information, such as deprecation warnings or versioning details, that can be used by tools and consumers.

### Annotations File (`annotations.raml`)

```raml
#%RAML 1.0 Library
annotationTypes:
  deprecated:
    type: string
    description: Indicates that an element is deprecated and provides a reason.
  since:
    type: date-only
    description: Indicates the version since which an element is available.
```

## Security Scheme

Definitions of the authentication and authorization mechanisms used by the API. Security schemes describe how to secure the API endpoints, including details like OAuth 2.0 flows, API keys, and other security protocols.

### Security Schemes File (`security.raml`)

```raml
#%RAML 1.0 SecurityScheme
description: OAuth 2.0 security scheme
type: OAuth 2.0
settings:
  authorizationUri: https://example.com/oauth/authorize
  accessTokenUri: https://example.com/oauth/token
  authorizationGrants: [ authorization_code, client_credentials ]
  scopes: [ read, write ]
```

## Overlay

A RAML document that extends an existing API definition to add, modify, or refine its properties without changing the original specification. Overlays allow for API customization for different use cases or environments.

### Overlay File (`overlay.raml`)

```raml
#%RAML 1.0 Overlay
extends: api.raml
title: Overlay for API
version: v1-overlay

/users:
  description: |
    This overlay adds additional information to the users resource.
```

## Extension

Similar to an overlay, an extension modifies an existing API definition but is intended to extend the base API with additional functionality or details, typically for specific versions or implementations.

### Extension File (`extension.raml`)

```raml
#%RAML 1.0 Extension
extends: api.raml
title: Extension for API
version: v1-extension

/users:
  post:
    description: |
      This extension modifies the POST method description for creating a user.
```

## Library

A modular file containing reusable types, traits, resource types, and security schemes that can be included in multiple RAML specifications. Libraries help in organizing and reusing common definitions across different APIs.

### Library File (`library.raml`)

```raml
#%RAML 1.0 Library
types:
  CommonTypes: types.raml
  Examples: examples.raml
  Annotations: annotations.raml
  SecuritySchemes: security.raml
  Traits: traits.raml
  ResourceTypes: resourceTypes.raml
```

## User Documentation

Sections within the RAML specification that provide information and guidance to API consumers. User documentation can include overviews, usage examples, and detailed explanations of API functionality.

### Documentation Section (`documentation.raml`)

```raml
#%RAML 1.0
title: API Documentation
version: v1
content:
  - title: Overview
    content: |
      This documentation provides detailed information about the API endpoints and their usage.
  - title: Examples
    content: |
      ### Example of Usage
      To retrieve a list of users:
      ```
      GET /v1/users
      ```
      Example Response:
      ```json
      [
        {
          "id": 123,
          "name": "Gaurav",
          "phone": 378466474,
          "address": "Dwarka Nagar"
        },
        {
          "id": 124,
          "name": "John Doe",
          "phone": 1234567890,
          "address": "Another Street"
        }
      ]
      ```

