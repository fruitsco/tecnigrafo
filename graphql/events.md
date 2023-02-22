# Events

## Global

This global metadata is defined on every event that is sent to the tracking backend

| Metadata    | Type      | Description                                                                                               |
| ----------- | --------- | --------------------------------------------------------------------------------------------------------- |
| `sessionId` | `string`  | Represents a single session of a user. Usually expires after closing the browser.                         |
| `trackId`   | `string`  | Identifies a user best-effort. If it's a signed in user, it's the `userId`. If not, it's the `sessionId`. |
| `userId`    | `string?` | Represents a logged-in user.                                                                              |
| `url`       | `string`  | The full URL of the page the event occurred on                                                            |
| `path`      | `string`  | The path of the page the event occurred on                                                                |
| `timestamp` | `Date`    | The timestamp when the event occurred.                                                                    |

## Enums

### `FlowType`

| Name      |
| --------- |
| `FILE`    |
| `EVENT`   |
| `PROJECT` |

### `EventType`

| Name                                |
| ----------------------------------- |
| `PAGEVIEW`                          |
| `COCKPIT_CREATE_LAUNCH`             |
| `COCKPIT_CREATE_STEP`               |
| `COCKPIT_CREATE_SUCCESS`            |
| `COCKPIT_CREATE_ENRICH_INFORMATION` |
| `COCKPIT_CREATE_DOWNLOAD`           |
| `COCKPIT_BUY_LAUNCH`                |
| `COCKPIT_BUY_STEP`                  |
| `COCKPIT_BUY_PAYMENT`               |
| `COCKPIT_BUY_PURCHASE`              |
| `COCKPIT_BUY_DOWNLOAD`              |

## Generic

### Page View

`PAGEVIEW`

## Cockpit

This is the metadata which is sent with all cockpit events.

| Metadata | Type                    | Description                                       |
| -------- | ----------------------- | ------------------------------------------------- |
| `flow`   | [`FlowType`](#flowtype) | The flow in which the event occurred.             |
| `step`   | `string`                | The step identifier the event is associated with. |

## Seller Cockpit

| Metadata           | Type     | Description                    |
| ------------------ | -------- | ------------------------------ |
| `cockpitSessinoId` | `string` | The ID of the cockpit session. |

### Create flow started

`COCKPIT_CREATE_LAUNCH`

This event is triggered when a creator visits the cockpit.

### Create flow step

`COCKPIT_CREATE_STEP`

This event is triggered when a creator advances in the cockpit.

### Create flow success

`COCKPIT_CREATE_SUCCESS`

This event is triggered when a creator successfully created a product.

| Metadata      | Type     | Description              |
| ------------- | -------- | ------------------------ |
| `productSlug` | `string` | The slug of the product. |

### Create flow seller data enrichment

`COCKPIT_CREATE_ENRICH_INFORMATION`

This event is triggered when a creator successfully enriches their seller account with personal information.

### Create flow file download

`COCKPIT_CREATE_DOWNLOAD`

This event is triggered when a seller downloads his own product files.

| Metadata      | Type     | Description              |
| ------------- | -------- | ------------------------ |
| `productSlug` | `string` | The slug of the product. |

## Buyer Cockpit

| Metadata      | Type     | Description              |
| ------------- | -------- | ------------------------ |
| `productSlug` | `string` | The slug of the product. |

### Buyer flow started

`COCKPIT_BUY_LAUNCH`

This event is triggered when a buyer visits the cockpit.

### Buyer flow step

`COCKPIT_BUY_STEP`

This event is triggered when a buyer advances in the cockpit.

### Buyer flow payment

`COCKPIT_BUY_PAYMENT`

This event is triggered when a buyer initiates the payment flow.

### Buyer flow purchase

`COCKPIT_BUY_PURCHASE`

This event is triggered when a buyer successfully purchased a product.

### Buyer flow file download

`COCKPIT_BUY_DOWNLOAD`

This event is triggered when a buyer downloads his own product files.
