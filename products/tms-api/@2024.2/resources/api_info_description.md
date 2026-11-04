# About the CargoSoft API

## General

> Attention: Since our API is still under development, endpoints may well change, or object structures may change. Of course, we try to avoid this as much as possible, but we cannot guarantee it. So that you have the possibility to react in time, we will soon offer you a possibility to subscribe to a newsletter. We would then inform all subscribers when something will change in the API. We are currently still looking for an integrable solution for newsletters, which can be used to subscribe and unsubscribe at this place. Until then, we would like to ask you to send us an email to support@cargosoft.de with the request that we inform you when there are planned changes to the API.

## Authorization

<p id='CHAPTER_AUTHORIZATION'></p>
The following chapter will explain the authorization prozess to access the CargoSoft API endpoints. The use case figure below shows a common example how to retrieve the JWT and how to use it.

![Authorization][SeqAuthorization]

[SeqAuthorization]: https://raw.githubusercontent.com/cargosoft-gmbh/cargosoft-api-docs/2024.2/resources/Seq_authentifizierung.svg "Use case Authorization"

### Retrieve a token

First of all each client has to request a bearer Java Web Toke (JWT) from the CargoSoft API. To receive a JWT, username
and password are required. Username and password can only be created by CargoSoft.
For more details [see](#operation/retrieveAuthToken).

> Please note: The JWT is valid for 24 hours. After that you have to request a new one.

### Access API endpoints

To access the CargoSoft API endpoint, a JWT is required. The JWT has to be sent in the HTTP authorization header.

**Example**: This example shows a minimal curl with a bearer authorization header. The token is not valid because we
only want to illustrate the structure.

```
curl --location --request POST 'https://api-demo.cargosoft.de/cargosoft/shipment/lookup' \
--header 'Authorization: Bearer eyJ0eXAiOiJqd3QiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJ1cGRhdGUiLCJjYXJnb3NvZnQtand0LNjc2NDQ1NTAzfQ.mK82rjSu0ltd0bf3mvXUqwpME05ck5OEuqnzsAQ8WPFPo' \
--data-raw '[]'"
```

> Attention: Your user may be restricted. The CargoSoft API uses a permission system to grant access to the
> CargoSoft API endpoint. If your user is restricted or it's not allowed to use an endpoint you need please contact our
> support.

### Access webhook

The webhook uses basic auth as its authorization type.

**Example**: This example shows a minimal curl request, to access the webhook. The basic auth is not valid.

```
curl --location --request POST 'https://api-demo.cargosoft.de/external/webhook' \
--header 'Authorization: Basic cG9ydg53xcvgawawb3J0YmFzZWNj' \
--data-raw '{}'"
```

> Please note: Your user needs a special role to access the webhook endpoint via basic auth.

## CRUD

The CargoSoft API uses CRUD operations. We attempt to implement each CRUD operation for each functional
group of endpoints if it makes sense. The delete operation is one of the most sensitive function and will not
available to each function group.

### Lookup

The CargoSoft API extends the CRUD retrieve operations with lookup operations. Each functional group of endpoints should
have a lookup method implemented.
Lookups should be used to search functional objects with given criteria named identifiers. The identifiers may be
different to each functional group. To find out which functional identifier are possible, please take a look at the
corresponding functional group.

The identifier will be sent in the request body to the API lookup endpoint. An identifier contains the following
three information:

1. _match_mode_: The match mode can alter the behaviour, how the criteria is compared against the object in the database
   are **[EQUALS, CONTAINS, IN, OR, GT, LT, GE, LE]**.
2. _key_: The key of the criteria for the functional object to lookup. To find out which keys are possible, please check
   the
   corresponding implementation.
3. _value_: The value of the criteria.

**Example**: Lookup request body to search a shipment

```json
[
  {
    "match_mode": "EQUALS",
    "key": "MBL",
    "value": "MASTER_12345"
  },
  {
    "match_mode": "EQUALS",
    "key": "MODE_OF_TRANSPORT",
    "value": "SEA"
  }
]
```

> Please note: When using one of any lookup methods, please define search criteria as precisely as possible to limit the
> result of objects.

The lookup may contain ordering by one or more criteria. The ordering will passed as a query parameter.

The result set of a lookup includes zero up to n functional objects depending on the search criteria.
Each functional object contains an ID. The ID can be used to retrieve additional information by passing the id as a path
parameter of the retrieve endpoint.

**Example**: Lookup a shipment and retrieve all shipment events

1. Looking up a shipment with given criteria, for instance with the BL number. For more details
   please [see](#operation/lookup).
2. Extract the ID of the shipment result object.
3. Call the [Events](#operation/{id}/events) endpoint with the shipment ID as path parameter.

### Pagination

Most endpoints of the CargoSoft API use pagination mechanics.

The pagination information will be sent as a request query parameter to the API endpoint. The pagination contains the
following two query parameter:
1. _page_no_: The current number of the page
2. _page_size_: The size of the page

**Example**: Lookup a shipment with pagination query parameter
```
curl --location --request POST 'https://api-demo.cargosoft.de/cargosoft/shipment/lookup?page_no=1&page_size=5' \
--header 'Authorization: Bearer eyJ0eXAiOiJqd3QiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJ1cGRhdGUiLCJjYXJnb3NvZnQtand0LNjc2NDQ1NTAzfQ.mK82rjSu0ltd0bf3mvXUqwpME05ck5OEuqnzsAQ8WPFPo' \
--header 'Content-Type: application/json' \
--data '[
    {
        "match_mode": "EQUALS",
        "key": "MBL",   
        "value": "MASTER_12345"
    },
   {
        "match_mode": "EQUALS",
        "key": "MODE_OF_TRANSPORT",
        "value": "SEA"
    }
]'
```

The response contains two additional headers:
1. _X-Page-Count_: The total number of pages.
2. _X-Row-Count_: The total number of found objects.

With these both headers it is possible to scroll through a result set.

## Testing

For testing purposes CargoSoft hosted a server with the last released CargoSoft API.

**URL**: https://api-demo.cargosoft.de

To use the test environment, please use the "Try it" console in this documentation. Login credentials are needed to use
the test API. The credentials have to be requested from the CargoSoft support. The received information grants access to
a
test account which should be able to use all endpoints. If any endpoint is restricted, please contact CargoSoft. Please
have in mind that the test environment will be reset every night!

# Use cases

## Retrieve a shipment

To retrieve a shipment, the [lookup](#operation/lookup) endpoint of the shipment functional group is one of
the common possibilities that should be used. The following figure shows the use case to look up a shipment. To keep the
figure simple, the authorization process is referred.

![RetrieveShipment][SeqRetrieveShipment]

[SeqRetrieveShipment]: https://raw.githubusercontent.com/cargosoft-gmbh/cargosoft-api-docs/2024.2/resources/Seq_retrieve_shipment.svg "Use case to retrieve a shipment"

1. To access the lookup endpoint a JWT is needed. For more details how to retrieve a JWT [see](#CHAPTER_AUTHORIZATION).
2. When a JWT is present, the shipment lookup endpoint can be called. Shipment identifier have to be sent in the request
   body to reduce the response result. For a complete list of shipment identifier
   see [here](#operation/lookup).

## Retrieve shipment status

CargoSoft has different pools in which status information are stored. These are described in detail in the next
subsections. However, in order to understand the differences
exactly, the following graphic tries to clarify how the status message flow works.

![ShipmentStatus][useCaseShipmentStatus]

[useCaseShipmentStatus]: https://raw.githubusercontent.com/cargosoft-gmbh/cargosoft-api-docs/2024.2/resources/Use_case_shipment_status.svg "Use case to retrieve shipment events"

### Shipment provider status

These status messages come from external status providers and service providers who supply CargoSoft with status
information.
They cannot be entered or changed by CargoSoft employees by hand. A shipment provider status always represents exactly
one state - planned / estimated or the actual date and time (depending on the name of the status). The codes of the
statuses are defined by the providers and are variable depending on the provider.

> Please note: There is no endpoint to retrieve provider status from the CargoSoft API yet. This endpoint will be
> implemented in a later release.

### Shipment status

CargoSoft customers can define this status code themselves. In addition, the shipment provider statuses can be mapped
into a shipment status if the CargoSoft customer configured his basic data accordingly. Here, for example, it can be
defined that the status
ABC of provider XYZ has to be processed as shipment status ETA. In this case ETA would be a freely defined code for the
status. Therefore, there is no list of valid codes.

With this status, there are also two forms of date information per data record. Once a planned and an actual one. This
means that a status ETA, although obviously planned, can contain an actual date. However, this is due to the recording
of the customer's data.

The CargoSoft customer can add this status information manually, but an existing record can no longer be changed.

> Please note: There is no endpoint to retrieve CargoSoft status from the CargoSoft API yet. This endpoint will be
> implemented in a later release.

### Shipment events

Shipment events are status events that are defined by CargoSoft. These events are fixed and can always be called up -
unless there is no data available for the shipment. For a complete list of shipment event codes
see [here](#operation/retrieveEvents).
A shipment event can be created automatically from a shipment or provider status if this is set in the CargoSoft master
data. A status always represents exactly one instance - planned or actual and the information can be changed at any time
by hand, but also by background processes.

![RetrieveShipmentEvents][SeqRetrieveShipmentEvents]

[SeqRetrieveShipmentEvents]: https://raw.githubusercontent.com/cargosoft-gmbh/cargosoft-api-docs/2024.2/resources/Seq_retrieve_shipment_event.svg "Use case to retrieve shipment events"