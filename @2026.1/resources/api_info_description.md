# About the CargoSoft API

## General

> Attention: Since our API is still under development, endpoints may well change, or object structures may change. Of course, we try to avoid this as much as possible, but we cannot guarantee it. So that you have the possibility to react in time, we will soon offer you a possibility to subscribe to a newsletter. We would then inform all subscribers when something will change in the API. We are currently still looking for an integrable solution for newsletters, which can be used to subscribe and unsubscribe at this place. Until then, we would like to ask you to send us an email to support@cargosoft.de with the request that we inform you when there are planned changes to the API.

## Changelog
This page documents all business-relevant changes to our API.

| Category       | Endpoint / Area            | Change                                                                                                 | Impact and required action                                       | Version |
|----------------|----------------------------|--------------------------------------------------------------------------------------------------------|------------------------------------------------------------------|---|
| **Added**      | `POST` /cargosoft/shipment | Added new shipment create endpoint.                                                                    | No changes to existing integrations are required.                | 2026.1 |
| **Added**      | Use cases                  | Added new use case to create a shipment - [see](#section/use-cases/create-a-shipment).                  | No changes to existing integrations are required.                | 2026.1 |
| **Added**      | Use cases                  | Added a use case for attaching an E-File document to a shipment - [see](#section/use-cases/attach-an-e-file-document-to-a-shipment). | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`POST /cargosoft/efile`](#operation/createEFileDocument) | Create an E-File document together with references and roles. | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`GET /cargosoft/efile/{id}`](#operation/retrieveEFileDocument) | Retrieve an E-File document by its ID. | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`DELETE /cargosoft/efile/{id}`](#operation/deleteEFileDocument) | Delete an E-File document by its ID. | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`POST /cargosoft/efile/lookup`](#operation/lookupEFileDocuments) | Look up E-File documents by E-File identifiers. | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`POST /cargosoft/efile/{id}/reference`](#operation/addEFileReference) | Add a reference to an E-File document. | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`GET /cargosoft/efile/{id}/reference`](#operation/retrieveEFileReferences) | Retrieve the references of an E-File document. | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`DELETE /cargosoft/efile/{id}/reference`](#operation/removeEFileReference) | Remove a reference from an E-File document. | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`POST /cargosoft/efile/{id}/role`](#operation/addEFileRole) | Add an authorization role to an E-File document. | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`GET /cargosoft/efile/{id}/role`](#operation/retrieveEFileRoles) | Retrieve the authorization roles of an E-File document. | No changes to existing integrations are required. | 2026.1 |
| **Added**      | [`DELETE /cargosoft/efile/{id}/role`](#operation/removeEFileRole) | Remove an authorization role from an E-File document. | No changes to existing integrations are required. | 2026.1 |
| **Fixed**      | [`POST /cargosoft/efile/lookup`](#operation/lookupEFileDocuments) | Corrected the documented lookup keys to the E-File identifiers `REFERENCE`, `SHIPMENT_ID`, and `CLIENT`. | Runtime behavior is unchanged. | 2026.1 |
| **Added**      | Basic data                 | Added new edi code conversion endpoints.                                                               | No changes to existing integrations are required.                | 2026.1 |
| **Deprecated** | ShipmentEventQualifier     | The shipment event qualifer `TRANSSHIPMENT` is now deprecated and will be removed in a future version. | Use the `TRANSSHIPMENT_ARRIVAL` shipment event qualifer instead. | 2026.1 |



### Category definitions

| Category | Meaning |
|---|---|
| **Breaking change** | A non-backward-compatible change that may require integration updates |
| **Added** | A new feature, endpoint, field, or supported value |
| **Changed** | A backward-compatible change to existing behavior |
| **Deprecated** | A feature is still available but will be removed in the future |
| **Removed** | A feature, endpoint, or field has been removed |
| **Fixed** | A bug fix without an intended breaking interface change |

## Authorization

<p id='CHAPTER_AUTHORIZATION'></p>
The following chapter will explain the authorization prozess to access the CargoSoft API endpoints. The use case figure below shows a common example how to retrieve the JWT and how to use it.

![Authorization][SeqAuthorization]

[SeqAuthorization]: ./resources/Seq_authentifizierung.svg "Use case Authorization!"

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

[SeqRetrieveShipment]: ./resources/Seq_retrieve_shipment.svg "Use case to retrieve a shipment"

1. To access the lookup endpoint a JWT is needed. For more details how to retrieve a JWT [see](#CHAPTER_AUTHORIZATION).
2. When a JWT is present, the shipment lookup endpoint can be called. Shipment identifier have to be sent in the request
   body to reduce the response result. For a complete list of shipment identifier
   see [here](#operation/lookup).

## Retrieve shipment status

CargoSoft has different pools in which status information are stored. These are described in detail in the next
subsections. However, in order to understand the differences
exactly, the following graphic tries to clarify how the status message flow works.

![ShipmentStatus][useCaseShipmentStatus]

[useCaseShipmentStatus]: ./resources/Use_case_shipment_status.svg "Use case to retrieve shipment events"

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

[SeqRetrieveShipmentEvents]: ./resources/Seq_retrieve_shipment_event.svg "Use case to retrieve shipment events"

## Create a shipment

<p id='CHAPTER_SHIPMENT'></p>

To create a new shipment, the [create](#operation/createShipment) endpoint of the shipment functional group has to be
used. The following figure shows the use case to create a shipment. To keep the figure simple, the authorization process is referred.

![CreateShipment][SeqCreateShipment]

[SeqCreateShipment]: ./resources/Seq_create_shipment.svg "Use case to create a shipment"

1. To access the create endpoint a JWT is needed. For more details how to retrieve a JWT [see](#CHAPTER_AUTHORIZATION).
2. Based on the authenticated API user, the CargoSoft API determines the EDI partner to associate with the new
   shipment: the EDI partner's matchcode has to be identical to the API username. If no EDI partner exists for
   the API user, the request is rejected and no shipment is created.
3. The determined EDI partner's settings are used, where configured, to fill in values that are otherwise
   required to generate the shipment number - see [EDI-Partner](#CHAPTER_EDIPARTNER_SETTINGS)
   below for the exact keys and their fallback rules.
4. The shipment is created together with any nested data supplied in the same request body - goods, containers,
   addon field values, order memo lines, and so on. A memo text longer than the maximum line length is
   automatically wrapped across several consecutive memo lines - see the [lookup](#operation/lookup) endpoint
   of the shipment memo functional group to retrieve them independently afterwards. If any of that nested data
   fails validation (for example an addon field value referencing a matchcode that doesn't exist in the master
   data), the whole shipment creation is rejected and nothing is created.
5. Once the shipment has been created, the shipment is returned as response to the request. 
   The shipment response contains the unique shipment ID and the CargoSoft shipment number. 


### EDI-Partner settings

<p id='CHAPTER_EDIPARTNER_SETTINGS'></p>
Generating a shipment number requires three pieces of information: the shipment number definition (posdef),
the main department, and the sub department. Each of them can be configured individually as a setting on the
EDI partner determined for the request (see step 2 above). A setting configured on the EDI partner always
takes precedence; an empty or blank setting value is treated the same as if the setting wasn't configured at
all. Each of the three keys is resolved independently of the other two - a missing/invalid value for one key
never prevents the fallback for a different key from being used. Any other, unrelated qualifier configured on
the EDI partner is ignored.

| Key                            | Meaning                                                       | Fallback if not configured (or blank) on the EDI partner                                                                                                  |
|----------------------------------|---------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| `SHIPMENT_NUMBER_DEFINITION_VERSION`    | The shipment number definition version to use. | The `shipment_number_definition_version` supplied in the request body, if present; otherwise the CargoSoft client's own default. |
| `MAINDEPARTMENT`                | The main department to use for shipment numbering.            | The `maindepartment` supplied in the request body. If neither is present, the request is rejected.                                                        |
| `SUBDEPARTMENT`                 | The sub department to use for shipment numbering.             | The `subdepartment` supplied in the request body. If neither is present, the request is rejected.                                                         |
| `ADDRESS_FORMAT`                 | The address format used to format the shipment addresses.     | The CargoSoft client's own default in the general company parameters.                                                                                     |

> Please note: if a request doesn't need to rely on the EDI partner's settings at all - for example while
> testing - `shipment_number_definition_version`, `maindepartment` and `subdepartment` can simply be supplied
> directly in the request body instead.

## Attach an E-File document to a shipment

<p id='CHAPTER_EFILE_SHIPMENT'></p>

An E-File document can be created and linked to an existing shipment in one request. The shipment must first be
resolved through `POST /cargosoft/shipment/lookup`, because the E-File shipment reference is built
from the shipment number and client returned by that lookup. The document is then uploaded with the
[E-File create endpoint](#operation/createEFileDocument).

![AttachEFileToShipment][SeqAttachEFileToShipment]

[SeqAttachEFileToShipment]: ./resources/Seq_attach_efile_to_shipment.svg "Use case to attach an E-File document to a shipment"

### Prerequisites

- The API user must be allowed to call `POST /cargosoft/shipment/lookup` and `POST /cargosoft/efile`. Adding a
  reference to an already existing document also requires access to `POST /cargosoft/efile/{id}/reference`.
- E-File must be enabled and configured for the shipment's CargoSoft client.
- The document content must be Base64 encoded without a data-URI prefix.
- The document category must exist in the customer's CargoSoft basic data. It is mandatory when the configured
  E-File system uses D3 mode.

### Workflow

1. Retrieve a JWT with the [authorization endpoint](#operation/retrieveAuthToken). Send the token as
   `Authorization: Bearer <token>` with every subsequent request.
2. Look up the shipment with criteria that identify exactly one shipment. A CargoSoft shipment number is unique
   only in combination with its client, so both identifiers should be supplied:

```json
[
  {
    "match_mode": "EQUALS",
    "key": "CLIENT",
    "value": "001"
  },
  {
    "match_mode": "EQUALS",
    "key": "SHIPMENT_NUMBER",
    "value": "022120300004"
  }
]
```

3. Process the lookup response before uploading. Continue only when the expected shipment was returned, and read
   its `id`, `shipment_number`, and `client`. The numeric shipment `id` can later be used to
   [look up E-File documents](#operation/lookupEFileDocuments), but it is not the value of the `CSFILE` reference.
4. Build the shipment reference and include it in the upload request:

| Situation | Endpoint | `reference_type` | `reference_value` |
|---|---|---|---|
| Create and link a new document | `POST /cargosoft/efile` | `CSFILE` | `<shipment_number>#<client>` |
| Link an existing document later | `POST /cargosoft/efile/{id}/reference` | `CSFILE` | `<shipment_number>#<client>` |

For the lookup response above, the reference value is `022120300004#001`.

5. Upload the document. The following example omits optional roles; when roles are supplied, use values configured
   for the customer's E-File system. `document_category` is also customer-specific.

```json
{
  "client": "001",
  "document_category": 1601,
  "document_name": "shipping-instructions.pdf",
  "description": "Shipping instructions received from the customer",
  "document_date": "2026-11-02T10:15:00",
  "bytes": "JVBERi0xLjQKJcTl8uXr...",
  "references": [
    {
      "reference_type": "CSFILE",
      "reference_value": "022120300004#001"
    }
  ]
}
```

6. A successful response contains the stored E-File document and its `id`. Keep this ID when references or roles
   need to be managed later.

### Error handling and retries

- A shipment lookup that returns no result or more than the expected shipment must be resolved before the upload.
  Refine the lookup identifiers instead of choosing an arbitrary result.
- `401` or `403` indicates missing or insufficient API credentials. Do not retry until the credentials or endpoint
  permissions have been corrected.
- Invalid Base64 data, an unknown client, a missing mandatory document category, or an unavailable E-File system
  causes the upload to fail. The error response must be handled before another upload is attempted.
- The server-side connection from the CargoSoft API to the configured E-File system has a default timeout of
  30 seconds; the deployment can override this value. API consumers should configure their HTTP timeout with enough
  headroom for that downstream call.
- The CargoSoft API does not automatically retry a failed upload. `POST /cargosoft/efile` is not idempotent, so a
  blind retry after a client or downstream timeout can create a duplicate. First call the
  [E-File lookup](#operation/lookupEFileDocuments) with the shipment `id` as `SHIPMENT_ID` and verify whether the
  document was stored. Retry only when it is absent. If a `503` response contains `Retry-After`, wait for the stated
  interval before retrying.

## EDI code conversion

An EDI code conversion translates an EDI partner's own code (for example for a charge type or packaging code)
into the corresponding CargoSoft internal code, or vice versa, depending on direction. They are looked up with
the [lookup](#operation/lookup) endpoint of the code conversion functional group, using a combination of the
`EDIPARTNER`, `DIRECTION`, `CODETYPE` and `SOURCECODE` identifiers.

Two identifier combinations are served from a cache instead of querying the database on every request, since
this master data changes very rarely:

- `EDIPARTNER` + `DIRECTION` + `CODETYPE`: returns every code conversion configured for that EDI partner's
  table/direction.
- `EDIPARTNER` + `DIRECTION` + `CODETYPE` + `SOURCECODE`: returns the one specific conversion for that source
  code.

Omitting `EDIPARTNER` from a `DIRECTION` + `CODETYPE` + `SOURCECODE` lookup returns every matching conversion
across all EDI partners that happen to share the same source code - also served from the cache. Any other
identifier combination is looked up directly against the database.

> Please note: the `DIRECTION` identifier's value must be the raw single-character code as stored in the
> CargoSoft TMS (`E` or `S`), not the `direction` value (`RECEIVE`/`SEND`) shown on the response object.
