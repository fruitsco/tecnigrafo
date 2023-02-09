# Schema

## Queries

### `getProducts`

Get a list of products for a given account name. If no account name is given, the currently logged-in user's own products are returned.

**Input**

| Name          | Type      |
| ------------- | --------- |
| `accountSlug` | `string?` |

**Response**

A list of products.

<code>[<a href="#product">Product</a>]</code>

### `getProduct`

Get a product with the given slug. If the user is not authenticated, certain features are restricted.

**Input**

| Name   | Type     |
| ------ | -------- |
| `slug` | `string` |

**Response**

The product, if it was found.

<code><a href="#product">Product</a></code>

### `getProductFileDownload`

Get a download link to either a single file or to a zip file which contains the files for the given product slug.
When `resourceId` is specified, the download link points to the given resource. If it's left out, the download link will point to the zip file.
The endpoint returns a download link only if the current user either purchased the item or is the owner and fails otherwise.

**Input**

| Name         | Type      |
| ------------ | --------- |
| `slug`       | `string`  |
| `resourceId` | `string?` |

**Response**

| Name          | Type     |
| ------------- | -------- |
| `downloadUrl` | `string` |

### `getBookings`

Get bookings for a given product slug if the current user is the owner of the product. If the user purchased the product, all bookings for the product are returned. Incase no slug is provided, a producer gets all bookings for his products and a user gets all bookings for his account.

> If the product type is not `event`, an error is thrown.

**Input**

| Name   | Type      |
| ------ | --------- |
| `slug` | `string?` |

**Response**

A list of bookings for the given product.

<code>[<a href="#booking">Booking</a>]</code>

### `getBookableDays`

Get days on which a given product is available for booking.

> If the product type is not `event`, an error is thrown.

**Input**

| Name   | Type     |
| ------ | -------- |
| `slug` | `string` |

**Response**

<code>[DateTime]</code>

### `getBookableSlots`

Get available time slots for a given product on the given day.
If calendars are linked those events are used to filter. If no calendars are linked, the product's availability is used.

> If the product type is not `event`, an error is thrown.

**Input**

| Name   | Type       |
| ------ | ---------- |
| `slug` | `string`   |
| `date` | `DateTime` |

**Response**

A list of bookable time slots.

<code>[<a href="#timeslot">TimeSlot</a>]</code>

## Mutations

### `initCockpitSession`

Initialize a fresh cockpit session.

**Response**

| Name            | Type  | Description                                         |
| --------------- | ----- | --------------------------------------------------- |
| `maxUploadSize` | `int` | The total max upload size in bytes for this session |

_Set-Cookie_

| Name              | Type     |
| ----------------- | -------- |
| `cockpit_session` | `string` |

### `initFileUpload`

Initialize a generic file upload.

**Input**

| Name       | Type                          |
| ---------- | ----------------------------- |
| `fileName` | `string`                      |
| `context`  | [`FileContext`](#filecontext) |

_Auth_

| Type     | Name              |
| -------- | ----------------- |
| `cookie` | `cockpit_session` |

**Response**

| Name                  | Type     |
| --------------------- | -------- |
| `fields`              | `object` |
| `fields.resourceId`   | `string` |
| `headers`             | `object` |
| `url`                 | `string` |
| `method`              | `string` |
| `remainingUploadSize` | `number` |

### `createProduct`

Create a product with a certain type. For now, it can be either a `file`, an `event` or a `bundle`.

**Input**

| Name          | Type                                                      |
| ------------- | --------------------------------------------------------- |
| `name`        | `string`                                                  |
| `description` | `string?`                                                 |
| `price`       | `int`                                                     |
| `type`        | [`ProductType`](#producttype)                             |
| `fileData`    | <code>[<a href="#resourceinput">ResourceInput<a>]</code>? |
| `eventData`   | <code><a href="#eventinput">EventInput</a>?</code>        |
| `bundleData`  | `[slugs]?`                                                |

> If `type` is _) `event`, `eventData` must be set, _) `file`, `fileData` must be set and \*) `bundle`, `bundleData` must be set.

_Auth_

| Type     | Name              |
| -------- | ----------------- |
| `cookie` | `session`         |
| `cookie` | `cockpit_session` |

**Response**

The created product.

<code><a href="#product">Product</a></code>

### `updateProduct`

Update a product with a given slug.

**Input**

| Name             | Type                                                     |
| ---------------- | -------------------------------------------------------- |
| `slug`           | `string`                                                 |
| `name`           | `string?`                                                |
| `description`    | `string?`                                                |
| `price`          | `int?`                                                   |
| `enabled`        | `boolean?`                                               |
| `bannerResource` | <code><a href="#resourceinput">ResourceInput</a>?</code> |
| `imageResource`  | <code><a href="#resourceinput">ResourceInput</a>?</code> |
| `eventData`      | <code><a href="#eventinput">EventInput</a>?</code>       |
| `bundleData`     | `[slug]?`                                                |

> `eventData` may be set only if `type` is `event`. Same for `bundle` and `bundleData`.

_Auth_

| Type     | Name      |
| -------- | --------- |
| `cookie` | `session` |

**Response**

The updated product.

<code><a href="#product">Product</a></code>

### `deleteProduct`

Delete a product with a given slug.

**Input**

| Name   | Type     |
| ------ | -------- |
| `slug` | `string` |

_Auth_

| Type     | Name      |
| -------- | --------- |
| `cookie` | `session` |

### `createEventSchedule`

Create a new event schedule which will be associated with the current account.

> If no user is logged in, this mutation will throw an error.

**Input**

The input to create a new event schedule.

[`EventScheduleInput`](#eventscheduleinput)

_Auth_

| Type     | Name      |
| -------- | --------- |
| `cookie` | `session` |

**Response**

The created event schedule.

[`EventSchedule`](#eventschedule)

### `createBooking`

Create a new booking for a given product.

> If the product type is not `event`, an error is thrown.

**Input**

| Name                | Type                      |
| ------------------- | ------------------------- |
| `slug`              | `string`                  |
| `parentProductSlug` | `string?`                 |
| `time`              | [`TimeRange`](#timerange) |

_Auth_

| Type     | Name      |
| -------- | --------- |
| `cookie` | `session` |

**Response**

The created booking.

[`Booking`](#booking)

### `cancelBooking`

Cancel a given booking with the possibility to specify an optional reason.

**Input**

| Name     | Type                                                                 |
| -------- | -------------------------------------------------------------------- |
| `id`     | `string`                                                             |
| `reason` | <code><a href="#bookingcancelreason">BookingCancelReason</a>?</code> |

_Auth_

| Type     | Name      |
| -------- | --------- |
| `cookie` | `session` |

### `getCalendarAccounts`

Get a list of all linked calendar accounts for the currently logged in account.

**Input**

| Name       | Type                                    |
| ---------- | --------------------------------------- |
| `provider` | [`CalendarProvider`](#calendarprovider) |
| `type`     | [`CalendarType`](#calendarprovider)     |

_Auth_

| Type     | Name      |
| -------- | --------- |
| `cookie` | `session` |

**Response**

A list of calendar accounts.

<code>[<a href="#calendaraccount">CalendarAccount</a>]</code>

### `linkCalendarAccount`

Link a new calendar account to the currently logged in account.

**Input**

| Name       | Type                                    |
| ---------- | --------------------------------------- |
| `provider` | [`CalendarProvider`](#calendarprovider) |

_Auth_

| Type     | Name      |
| -------- | --------- |
| `cookie` | `session` |

**Response**

A url which can be used to connect to a calendar account.

| Name  | Type     |
| ----- | -------- |
| `url` | `string` |

### `deleteCalendarAccount`

Unlink and delete an existing calendar connection from the currently logged in account.

**Input**

| Name | Type     |
| ---- | -------- |
| `id` | `string` |

_Auth_

| Type     | Name      |
| -------- | --------- |
| `cookie` | `session` |

### `updateCalendar`

Update an existing calendar of the currently logged in account.

**Input**

| Name               | Type      |
| ------------------ | --------- |
| `id`               | `string`  |
| `detectCollission` | `boolean` |
| `update_events`    | `boolean` |

### `setDefaultCalendar`

Change the default calendar for the currently logged in account.

**Input**

| Name | Type     |
| ---- | -------- |
| `id` | `string` |

_Auth_

| Type     | Name      |
| -------- | --------- |
| `cookie` | `session` |

## Inputs

### `ResourceInput`

| Name         | Type      |
| ------------ | --------- |
| `name`       | `string?` |
| `resourceId` | `string`  |

### `EventInput`

| Name                 | Type                                                               |
| -------------------- | ------------------------------------------------------------------ |
| `type`               | [`EventType`](#eventtype)                                          |
| `bookingStop`        | `int`                                                              |
| `bufferTimeAfter`    | `int`                                                              |
| `bufferTimeBefore`   | `int`                                                              |
| `dateRangeFrom`      | `DateTime`                                                         |
| `dateRangeTo`        | `DateTime`                                                         |
| `duration`           | `int`                                                              |
| `location`           | [`EventLocationInput`](#eventlocationinput)                        |
| `schedule`           | <code><a href="#eventscheduleinput">EventScheduleInput</a>?</code> |
| `scheduleId`         | `string?`                                                          |
| `showRemainingSlots` | `boolean`                                                          |
| `slotSizeMax`        | `int`                                                              |
| `timeSlots`          | `int`                                                              |

> Either `schedule` or `scheduleId` can be set, but not both.

### `EventLocationInput`

| Name   | Type                                      |
| ------ | ----------------------------------------- |
| `type` | [`EventLocationType`](#eventlocationtype) |
| `meta` | `[string : string]`                       |

### `EventScheduleInput`

| Name       | Type                                                |
| ---------- | --------------------------------------------------- |
| `name`     | `string`                                            |
| `schedule` | <code>[[<a href="#timerange">TimeRange</a>]]</code> |

## Models

### `Product`

| Name             | Type                                                  |
| ---------------- | ----------------------------------------------------- |
| `slug`           | `string`                                              |
| `type`           | [`ProductType`](#producttype)                         |
| `name`           | `string`                                              |
| `description`    | `string?`                                             |
| `price`          | `int?`                                                |
| `owner`          | `boolean`                                             |
| `purchased`      | `boolean`                                             |
| `bannerResource` | <code><a href="#resource">Resource<a>?</code>         |
| `imageResource`  | <code><a href="#resource">Resource<a>?</code>         |
| `fileData`       | <code>[<a href="#resource">Resource<a>]?</code>       |
| `eventData`      | <code><a href="#productevent">ProductEvent<a>?</code> |
| `bundleData`     | <code>[<a href="#product">Product<a>]?</code>         |

> `owner` is `true`, if the currently logged-in user is the owner of the product, `false` otherwise.

> `purchased` is `true`, if the currently logged-in user is not the owner of the product and purchased it, `false` otherwise.

> `fileData` and `eventData` is only sent if the current user is the owner.

> If `type` is `bundle`, the product is a bundle and its child products can be queried from `bundleData`.

### `ProductEvent`

| Name                 | Type                              |
| -------------------- | --------------------------------- |
| `type`               | [`EventType`](#eventtype)         |
| `bookingStop`        | `int`                             |
| `bufferTimeAfter`    | `int`                             |
| `bufferTimeBefore`   | `int`                             |
| `dateRangeFrom`      | `DateTime`                        |
| `dateRangeTo`        | `DateTime`                        |
| `duration`           | `int`                             |
| `location`           | [`EventLocation`](#eventlocation) |
| `schedule`           | [`EventSchedule`](#eventschedule) |
| `showRemainingSlots` | `boolean`                         |
| `slotSizeMax`        | `int`                             |
| `timeSlots`          | `int`                             |

### `EventLocation`

| Name   | Type                                      |
| ------ | ----------------------------------------- |
| `type` | [`EventLocationType`](#eventlocationtype) |
| `meta` | `[string : string]`                       |

### `EventSchedule`

| Name       | Type                                                |
| ---------- | --------------------------------------------------- |
| `id`       | `string`                                            |
| `name`     | `string`                                            |
| `schedule` | <code>[[<a href="#timerange">TimeRange</a>]]</code> |

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

| Name   | Type     |
| ------ | -------- |
| `id`   | `string` |
| `name` | `string` |
| `size` | `int`    |
| `mime` | `string` |

### `TimeSlot`

Represents a single bookable time slot.

| Name             | Type                      |
| ---------------- | ------------------------- |
| `availableSlots` | `int?`                    |
| `range`          | [`TimeRange`](#timerange) |

### `TimeRange`

| Name       | Type       |
| ---------- | ---------- |
| `timeFrom` | `DateTime` |
| `timeTo`   | `DateTime` |

### `Booking`

| Name        | Type                              |
| ----------- | --------------------------------- |
| `id`        | `string`                          |
| `cancelled` | `boolean`                         |
| `duration`  | `int`                             |
| `startTime` | `DateTime`                        |
| `endTime`   | `DateTime`                        |
| `user`      | [`User`](#user)                   |
| `location`  | [`EventLocation`](#eventlocation) |
| `createdAt` | `DateTime`                        |

### `CalendarAccount`

| Name          | Type                                            |
| ------------- | ----------------------------------------------- |
| `id`          | `string`                                        |
| `name`        | `string`                                        |
| `accountName` | `string`                                        |
| `provider`    | [`CalendarProvider`](#calendarprovider)         |
| `providerId`  | `string`                                        |
| `calendars`   | <code>[<a href="#calendar">Calendar</a>]</code> |

### `Calendar`

| Name              | Type      |
| ----------------- | --------- |
| `id`              | `string`  |
| `providerId`      | `string`  |
| `name`            | `string`  |
| `checkCollisions` | `boolean` |
| `updateEvents`    | `boolean` |

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

### `BookingCancelReason`

| Name          |
| ------------- |
| `REBOOKING`   |
| `UNAVAILABLE` |

### `CalendarProvider`

| Name           |
| -------------- |
| `GOOGLE`       |
| `APPLE_ICLOUD` |

### `CalendarType`

| Name     |
| -------- |
| `REMOTE` |
| `LINKED` |
