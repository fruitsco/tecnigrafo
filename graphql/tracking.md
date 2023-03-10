# Tracking

The following sections describe the schema for tracking events sent to the server by our clients. When forwarding these events to a tracking data pipeline, the server attaches the metadata defined in [_server metadata_](#server-metadata) to the payload.

## General

### Server Metadata

On top of the metadata sent by the client, the following metadata is attached to every outgoing event by the server.

| Metadata    | Type      | Description                                                                                               |
| ----------- | --------- | --------------------------------------------------------------------------------------------------------- |
| `sessionId` | `string`  | Represents a single session of a user. Usually expires after closing the browser.                         |
| `trackId`   | `string`  | Identifies a user best-effort. If it's a signed in user, it's the `userId`. If not, it's the `sessionId`. |
| `userId`    | `string?` | Represents a logged-in user.                                                                              |

### Global Metadata

This global metadata is sent with every event.

| Metadata    | Type     | Description                                    |
| ----------- | -------- | ---------------------------------------------- |
| `url`       | `string` | The full URL of the page the event occurred on |
| `timestamp` | `Date`   | The timestamp when the event occurred.         |

## Types

### `TrackEvent`

| Name                                                                       |
| -------------------------------------------------------------------------- |
| [`PAGEVIEW`](#page-view)                                                   |
| [`COCKPIT_CREATE_LAUNCH`](#create-flow-started)                            |
| [`COCKPIT_CREATE_STEP`](#create-flow-step)                                 |
| [`COCKPIT_CREATE_SUCCESS`](#create-flow-success)                           |
| [`COCKPIT_CREATE_ENRICH_INFORMATION`](#create-flow-seller-data-enrichment) |
| [`COCKPIT_CREATE_DOWNLOAD`](#create-flow-file-download)                    |
| [`COCKPIT_BUY_LAUNCH`](#buyer-flow-started)                                |
| [`COCKPIT_BUY_STEP`](#buyer-flow-step)                                     |
| [`COCKPIT_BUY_PAYMENT`](#buyer-flow-payment)                               |
| [`COCKPIT_BUY_PURCHASE`](#buyer-flow-purchase)                             |
| [`COCKPIT_BUY_DOWNLOAD`](#buyer-flow-file-download)                        |

### `CockpitFlow`

| Name      |
| --------- |
| `FILE`    |
| `EVENT`   |
| `PROJECT` |

## Generic Events

### Page View

> ???? `PAGEVIEW`

| Metadata   | Type     | Description                      |
| ---------- | -------- | -------------------------------- |
| `category` | `string` | The category of the page viewed. |
| `name`     | `string` | The name of the page viewed.     |

## Cockpit Events

This metadata is sent with all cockpit events.

| Metadata | Type                          | Description                                       |
| -------- | ----------------------------- | ------------------------------------------------- |
| `flow`   | [`CockpitFlow`](#cockpitflow) | The flow in which the event occurred.             |
| `step`   | `string`                      | The step identifier the event is associated with. |

## Seller Cockpit Events

| Metadata           | Type     | Description                    |
| ------------------ | -------- | ------------------------------ |
| `cockpitSessionId` | `string` | The ID of the cockpit session. |

### Create flow started

> ???? `COCKPIT_CREATE_LAUNCH`

This event is triggered when a creator visits the cockpit.

### Create flow step

> ???? `COCKPIT_CREATE_STEP`

This event is triggered when a creator advances in the cockpit.

### Create flow success

> ???? `COCKPIT_CREATE_SUCCESS`

This event is triggered when a creator successfully created a product.

| Metadata      | Type     | Description              |
| ------------- | -------- | ------------------------ |
| `productSlug` | `string` | The slug of the product. |

### Create flow seller data enrichment

> ???? `COCKPIT_CREATE_ENRICH_INFORMATION`

This event is triggered when a creator successfully enriches their seller account with personal information.

### Create flow file download

> `???? COCKPIT_CREATE_DOWNLOAD`

This event is triggered when a seller downloads his own product files.

| Metadata      | Type     | Description              |
| ------------- | -------- | ------------------------ |
| `productSlug` | `string` | The slug of the product. |

## Buyer Cockpit Events

This event metadata is sent with all buyer cockpit events.

| Metadata      | Type     | Description              |
| ------------- | -------- | ------------------------ |
| `productSlug` | `string` | The slug of the product. |

### Buyer flow started

> `???? COCKPIT_BUY_LAUNCH`

This event is triggered when a buyer visits the cockpit.

### Buyer flow step

> `???? COCKPIT_BUY_STEP`

This event is triggered when a buyer advances in the cockpit.

### Buyer flow payment

> ???? `COCKPIT_BUY_PAYMENT`

This event is triggered when a buyer initiates the payment flow.

### Buyer flow purchase

> ???? `COCKPIT_BUY_PURCHASE`

This event is triggered when a buyer successfully purchased a product.

### Buyer flow file download

> ???? `COCKPIT_BUY_DOWNLOAD`

This event is triggered when a buyer downloads his own product files.
