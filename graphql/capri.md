# Schema

## Queries

### `getProducts`

Get a list of products for a given account name. If no account name is given, the currently logged-in user's own products are returned.

#### Input

| Name          | Type      |
| ------------- | --------- |
| `accountName` | `string?` |

#### Output

`[`[`Product`](#product)`]`


### `getProduct`

Get a product with the given slug.

#### Input

| Name   | Type     |
| ------ | -------- |
| `slug` | `string` |

#### Output

See [`Product`](#product)


### `getProductFiles`

Get a list of files for a given product slug if the current user either purchased the item or is the owner.

#### Input

| Name   | Type     |
| ------ | -------- |
| `slug` | `string` |

#### Output

| Name    | Type                            |
| ------- | ------------------------------- |
| `files` | [`SafeResource`](#saferesource) |

### `getBookings`

Get bookings for a given product slug if the current user purchased the item. As a user can purchase and book a event multiple times, a list of bookings is returned.

> If the product type is not `event`, an error is thrown.

#### Input

| Name   | Type     |
| ------ | -------- |
| `slug` | `string` |

#### Output

List of bookings

`[`[`Booking`](#booking)`]`

### `getBookableDays`

Get days on which a given product is available for booking.

> If the product type is not `event`, an error is thrown.

#### Input

| Name   | Type     |
| ------ | -------- |
| `slug` | `string` |

#### Output

| Name    | Type         |
| ------- | ------------ |
| `dates` | `[DateTime]` |

### `getBookableSlots`

Get available time slots for a given product on the given day.

> If the product type is not `event`, an error is thrown.

#### Input

| Name   | Type       |
| ------ | ---------- |
| `slug` | `string`   |
| `date` | `DateTime` |

#### Output

| Name    | Type                            |
| ------- | ------------------------------- |
| `date`  | `DateTime`                      |
| `slots` | `[`[`TimeRange`](#timerange)`]` |

## Mutations

### `initFileUpload`

Initialize a generic file upload.

#### Input

| Name       | Type                          |
| ---------- | ----------------------------- |
| `fileName` | `string`                      |
| `context`  | [`FileContext`](#filecontext) |

#### Output

| Name         | Type     |
| ------------ | -------- |
| `resourceId` | `string` |
| `uploadUrl`  | `string` |


### `createProduct`

Create a product with a certain type. For now, it can be either a `file`, an `event` or a `bundle`.

#### Input

| Name          | Type                                         |
| ------------- | -------------------------------------------- |
| `name`        | `string`                                     |
| `description` | `string?`                                    |
| `price`       | `int`                                        |
| `type`        | [`ProductType`](#producttype)                |
| `fileData`    | `[`[`ResourcInput`](#resourceinput)`]?`      |
| `eventData`   | [`ProductEventInput`](#producteventinput)`?` |
| `bundleData`  | `[string]?`                                  |

> If `type` is *) `event`, `eventData` must be set, *) `file`, `fileData` must be set and *) `bundle`, `bundleData` must be set.

#### Output

See [`Product`](#product)

### `updateProduct`

Update a product with a given slug.

#### Input

| Name             | Type                                         |
| ---------------- | -------------------------------------------- |
| `slug`           | `string`                                     |
| `name`           | `string?`                                    |
| `description`    | `string?`                                    |
| `price`          | `int?`                                       |
| `enabled`        | `boolean?`                                   |
| `eventData`      | [`ProductEventInput`](#producteventinput)`?` |
| `bundleData`     | `[string]?`                                  |
| `bannerResource` | [`ResourcInput`](#resourceinput)`]           |
| `imageResource`  | [`ResourcInput`](#resourceinput)`]           |

> `eventData` may be set only if `type` is `event`. Same for `bundle` and `bundleData`.

#### Output

See [`Product`](#product)

### `deleteProduct`

Delete a product with a given slug.

#### Input

| Name   | Type     |
| ------ | -------- |
| `slug` | `string` |


## Inputs

### `ResourceInput`

| Name         | Type      |
| ------------ | --------- |
| `name`       | `string?` |
| `resourceId` | `string`  |

### `ProductEventInput`

| Name                 | Type                                                         |
| -------------------- | ------------------------------------------------------------ |
| `type`               | [`EventType`](#eventtype)                                    |
| `bookingStop`        | `int`                                                        |
| `bufferTimeAfter`    | `int`                                                        |
| `bufferTimeBefore`   | `int`                                                        |
| `dateRangeFrom`      | `DateTime`                                                   |
| `dateRangeTo`        | `DateTime`                                                   |
| `duration`           | `int`                                                        |
| `location`           | [`ProductEventLocationInput`](#producteventlocationinput)    |
| `schedule`           | [`ProductEventScheduleInput`](#producteventscheduleinput)`?` |
| `scheduleId`         | `string?`                                                    |
| `showRemainingSlots` | `boolean`                                                    |
| `slotSizeMax`        | `int`                                                        |
| `timeSlots`          | `int`                                                        |

> Either `schedule` or `scheduleId` can be set, but not both.

### `ProductEventLocationInput`

| Name   | Type                                      |
| ------ | ----------------------------------------- |
| `type` | [`EventLocationType`](#eventlocationtype) |
| `meta` | `Dict[string, string]`                    |

### `ProductEventScheduleInput`

| Name       | Type                              |
| ---------- | --------------------------------- |
| `name`     | `string`                          |
| `schedule` | `[[`[`TimeRange`](#timerange)`]]` |

## Models

### `Product`

| Name             | Type                               |
| ---------------- | ---------------------------------- |
| `slug`           | `string`                           |
| `type`           | [`ProductType`](#producttype)      |
| `name`           | `string`                           |
| `description`    | `string?`                          |
| `price`          | `int`                              |
| `owner`          | `boolean`                          |
| `purchased`      | `boolean`                          |
| `bannerResource` | [`Resource`](#resource)`]          |
| `imageResource`  | [`Resource`](#resource)`]          |
| `fileData`       | `[`[`Resource`](#resource)`]?`     |
| `eventData`      | [`ProductEvent`](#productevent)`?` |
| `bundleData`     | `[`[`Product`](#product)`]?`       |

> `owner` is `true`, if the currently logged-in user is the owner of the product, `false` otherwise.

> `purchased` is `true`, if the currently logged-in user is not the owner of the product and purchased it, `false` otherwise.

> `fileData` and `eventData` is only sent if the current user is the owner.

> If `type` is `bundle`, the product is a bundle and its child products can be queried from `bundleData`.

### `ProductEvent`

| Name                 | Type                                            |
| -------------------- | ----------------------------------------------- |
| `type`               | [`EventType`](#eventtype)                       |
| `bookingStop`        | `int`                                           |
| `bufferTimeAfter`    | `int`                                           |
| `bufferTimeBefore`   | `int`                                           |
| `dateRangeFrom`      | `DateTime`                                      |
| `dateRangeTo`        | `DateTime`                                      |
| `duration`           | `int`                                           |
| `location`           | [`ProductEventLocation`](#producteventlocation) |
| `schedule`           | [`ProductEventSchedule`](#producteventschedule) |
| `showRemainingSlots` | `boolean`                                       |
| `slotSizeMax`        | `int`                                           |
| `timeSlots`          | `int`                                           |

### `ProductEventLocation`

| Name   | Type                                      |
| ------ | ----------------------------------------- |
| `type` | [`EventLocationType`](#eventlocationtype) |
| `meta` | `Dict[string, string]`                    |

### `ProductEventSchedule`

| Name       | Type                              |
| ---------- | --------------------------------- |
| `id`       | `string`                          |
| `name`     | `string`                          |
| `schedule` | `[[`[`TimeRange`](#timerange)`]]` |

### `Resource`

Represents a single file resource stored by fruits. Can be either a file available for purchase, or metadata associated with a product.

| Name   | Type     |
| ------ | -------- |
| `id`   | `string` |
| `name` | `string` |
| `size` | `int`    |
| `mime` | `string` |
| `url`  | `string` |

### `SafeResource`

Represents a reference to a file purchased by a consumer. It is safe to return to consumers, as it does not contain sensitive information such as the resource id. The `downloadUrl` is a temporary link to the downloadable file.

| Name          | Type     |
| ------------- | -------- |
| `name`        | `string` |
| `size`        | `int`    |
| `mime`        | `string` |
| `downloadUrl` | `string` |

### `TimeRange`

| Name   | Type       |
| ------ | ---------- |
| `from` | `DateTime` |
| `to`   | `DateTime` |

### `Booking`

| Name        | Type                                                  |
| ----------- | ----------------------------------------------------- |
| `id`        | `string`                                              |
| `cancelled` | `boolean`                                             |
| `duration`  | `int`                                                 |
| `startTime` | `DateTime`                                            |
| `endTime`   | `DateTime`                                            |
| `user`      | `[`[`User`](#user)`]`                                 |
| `createdAt` | `DateTime`                                            |
| `location`  | `[`[`ProductEventLocation`](#producteventlocation)`]` |

## Enums

### `ProductType`

| Name      |
| --------- |
| `FILE`    |
| `EVENT`   |
| `BUNDLE`  |
| `PROJECT` |

### `EventType`

| Name     |
| -------- |
| `GROUP`  |
| `SINGLE` |

### `EventLocationType`

| Name              |
| ----------------- |
| `FRUITS_MEET`     |
| `GOOGLE_MEET`     |
| `IN_PERSON`       |
| `MICROSOFT_TEAMS` |
| `PHONE_CALL`      |
| `WEBEX`           |
| `WHEREBY`         |
| `ZOOM`            |

### `FileContext`

| Name     |
| -------- |
| `UPLOAD` |
| `META`   |