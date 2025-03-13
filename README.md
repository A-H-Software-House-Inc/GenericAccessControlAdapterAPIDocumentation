# Generic Access Control Adapter Integration API Guide

**Document version 1.0.0 (2025-03-11)** <br> **Copyright © 2004-2025 A&H Software House, Inc.**

## Introduction

Welcome to the API guide for integrating access control systems with the Video Management System (VMS).

The VMS software includes a universal access control client, designed to seamlessly interact with designated API endpoints on access control system servers.

**Intended Audience:** This guide is intended for Access Control manufacturers and developers responsible for implementing API endpoints to integrate with the VMS.

To ensure compatibility with the VMS, it is essential that access control systems implement the API endpoints specified in this guide.


<div style="page-break-after: always;"></div>



[//]: <> (--------------------------- Table of Contents {{{ -------------------------------)

## Table of Contents

**Generic Access Control Adapter Integration API Guide**
- [Introduction](#introduction)
- [Table of Contents](#table-of-contents)

**General Information**
- [High-Level Overview and Purpose](#high-level-overview-and-purpose)
- [Compatibility Requirements](#compatibility-requirements)

**API Endpoints**
- [General API Endpoints Response Format](#general-api-endpoints-response-format)
- [Access Control Info, Supported Features, Endpoints, and Integrator ID](#access-control-info-supported-features-endpoints-and-integrator-id)
- Users
  - [All Users](#all-users)
  - [Retrieve Specific User](#retrieve-specific-user)
- Events
  - [All Events](#all-events)
  - [Retrieve Specific Event](#retrieve-specific-event)
- Actions
  - [All Actions](#all-actions)
  - [Retrieve or Trigger a Specific Action](#retrieve-or-trigger-a-specific-action)
- Parameters
  - [All Parameters](#all-parameters)
  - [Retrieve Specific Parameter](#retrieve-specific-parameter)
- Items
  - [All Items](#all-items)
  - [Retrieve or Trigger a Specific Item Action](#retrieve-or-trigger-a-specific-item-action)
- States
  - [All States](#all-states)
  - [Retrieve Specific State](#retrieve-specific-state)
- Notifications
  - [Subscribe to Notifications (Server-Sent Events)](#subscribe-to-notifications-server-sent-events)
- Localization IDs
  - [Supported Localization IDs](#supported-localization-ids)
    - [Door Action IDs](#door-action-ids)  
    - [Partition Action IDs](#partition-action-ids)
    - [Zone Action IDs](#zone-action-ids)
    - [Output Action IDs](#output-action-ids)
    - [State IDs](#state-ids)
    - [Door State IDs](#door-state-ids)
    - [Zone State IDs](#zone-state-ids)
    - [Partition State IDs](#partition-state-ids)

[//]: <> (--------------------------- Table of Contents }}} -------------------------------)


<div style="page-break-after: always;"></div>

***

# General Information

## High-Level Overview and Purpose
This document provides a comprehensive overview of the API endpoints and data formats that Access Control manufacturers or integrators must implement for seamless integration with the VMS Generic Access Control Adapter. By following these guidelines, Access Control systems can reliably communicate user information, events, door states, and other security-related data to the VMS.

**Benefits of Integration:**
- **Real-Time Monitoring:** Operators can view access control events and statuses and manage access control actions directly from the VMS.
- **Enhanced Security:** Streamlined communication ensures accurate and timely updates on access control events and statuses. Operators can respond quickly to security incidents.
- **Scalability:** The flexible API design accommodates a wide range of access control functionalities and actions. Addition of new features and functionalities can be easily supported.
- **Compatibility:** The standardized API endpoints ensure compatibility of the VMS Generic Access Control Adapter with a variety of access control systems on current and future versions of the VMS software without the need for additional development and VMS system updates.


## Compatibility Requirements
Achieving successful integration between your access control system and the VMS Generic Access Control Client requires the implementation of the outlined API endpoints. These endpoints have been carefully developed to accommodate a wide range of access control functionalities and actions.

<div style="page-break-after: always;"></div>

***

# API Endpoints

[//]: <> (--------------------------- GENERAL API RESPONSE FORMAT {{{ -------------------------------)

## General API Endpoints Response Format

To ensure consistency and ease of integration, most of the API endpoints in this guide (except for the [notifications](#subscribe-to-notifications-server-sent-events) endpoint) follow a common response format. This standardized format helps developers handle responses uniformly across different endpoints.

### Common Response Format

The response is a JSON object with the following properties:

```json
{
  "page": null,
  "nextPage": null,
  "error": null,
  "errorMessage": null,
  "data": {}
}
```

#### Response Object Properties
- `page` (number or null) - The current page number. If the response contains a list of items, this property indicates the current page number. In this case URL parameter `page` can be used to navigate through the pages. If the response contains a single item or there are no items available, this property is null.
- `nextPage` (boolean or null) - Indicates whether there is a next page available. If the response contains a list of items and there is a next page, this property is `true`. If there is no next page, this property is `false`. If the response contains a single item or there are no items available, this property is null.
- `error` (number or null) - The error code. If the request was successful, this property is null. If an error occurred, this property contains the error code.
- `errorMessage` (string or null) - The error message. If the request was successful, this property is null. If an error occurred, this property contains the error message.
- `data` (object or array) - The response data. This property contains the response data object or array of objects, depending on the endpoint.

#### Example Response

Here is an example response for an endpoint that returns a list of users. In this case, the response contains an array of user objects:

```json
{
  "page": 0,
  "nextPage": true,
  "error": null,
  "errorMessage": null,
  "data": [
    {
      "id": "user-00001",
      "firstName": "Jane",
      "lastName": "Cooper",
      "cards": ["card01", "card03"],
      "utcTime": 133808177708852381
    },
    {
      "id": "user-00002",
      "firstName": "Thomas",
      "lastName": "Johnson",
      "cards": ["card000004"],
      "utcTime": 133808177708897516
    }
  ]
}
```

[//]: <> (--------------------------- GENERAL API RESPONSE FORMAT }}} -------------------------------)

***

[//]: <> (--------------------------- ACCESS CONTROL INFO {{{ -------------------------------)

## Access Control Info, Supported Features, Endpoints, and Integrator ID

#### Endpoint
`GET /info`

#### Description
This endpoint provides essential information about the access control system, including its name, version, and supported features (e.g., doors, users, events, actions, etc.). It also returns the URLs used to perform actions on the system, as well as the current UTC time.

> **Important:** The `integrationId` field must contain a unique integrator ID (token in form of a GUID) provided specifically for you by the VMS manufacturer. This ID is required for the VMS to validate the integration connection, and must be kept confidential. Without a valid integrator ID, the access control system cannot authenticate with the VMS.

#### Sample Request
```plaintext
http://{access-control-server}/info
```

#### Sample Response
```json
{
  "page": null,
  "nextPage": null,
  "error": null,
  "errorMessage": null,
  "data": {
    "title": "GenericAdapterEmulator",
    "version": "1.0.0.0",
    "integrationId": "INTEGRATOR_ID_PROVIDED_BY_VMS_MANUFACTURER",
    "supports": [
      "doors",
      "users",
      "notifications",
      "events",
      "actions",
      "partitions",
      "zones",
      "outputs",
      "parameters",
      "states"
    ],
    "utcTime": 133808101744678128,
    "utcTimeString": "2025.01.08 11:42:54.467",
    "usersUrl": "/v1/users",
    "notificationsUrl": "/v1/notifications",
    "eventsUrl": "/v1/events",
    "actionsUrl": "/v1/actions",
    "parametersUrl": "/v1/parameters",
    "itemsUrl": "/v1/items",
    "statesUrl": "/v1/states"
  }
}
```

#### Access Control Information Data Object Properties
- `title` (string) - The name of the access control system.
- `version` (string) - The version of the access control system.
- `integrationId` (string) - The integrator ID provided by the VMS manufacturer. This ID is required for authentication and must be kept confidential.
- `supports` (array of strings) - The features supported by the access control system. The possible values are: `doors`, `users`, `notifications`, `events`, `actions`, `partitions`, `zones`, `outputs`, `parameters`, and `states`.
- `utcTime` (integer) - The current UTC time in FileTime format (number of 100-nanosecond intervals since January 1, 1601, UTC).
- `utcTimeString` (string) - The current UTC time as a formatted string in the `YYYY.MM.DD HH:MM:SS.mmm` format.
- `usersUrl` (string) - The URL to access user information.
- `notificationsUrl` (string) - The URL to access notifications.
- `eventsUrl` (string) - The URL to access events.
- `actionsUrl` (string) - The URL to access actions.
- `parametersUrl` (string) - The URL to access parameters.
- `itemsUrl` (string) - The URL to access items. Following items are supported: `door`, `partition`, `zone`, `output`.
- `statesUrl` (string) - The URL to access states.


#### Use Provided URLs for Subsequent Requests
- After retrieving the `/info` response, use the endpoint URLs provided (e.g., `usersUrl`, `notificationsUrl`, `eventsUrl`, `actionsUrl`, `parametersUrl`, `itemsUrl`, `statesUrl`) to access specific information and perform actions on the access control system.
- These endpoint URLs are relative to the base URL of your access control system server (e.g., `http://{access-control-server}`).


[//]: <> (--------------------------- ACCESS CONTROL INFO }}} -------------------------------)

<div style="page-break-after: always;"></div>

***

[//]: <> (---------------------------- PROVIDE ALL USERS {{{ ------------------------------)

## All Users

#### Endpoint
`GET {usersUrl}{?page}`

> **Note:** The exact URL to access user information is provided in the `/info` response under the `usersUrl` property. Same applies to other endpoints mentioned in this document.

#### Description
This endpoint provides the information about all available users and their cards (if supported by the access control system). The response contains an array of user objects, each representing a user in the access control system. Each user object contains information about the user, including the first name, last name, cards, and unique identifier.

#### URL Parameters
- `page` (number): Optional. The page number to retrieve. If the number of users exceeds the page size, the response will include the `nextPage` property with the next page number. The default page size is 10.

#### Sample Requests

```plaintext
/v1/users
/v1/users?page=0
```

> **Note:** This is a sample request using the `/v1/users` endpoint provided in the `/info` response. The actual URL may vary based on the access control system implementation.

#### Sample Response
```json
{
  "page": 0,
  "nextPage": false,
  "error": null,
  "errorMessage": null,
  "data": [
    {
      "id": "user-00001",
      "firstName": "Jane",
      "lastName": "Cooper",
      "cards": ["card01", "card03"],
      "utcTime": 133808177708852381
    },
    {
      "id": "user-00002",
      "firstName": "Thomas",
      "lastName": "Johnson",
      "cards": ["card000004"],
      "utcTime": 133808177708897516
    },
    {
      "id": "user-00003",
      "firstName": "Bill",
      "lastName": "Sunday",
      "cards": [],
      "utcTime": 133808177708897516
    },
    {
      "id": "user-00004",
      "firstName": "Megan",
      "lastName": "Lee",
      "cards": [],
      "utcTime": 133808177708897516
    },
    {
      "id": "user-00005",
      "firstName": "Arnold",
      "lastName": "Bronstein",
      "cards": ["card005", "card006", "card007", "card526"],
      "utcTime": 133808177708897516
    }
  ]
}
```

#### User Object Properties

- `id` (string) - The unique identifier of the user.
- `firstName` (string) - The first name of the user.
- `lastName` (string) - The last name of the user.
- `cards` (array of strings) - The list of card identifiers associated with the user.
- `utcTime` Optional. (integer) - The UTC time (FileTime format) when the user information was last updated.

[//]: <> (---------------------------- PROVIDE ALL USERS }}} ------------------------------)

***

[//]: <> (----------------------------- PROVIDE SPECIFIC USER {{{ ------------------------------)

## Retrieve Specific User

#### Endpoint
`GET {usersUrl}/{user_id}{?imageData}`

#### Description
This endpoint provides the information about a specific user, including the first name, last name, cards, and unique identifier. 

The cards are optional. If the access control system does not support cards, then the cards array should be empty.

Optionally, by setting the `imageData` query parameter to `true` the response can include the user's photo image data in Base64 format (PNG). If the access control system does not support user photos or the photo is not available, the `photoBase64` property should be `null`.


#### URL Parameters
- `user_id` (string): Required. The unique identifier of the user.
- `imageData` (boolean): Optional. Set it to `true` if you want to include `photoBase64` image data in the response. If it is set to `false` or omitted, the `photoBase64` property in the response should be `null`.

#### Sample Requests
```plaintext
/v1/users/user-00001
/v1/users/user-00001?imageData=false (same as /v1/users/user-00001)
/v1/users/user-00001?imageData=true
```

#### Sample Response
```json
{
  "page": null,
  "nextPage": null,
  "error": null,
  "errorMessage": null,
  "data": {
    "id": "user-00001",
    "firstName": "Jane",
    "lastName": "Cooper",
    "cards": ["card01", "card03"],
    "photoBase64": "iVBORw0KGgoAAAANSUhEUgAAACAAAAAgBAMAAACBVGfHAAAAJ1BMVEUpYadRHTx3MTOQSlCdX2W8//2/e37VfG3dp2LrnHLv3pfxyav///8PMNiPAAABWklEQVQoz23QvU6DUBTAcTZXq/EBoF/GrS2h2k0hYnUzQl0LTW3uqDUojiiSO5sQylhyOZ7ZpCE8gW/lpS2SGM/4yzn/4QjGnxH+B3M6UaVmBSZBTz7vNn+BAMjJ0rZKMJF6hBBFaZYAwBiDpV0Cv7gAQN+zSqCUIPKbbgUUkMJsu8GbxTDmFZEtuGkRmVYA6CIJKoCUpkiJUgKki5BXfgGSRYQqMl+xNvCehp8hPgHzxhtwa43wvlavwD7dWXV2v2hgl9CIVnoUXtIZrwpvhvnazrPnPBrQqXJgCMEavuM8PAZ/DY+j5VnuXOXzFq9aHBwOcRRndgOAV4VbR0/Ul+ldpNYDnHEwHJ3JJ1kUyhKhPCIYIw36/Tz/UMUBZT4HQ3N1Lc7mcucQwBtzuHF7D7ra08QWYlCAcTTRh46mivv8MQqH63YiD525uCchgM9Bklx5GKqSJA2QBj/tdNGoJOkY5wAAAABJRU5ErkJggg==",
    "utcTime": 133808177708852381
  }
}
```

#### User Data Object Properties

- `id` (string) - The unique identifier of the user.
- `firstName` (string) - The first name of the user.
- `lastName` (string) - The last name of the user.
- `cards` (array of strings) - The list of card identifiers associated with the user. If the access control system does not support cards, this property should be an empty array.
- `photoBase64` (string or null) - The user's photo image data in Base64 format (PNG) (optional). If the access control system does not support user's photo or photo is not available, this property should be `null`.
- `utcTime` Optional. (integer) - The UTC time (FileTime format) when the user information was last updated.

[//]: <> (----------------------------- PROVIDE SPECIFIC USER }}} ------------------------------)

***

[//]: <> (----------------------------- PROVIDE ALL EVENTS {{{ ------------------------------)

## All Events

#### Endpoint

`GET {eventsUrl}{?page}`

#### Description

This endpoint provides the information about all available events. The response contains an array of event objects, each representing an event in the access control system. Each event object contains information about the event, including the description, category, and unique identifier.

#### URL Parameters

- `page` (number): Optional. The page number to retrieve. If the number of events exceeds the page size, the response will include the `nextPage` property with the next page number. The default page size is 10.

#### Sample Requests

```plaintext
/v1/events
/v1/events?page=0
```

#### Sample Response

```json
{
    "page": 0,
    "nextPage": false,
    "error": null,
    "errorMessage": null,
    "data": [{
            "id": "1",
            "description": "Sample description of door event #1",
            "category": "door",
            "utcTime": 133809078112555418
        }, {
            "id": "2",
            "description": "Sample description of door event #2",
            "category": "door",
            "utcTime": 133809078112555418
        }, {
            "id": "3",
            "description": "Sample description of door event #3",
            "category": "door",
            "utcTime": 133809078112555418
        }, {
            "id": "10001",
            "description": "Sample description of system event #1",
            "category": "system",
            "utcTime": 133809078112555418
        }, {
            "id": "10002",
            "description": "Sample description of system event #2",
            "category": "system",
            "utcTime": 133809078112555418
        }, {
            "id": "10003",
            "description": "Sample description of system event #3",
            "category": "system",
            "utcTime": 133809078112555418
        }, {
            "id": "20001",
            "description": "Sample description of partition event #1",
            "category": "partition",
            "utcTime": 133809078112555418
        }, {
            "id": "20002",
            "description": "Sample description of partition event #2",
            "category": "partition",
            "utcTime": 133809078112555418
        }, {
            "id": "20003",
            "description": "Sample description of partition event #3",
            "category": "partition",
            "utcTime": 133809078112555418
        }, {
            "id": "30001",
            "description": "Sample description of zone event #1",
            "category": "zone",
            "utcTime": 133809078112555418
        }, {
            "id": "30002",
            "description": "Sample description of zone event #2",
            "category": "zone",
            "utcTime": 133809078112555418
        }, {
            "id": "30003",
            "description": "Sample description of zone event #3",
            "category": "zone",
            "utcTime": 133809078112555418
        }, {
            "id": "40001",
            "description": "Sample description of output event #1",
            "category": "output",
            "utcTime": 133809078112555418
        }, {
            "id": "40002",
            "description": "Sample description of output event #2",
            "category": "output",
            "utcTime": 133809078112555418
        }, {
            "id": "40003",
            "description": "Sample description of output event #3",
            "category": "output",
            "utcTime": 133809078112555418
        }
    ]
}

```

#### Event Object Properties

- `id` (string) - The unique identifier of the event.
- `description` (string) - The description of the event.
- `category` (string) - The category of the event.
- `utcTime` Optional. (integer) - The UTC time (FileTime format) when the event was updated.

[//]: <> (----------------------------- PROVIDE ALL EVENTS }}} ------------------------------)

***

[//]: <> (----------------------------- PROVIDE SPECIFIC EVENT {{{ ------------------------------)

## Retrieve Specific Event

#### Endpoint

`GET {eventsUrl}/{event_id}`

#### Description

This endpoint provides information about a specific event. The response contains an event object representing the event in the access control system. The event object contains information about the event, including the description, category, and unique identifier.

#### URL Parameters

- `event_id` (string): Required. The unique identifier of the event.

#### Sample Requests

```plaintext
/v1/events/1
```

#### Sample Response

```json
{
    "page": null,
    "nextPage": null,
    "error": null,
    "errorMessage": null,
    "data": {
        "id": "1",
        "description": "Sample description of door event #1",
        "category": "door",
        "utcTime": 133809078112555418
    }
}

```

#### Event Object Properties

- `id` (string) - The unique identifier of the event.
- `description` (string) - The description of the event.
- `category` (string) - The category of the event.
- `utcTime` Optional. (integer) - The UTC time (FileTime format) when the event was updated.

[//]: <> (----------------------------- PROVIDE SPECIFIC EVENT }}} ---------------------------)

***

[//]: <> (----------------------------- PROVIDE ALL ACTIONS {{{ ------------------------------)

## All Actions

#### Endpoint

`GET {actionsUrl}{?page}`

#### Description

This endpoint provides the information about all available actions. The response contains an array of action objects, each representing an action in the access control system. Each action object contains information about the action, including the description, category, and unique identifier.

#### URL Parameters

- `page` (number): Optional. The page number to retrieve. If the number of events exceeds the page size, the response will include the `nextPage` property with the next page number. The default page size is 10.

#### Sample Requests

```plaintext
/v1/actions
/v1/actions?page=0
```

#### Sample Response

```json
{
    "page": 0,
    "nextPage": false,
    "error": null,
    "errorMessage": null,
    "data": [{
            "id": "doorLock",
            "title": "Lock",
            "localizationId": "#DoorActionLock",
            "category": "door",
            "params": null,
            "utcTime": 133809078112455403
        }, {
            "id": "doorUnlock",
            "title": "Unlock",
            "localizationId": "#DoorActionUnlock",
            "category": "door",
            "params": null,
            "utcTime": 133809078112505844
        }, {
            "id": "doorUnlockTemp",
            "title": "Temp Unlock",
            "localizationId": "#DoorActionTempUnlock",
            "category": "door",
            "params": null,
            "utcTime": 133809078112505844
        }, {
            "id": "doorLockDown",
            "title": "Lock Down the door",
            "localizationId": "#DoorActionLockdown",
            "category": "door",
            "params": null,
            "utcTime": 133809078112505844
        }, {
            "id": "partitionArm",
            "title": null,
            "localizationId": "#PartitionActionArm",
            "category": "partition",
            "params": ["pin"],
            "utcTime": 133809078112505844
        }, {
            "id": "partitionDisarm",
            "title": null,
            "localizationId": "#PartitionActionDisarm",
            "category": "partition",
            "params": ["pin"],
            "utcTime": 133809078112525780
        }, {
            "id": "zoneArm",
            "title": null,
            "localizationId": null,
            "category": "zone",
            "params": ["pin"],
            "utcTime": 133809078112525780
        }, {
            "id": "zoneDisarm",
            "title": null,
            "localizationId": null,
            "category": "zone",
            "params": ["pin"],
            "utcTime": 133809078112525780
        }, {
            "id": "zoneBypass",
            "title": null,
            "localizationId": "#ZoneActionBypass",
            "category": "zone",
            "params": ["pin"],
            "utcTime": 133809078112525780
        }, {
            "id": "zoneUnbypass",
            "title": null,
            "localizationId": "#ZoneActionUnbypass",
            "category": "zone",
            "params": ["pin"],
            "utcTime": 133809078112525780
        }, {
            "id": "outputActivate",
            "title": null,
            "localizationId": "#OutputActionActivate",
            "category": "output",
            "params": ["pin"],
            "utcTime": 133809078112525780
        }, {
            "id": "outputDeactivate",
            "title": null,
            "localizationId": "#OutputActionDeactivate",
            "category": "output",
            "params": ["pin"],
            "utcTime": 133809078112525780
        }, {
            "id": "systemAction1",
            "title": null,
            "localizationId": null,
            "category": "system",
            "params": null,
            "utcTime": 133809078112525780
        }, {
            "id": "systemAction2",
            "title": null,
            "localizationId": null,
            "category": "system",
            "params": null,
            "utcTime": 133809078112525780
        }, {
            "id": "systemAction3",
            "title": null,
            "localizationId": null,
            "category": "system",
            "params": ["pin"],
            "utcTime": 133809078112525780
        }
    ]
}

```

#### Action Object Properties

- `id` (string) - The unique identifier of the action.
- `title` (string) - The title of the action.
- `localizationId` (string) - Optional. The localization identifier of the action. See the [Supported Localization IDs](#supported-localization-ids) section for more details.
- `category` (string) - The category of the action.
- `params` (array of strings or null) - The parameters required for the action. If the action does not require any parameters, this property should be `null`.
- `utcTime` (integer) - Optional. The UTC time (FileTime format) when the action was last updated.

[//]: <> (----------------------------- PROVIDE ALL ACTIONS }}} ------------------------------)

***

[//]: <> (----------------------------- PROVIDE SPECIFIC ACTION {{{ ------------------------------)

## Retrieve or Trigger a Specific Action

#### Endpoint

`GET {actionsUrl}/{action_id}{?action}`

#### Description

This endpoint provides information about a specific action. The response contains an action object representing the action in the access control system. The action object includes details such as the description, category, and unique identifier. 

Optionally, you can trigger the action during the GET request by appending the `action=trigger` query parameter. If supported by the access control system, sending `action=trigger` will execute the specified action immediately. This feature is required for actions that need to be executed and they don't belong to any particular item (e.g., system actions).

#### URL Parameters

- `action_id` (string): Required. The unique identifier of the action.
- `action` (string): Optional. If set to `trigger`, the access control system will attempt to execute the action immediately.

#### Sample Requests

```plaintext
/v1/actions/systemAction1 - Retrieve the 'systemAction1' action information
/v1/actions/systemAction1?action=trigger - Trigger the 'systemAction1' action
```

#### Sample Response

```json
{
    "page": null,
    "nextPage": null,
    "error": null,
    "errorMessage": null,
    "data": {
        "id": "doorLock",
        "title": "Lock",
        "localizationId": "#DoorActionLock",
        "category": "door",
        "params": null,
        "utcTime": 133809078112455403
    }
}

```

#### Action Object Properties

- `id` (string) - The unique identifier of the action.
- `title` (string) - The title of the action.
- `localizationId` (string) - Optional. The localization identifier of the action. See the [Supported Localization IDs](#supported-localization-ids) section for more details.
- `category` (string) - The category of the action.
- `params` (array of strings or null) - The parameters required for the action. If the action does not require any parameters, this property should be `null`.
- `utcTime` (integer) - Optional. The UTC time (FileTime format) when the action was last updated.

[//]: <> (----------------------------- PROVIDE SPECIFIC ACTION }}} ---------------------------)

***

[//]: <> (----------------------------- PROVIDE ALL PARAMETERS {{{ ------------------------------)

## All Parameters

#### Endpoint


`GET {parametersUrl}{?page}`
#### Description

This endpoint provides the information about all available parameters. The response contains an array of parameter objects, each representing a parameter in the access control system. Each parameter object contains information about the parameter, including the description, category, and unique identifier.

#### URL Parameters

- `page` (number): Optional. The page number to retrieve. If the number of parameters exceeds the page size, the response will include the `nextPage` property with the next page number. The default page size is 10.

#### Sample Requests

```plaintext
/v1/parameters
/v1/parameters?page=0
```

#### Sample Response

```json
{
    "page": 0,
    "nextPage": false,
    "error": null,
    "errorMessage": null,
    "data": [{
            "id": "pin",
            "title": "PIN",
            "description": "PIN for command",
            "required": true,
            "type": "password",
            "utcTime": 133809078112355417
        }, {
            "id": "card",
            "title": "Card",
            "description": "Card for command",
            "required": true,
            "type": "text",
            "utcTime": 133809078112355417
        }
    ]
}

```

#### Parameter Object Properties

- `id` (string) - The unique identifier of the parameter.
- `title` (string) - The title of the parameter.
- `description` (string) - The description of the parameter.
- `required` (boolean) - Indicates whether the parameter is required.
- `type` (string) - The type of the parameter.
- `utcTime` (integer) - Optional. The UTC time (FileTime format) when the parameter was last updated.

[//]: <> (----------------------------- PROVIDE ALL PARAMETERS }}} ------------------------------)

***

[//]: <> (----------------------------- PROVIDE SPECIFIC PARAMETER {{{ ------------------------------)

## Retrieve Specific Parameter

#### Endpoint

`GET {parametersUrl}/{parameter_id}`
#### Description

This endpoint provides information about a specific parameter. The response contains a parameter object representing the parameter in the access control system. The parameter object contains information about the parameter, including the description, category, and unique identifier.

#### URL Parameters

- `parameter_id` (string): Required. The unique identifier of the parameter.

#### Sample Requests

```plaintext
/v1/parameters/pin
```

#### Sample Response

```json
{
    "page": null,
    "nextPage": null,
    "error": null,
    "errorMessage": null,
    "data": {
        "id": "pin",
        "title": "PIN",
        "description": "PIN for command",
        "required": true,
        "type": "password",
        "utcTime": 133809078112355417
    }
}

```

#### Parameter Object Properties

- `id` (string) - The unique identifier of the parameter.
- `title` (string) - The title of the parameter.
- `description` (string) - The description of the parameter.
- `required` (boolean) - Indicates whether the parameter is required.
- `type` (string) - The type of the parameter.
- `utcTime` (integer) - Optional. The UTC time (FileTime format) when the parameter was last updated.

[//]: <> (----------------------------- PROVIDE SPECIFIC PARAMETER }}} ---------------------------)

***

[//]: <> (----------------------------- PROVIDE ALL ITEMS {{{ ------------------------------)

## All Items

#### Endpoint

`GET {itemsUrl}{?page}`

#### Description

This endpoint provides the information about all available items. The response contains an array of item objects, each representing an item in the access control system. Each item object contains information about the item, including the title, category, states, and unique identifier.

#### URL Parameters

- `page` (number): Optional. The page number to retrieve. If the number of items exceeds the page size, the response will include the `nextPage` property with the next page number. The default page size is 10.

#### Sample Requests

```plaintext
/v1/items
/v1/items?page=0
```

#### Sample response

```json
{
  "page": 0,
  "nextPage": true,
  "error": null,
  "errorMessage": null,
  "data": [
    {
      "id": "door-1",
      "title": "Door #1",
      "category": "door",
      "states": {
        "doorLocked": true,
        "doorLockedDown": null,
        "doorUnlocked": false,
        "doorClosed": true,
        "doorOpen": false,
        "doorForcedOpen": null,
        "doorOpenTooLong": null
      },
      "utcTime": 133809078112635774
    },
    {
      "id": "partition-1",
      "title": "Partition #1",
      "category": "partition",
      "states": {
        "partitionArmed": true,
        "partitionDisarmed": false,
        "partitionInAlarm": null,
        "partitionReady": true
      },
      "utcTime": 133809078112705824
    },
    {
      "id": "zone-401",
      "title": "Zone #401",
      "category": "zone",
      "states": {
        "zoneArmed": false,
        "zoneDisarmed": true,
        "zoneReady": true,
        "zoneTrouble": null,
        "zoneAlarmed": null,
        "zoneBypassed": null,
        "zoneUnbypassed": true
      },
      "utcTime": 133809078112705824
    },
    {
      "id": "output-1",
      "title": "Output #1",
      "category": "output",
      "states": {
        "outputActive": true,
        "outputInactive": false
      },
      "utcTime": 133809078112715838
    },
    {
      "id": "output-2",
      "title": "Output #2",
      "category": "output",
      "states": {
        "outputActive": false,
        "outputInactive": true
      },
      "utcTime": 133809078112725839
    },
    {
      "id": "partition-2",
      "title": "Partition #2",
      "category": "partition",
      "states": {
        "partitionArmed": false,
        "partitionDisarmed": true,
        "partitionInAlarm": null,
        "partitionReady": false
      },
      "utcTime": 133809078112735845
    },
    {
      "id": "zone-402",
      "title": "Zone #402",
      "category": "zone",
      "states": {
        "zoneArmed": true,
        "zoneDisarmed": false,
        "zoneReady": false,
        "zoneTrouble": null,
        "zoneAlarmed": true,
        "zoneBypassed": null,
        "zoneUnbypassed": false
      },
      "utcTime": 133809078112745850
    }
  ]
}
```

#### Item Object Properties

- `id` (string) - The unique identifier of the item.
- `title` (string) - The title of the item.
- `category` (string) - The category of the item.
- `states` (object) - The states of the item. The states object contains key-value pairs representing the state name and its value.
- `utcTime` (integer) - Optional. The UTC time (FileTime format) when the item information was last updated.

[//]: <> (----------------------------- PROVIDE ALL ITEMS }}} ------------------------------)

***

[//]: <> (----------------------------- PROVIDE SPECIFIC ITEM {{{ ------------------------------)

## Retrieve or Trigger a Specific Item Action

#### Endpoint

`GET {itemsUrl}/{item_id}{?action}`

#### Description

This endpoint provides information about a specific item. The response contains an item object representing the item in the access control system. The item object contains information about the item, including the title, category, states, and unique identifier.

#### URL Parameters

- `item_id` (string): Required. The unique identifier of the item.
- `action` (string): Optional. If the item supports actions, you can trigger an action by appending the `action` query parameter with the action identifier. The access control system will attempt to execute the specified action immediately.

#### Sample Requests

```plaintext
/v1/items/door-1 - Retrieve the information about the 'door-1' item
/v1/items/door-1?action=doorUnlock - Trigger the 'doorUnlock' action for the 'door-1' item
```

#### Sample Response

```json
{
  "page": null,
  "nextPage": null,
  "error": null,
  "errorMessage": null,
  "data": {
    "id": "door-1",
    "title": "Door #1",
    "category": "door",
    "states": {
      "doorLocked": true,
      "doorLockedDown": null,
      "doorUnlocked": false,
      "doorClosed": true,
      "doorOpen": false,
      "doorForcedOpen": null,
      "doorOpenTooLong": null
    },
    "utcTime": 133809078112635774
  }
}
```

#### Item Object Properties

- `id` (string) - The unique identifier of the item.
- `title` (string) - The title of the item.
- `category` (string) - The category of the item.
- `states` (object) - The states of the item. The states object contains key-value pairs representing the state name and its value.
- `utcTime` (integer) - Optional. The UTC time (FileTime format) when the item information was last updated.

[//]: <> (----------------------------- PROVIDE SPECIFIC ITEM }}} ------------------------------)

***

[//]: <> (----------------------------- PROVIDE ALL STATES {{{ ------------------------------)

## All States

#### Endpoint

`GET {statesUrl}{?page}`

#### Description

This endpoint provides the information about all available states. The response contains an array of state objects, each representing a state in the access control system. Each state object contains information about the state, including the title, category, and unique identifier.

#### URL Parameters

- `page` (number): Optional. The page number to retrieve. If the number of states exceeds the page size, the response will include the `nextPage` property with the next page number. The default page size is 10.

#### Sample Requests

```plaintext
/v1/states
/v1/states?page=0
```


#### Sample Response

```json
{
    "page": 0,
    "nextPage": false,
    "error": null,
    "errorMessage": null,
    "data": [{
            "id": "doorLocked",
            "title": "Locked",
            "localizationId": "#DoorStatusLocked",
            "category": "door",
            "utcTime": 133812501871273473
        }, {
            "id": "doorLockedDown",
            "title": "Locked Down",
            "localizationId": null,
            "category": "door",
            "utcTime": 133812501871273473
        }, {
            "id": "doorUnlocked",
            "title": "Unlocked",
            "localizationId": "#DoorStatusUnlocked",
            "category": "door",
            "utcTime": 133812501871273473
        }, {
            "id": "doorClosed",
            "title": "Closed",
            "localizationId": "#DoorStatusClosed",
            "category": "door",
            "utcTime": 133812501871273473
        }, {
            "id": "doorOpen",
            "title": "Open",
            "localizationId": "#DoorStatusOpen",
            "category": "door",
            "utcTime": 133812501871273473
        }, {
            "id": "doorForcedOpen",
            "title": "Forced Open",
            "localizationId": "#DoorStatusForcedOpen",
            "category": "door",
            "utcTime": 133812501871273473
        }, {
            "id": "doorOpenTooLong",
            "title": "Open Too Long",
            "localizationId": "#DoorStatusHeldOpen",
            "category": "door",
            "utcTime": 133812501871273473
        }, {
            "id": "partitionArmed",
            "title": "Armed",
            "localizationId": "#StatusArmed",
            "category": "partition",
            "utcTime": 133812501871273473
        }, {
            "id": "partitionDisarmed",
            "title": "Disarmed",
            "localizationId": "",
            "category": "partition",
            "utcTime": 133812501871273473
        }, {
            "id": "partitionReady",
            "title": "Ready",
            "localizationId": "#StatusReady",
            "category": "partition",
            "utcTime": 133812501871273473
        }, {
            "id": "partitionNotReady",
            "title": "Not Ready",
            "localizationId": "#StatusNotReady",
            "category": "partition",
            "utcTime": 133812501871273473
        }, {
            "id": "partitionTrouble",
            "title": "Trouble",
            "localizationId": "#StatusTrouble",
            "category": "partition",
            "utcTime": 133812501871273473
        }, {
            "id": "partitionAlarmed",
            "title": "Alarmed",
            "localizationId": "#StatusAlarmed",
            "category": "partition",
            "utcTime": 133812501871273473
        }, {
            "id": "zoneArmed",
            "title": "Armed",
            "localizationId": "#StatusArmed",
            "category": "zone",
            "utcTime": 133812501871273473
        }, {
            "id": "zoneDisarmed",
            "title": "Disarmed",
            "localizationId": null,
            "category": "zone",
            "utcTime": 133812501871273473
        }, {
            "id": "zoneReady",
            "title": "Ready",
            "localizationId": "#StatusReady",
            "category": "zone",
            "utcTime": 133812501871273473
        }, {
            "id": "zoneTrouble",
            "title": "Trouble",
            "localizationId": "#StatusTrouble",
            "category": "zone",
            "utcTime": 133812501871273473
        }, {
            "id": "zoneAlarmed",
            "title": "Alarm",
            "localizationId": "#StatusAlarmed",
            "category": "zone",
            "utcTime": 133812501871273473
        }, {
            "id": "zoneBypassed",
            "title": "Bypassed",
            "localizationId": "#ZoneStatusBypassed",
            "category": "zone",
            "utcTime": 133812501871273473
        }, {
            "id": "zoneUnbypassed",
            "title": "Unbypassed",
            "localizationId": null,
            "category": "zone",
            "utcTime": 133812501871273473
        }, {
            "id": "outputActivated",
            "title": "Activated",
            "localizationId": "#StatusActivated",
            "category": "output",
            "utcTime": 133812501871273473
        }, {
            "id": "outputDeactivated",
            "title": "Deactivated",
            "localizationId": "#StatusDeactivated",
            "category": "output",
            "utcTime": 133812501871273473
        }
    ]
}
```

#### State Object Properties

- `id` (string) - The unique identifier of the state.
- `title` (string) - The title of the state.
- `localizationId` (string or null) - Optional. The localization identifier of the state. See the [Supported Localization IDs](#supported-localization-ids) section for more details.
- `category` (string) - The category of the state.
- `utcTime` (integer) - Optional. The UTC time (FileTime format) when the state was last updated.

[//]: <> (----------------------------- PROVIDE ALL STATES }}} ------------------------------)

***

[//]: <> (----------------------------- PROVIDE SPECIFIC STATE {{{ ------------------------------)

## Retrieve Specific State

#### Endpoint

`GET {statesUrl}/{state_id}`

#### Description

This endpoint provides information about a specific state. The response contains a state object representing the state in the access control system. The state object contains information about the state, including the title, category, and unique identifier.

#### URL Parameters

- `state_id` (string): Required. The unique identifier of the state.

#### Sample Requests

```plaintext
/v1/states/doorLocked
```

#### Sample Response

```json
{
    "page": null,
    "nextPage": null,
    "error": null,
    "errorMessage": null,
    "data": {
        "id": "doorLocked",
        "title": "Locked",
        "localizationId": "#DoorStatusLocked",
        "category": "door",
        "utcTime": 133812501871273473
    }
}
```

#### State Object Properties

- `id` (string) - The unique identifier of the state.
- `title` (string) - The title of the state.
- `localizationId` (string or null) - Optional. The localization identifier of the state. See the [Supported Localization IDs](#supported-localization-ids) section for more details.
- `category` (string) - The category of the state.
- `utcTime` (integer) - Optional. The UTC time (FileTime format) when the state was last updated.

[//]: <> (----------------------------- PROVIDE SPECIFIC STATE }}} ---------------------------)

***

[//]: <> (----------------------------- SUBSCRIBE TO NOTIFICATIONS {{{ ------------------------------)
## Subscribe to Notifications (Server-Sent Events)

> **Note:** You can find out more about Server-Sent Events (SSE) technology in the [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events).

#### Endpoint
`GET {notificationsUrl}{?keepAlive}`

#### Description

Access control system must provide a Server-Sent Events (SSE) endpoint to allow the VMS to subscribe to notifications. The SSE endpoint should send notifications about updated items and access control events. The notifications are sent as a stream of messages in the SSE format.

#### URL Parameters
- `keepAlive` (integer): Optional. The interval in seconds to send keep-alive messages to the server. If the server does not receive a keep-alive message within the specified interval, the connection will be closed. If the parameter is omitted, the server should use the default keep-alive interval of 20 seconds.

#### Sample requests
```plaintext
/v1/notifications
/v1/notifications?keepAlive=5
```

#### Sample Response (SSE) stream of notifications

```plaintext
event:notify
data:{"updatedItems":[{"current":{"id":"door-1","title":"Door #1","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177708937533}},{"current":{"id":"door-2","title":"Door #2","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177709007543}},{"current":{"id":"door-3","title":"Door #3","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177709007543}},{"current":{"id":"door-4","title":"Door  #400","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177709027514}}],"utcTime":133808871512993765}

event:notify
data:{"utcTime":133808871713260587}

event:notify
data:{"utcTime":133808871913403805}

event:notify
data:{"updatedItems":[{"new":{"id":"door-1602","title":"Door #1602","category":"door","states":{"doorLocked":null,"doorLockedDown":null,"doorUnlocked":null,"doorClosed":null,"doorOpen":null,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808896895532498}}],"utcTime":133808896895542491}

event:notify
data:{"utcTime":133808897095623309}

event:notify
data:{"updatedItems":[{"old":{"id":"door-1","title":"Door #1","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177708937533}}],"utcTime":133808928944597695}

event:notify
data:{"utcTime":133808930545201883}

event:notify
data:{"events":[{"id":"772de4ac-f3ad-4481-b02b-d5937c727018","eventDescriptionId":"20001","utcTime":133808930545721933,"objectId":"partition-847","objectType":"partition","actorId":"user-00002","actorType":"user","eventText":"Sample description of partition event #1"}],"utcTime":133808930545731937}

event:notify
data:{"events":[{"id":"6f8ac731-ebc7-4dc9-bd39-4614649a5e8f","eventDescriptionId":"20003","utcTime":133808930643736628,"objectId":"partition-1120","objectType":"partition","actorId":"user-00002","actorType":"user","eventText":"Sample description of partition event #3"}],"utcTime":133808930643746635}


```

#### Notification Types

Each notification message is represented by an `event:notify` pre-fix and contains a `data` object with the notification data. The notification data object contains the following properties depending on the event type.

##### 1. Keep-alive notification

Keep-alive messages are sent periodically to maintain the connection and solely contain the `utcTime` property representing the current UTC time in FileTime format.

*Example keep-alive message:*

```plaintext
event:notify
data:{"utcTime":133808930545201883}
```

##### 2. Updated Items notification

Updated items notifications include an `updatedItems` array, which contains multiple item status objects. Each item status object represents an item in the access control system and contains the current, new, or old status of the item.

- **`current`**

  The `current` item data represents the current status of the item and must be sent only once on the initial notifications connection.

  >Notice: When sending the `current` item data, the `new`, and `old` item data must be omitted. The `current` item data can't be sent after the `new` or `old` item data has been sent.

- **`old`**

  The `old` item data represents the previous status of the item before the update or deletion. When item is deleted, only the `old` item data is sent. When item is updated, both `old` and `new` item data are sent.

- **`new`**

  The `new` item data represents newly added items or updated items. When item is updated, both `old` and `new` item data are sent. When item is added, only the `new` item data is sent.

###### Item Status Object Properties

Each item status object (`current`, `new`, `old`) contains the following properties:

- `id` (string) - The unique identifier of the item.
- `title` (string) - The title of the item.
- `category` (string) - The category of the item.
- `states` (object) - The states of the item. The object contains key-value pairs where the key is the state name and the value is the state value.
- `utcTime` (integer) - The UTC time (FileTime format) when the item data was last updated.

###### Examples of Updated Items Notification Messages

*The initial `current` status object of the items:*

```plaintext
event:notify
data:{"updatedItems":[{"current":{"id":"door-1","title":"Door #1","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177708937533}},{"current":{"id":"door-2","title":"Door #2","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177709007543}},{"current":{"id":"door-3","title":"Door #3","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177709007543}},{"current":{"id":"door-4","title":"Door  #400","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177709027514}}],"utcTime":133808871512993765}
```


*The `old` only status object of the deleted item:*

```plaintext
event:notify
data:{"updatedItems":[{"old":{"id":"door-1","title":"Door #1","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177708937533}}],"utcTime":133808928944597695}
```


*The `new` only status object of the added item:*

```plaintext
event:notify
data:{"updatedItems":[{"new":{"id":"door-1602","title":"Door #1602","category":"door","states":{"doorLocked":null,"doorLockedDown":null,"doorUnlocked":null,"doorClosed":null,"doorOpen":null,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808896895532498}}],"utcTime":133808896895542491}
```


*The `old` and `new` item status object for the updated item:*

```plaintext
event:notify
data:{"updatedItems":[{"old":{"id":"door-1","title":"Door #1","category":"door","states":{"doorLocked":true,"doorLockedDown":null,"doorUnlocked":false,"doorClosed":true,"doorOpen":false,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808177708937533}}, {"new":{"id":"door-1","title":"Door #1","category":"door","states":{"doorLocked":false,"doorLockedDown":null,"doorUnlocked":true,"doorClosed":false,"doorOpen":true,"doorForcedOpen":null,"doorOpenTooLong":null},"utcTime":133808930545721933}}],"utcTime":133808930545731937} 
```

##### 3. Access Control Events notification

Access control events notifications include an `events` array, which contains multiple event objects. 
Each event object represents an access control event that has occurred in the system.

###### Example Access Control Events Notification Message

```plaintext
event:notify
data:{"events":[{"id":"9d485488-8f56-405a-9e3e-c117bd517a34","eventDescriptionId":"30002","utcTime":133809065376342151,"objectId":"zone-530","objectType":"zone","actorId":"user-00001","actorType":"user","eventText":"Sample description of zone event #2"}],"utcTime":133809065376342151}
```

###### Access Control Event Notification Object Properties

Each event object contains the following properties:

- `id` (string) - The unique identifier of the event.
- `eventDescriptionId` (string) - The identifier of the event description.
- `utcTime` (integer) - The UTC time (FileTime format) when the event occurred.
- `objectId` (string) - The unique identifier of the object related to the event.
- `objectType` (string) - The type of the object related to the event.
- `actorId` (string) - The unique identifier of the actor related to the event.
- `actorType` (string) - The type of the actor related to the event.
- `eventText` (string) - The description of the event.


[//]: <> (----------------------------- SUBSCRIBE TO NOTIFICATIONS }}} ------------------------------)

***

[//]: <> (----------------------------- LOCALIZATION IDs {{{ ------------------------------)

## Supported Localization IDs

The localization IDs are unique strings that map access control system resources (e.g., actions, states) to their localized names in the VMS interface. Additionally, certain localization IDs signify critical states and influence how resources appear based on priority, security risk, or other key factors in the VMS. Below is a list of supported localization IDs.

### Door Action IDs:
```plaintext
#DoorActionLock
#DoorActionUnlock
#DoorActionTempUnlock
#DoorActionLockdown
```

### Partition Action IDs:
```plaintext
#PartitionActionArm
#PartitionActionDisarm
#PartitionActionStay
```

### Zone Action IDs:
```plaintext
#ZoneActionBypass
#ZoneActionUnbypass
#ZoneActionToggleBypass
```

### Output Action IDs:
```plaintext
#OutputActionActivate
#OutputActionDeactivate
```

### State IDs:
```plaintext
#StatusArmed
#StatusTrouble
#StatusAlarmed
#StatusReady
#StatusNotReady
#StatusActivated
#StatusDeactivated
```

### Door State IDs:
```plaintext
#DoorStatusOpen
#DoorStatusClosed
#DoorStatusLocked
#DoorStatusUnlocked
#DoorStatusForcedOpen
#DoorStatusHeldOpen
```

### Zone State IDs:
```plaintext
#ZoneStatusBypassed
```

### Partition State IDs:
```plaintext
#PartitionStatusFire
#PartitionStatusMedic
#PartitionStatusPanic
#PartitionStatusStay
```

[//]: <> (----------------------------- LOCALIZATION IDs }}} ------------------------------)

***

**Copyright © 2004-2025 A&H Software House, Inc.**
