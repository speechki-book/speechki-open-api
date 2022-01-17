# Hermes

Hermes is a service providing integration with Speechki CRM to create orders (books) over REST API.

At the moment we don't have a sandbox, so all the integration tests will be made in production environment.


## Requirements

To work with the API you will need:
- a Customer in Speechki CRM;
- a Customer token to call Hermes REST API;
- an endpoint for WebHook which will be receiving requests from our side.


## Customer Token

Customer's admin and manager can create the token. The token has no lifetime limit. For a token to be revoked it must be destroyed.

Token helps us identify the current Customer and user. All of the actions that will be performed with the token will be written under the user owning the token.

HTTP Header — **Authorization**.

Authorization header format — **Token waGpB26t.JXD7RPEHqJVdTCy2AZl7HM0Garmv9DQB**


## Embedded

You can redirect a user to this URL or embed an iframe with it. Recommended minimum width for the iframe is 1200px.

`<iframe src="guest_link" />`

You can read about [*Guest Link*](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#guest-link) below. 

Additionaly to params that guest link contains, you can also append `lang` parameter to change editor language. `...&lang=eng`.
Current available languages:
* `rus` - Russian
* `eng` - English

Embed Editor also provides methods to interact with the corresponding Speechki CRM order. In the top bar of the window there would be controls for changing order state (if any is available). 

When editing process is over and the order is closed iframe sends `postMessage` to its parent containing event object in following notation: 

`
  { event: 'speechki-close-editor', data: {} }
`

For right now there is only one event which signals order closing. 

## Swagger Doc

Up-to-date documentation for the endpoints is available at https://hermes.books.speechkit.ru/docs.


### Book languages

This endpoint is absolutely open for requests.

It is a dictionary where key is a unique ID for the language and value is a label for this language.

These values are used for filtering voices (speakers) and creating the book (text parsing).

Book languages [endpoint](https://hermes.books.speechkit.ru/docs#/speech_settings.v1/get_book_languages_handler_api_v1_speech_settings_languages__get).


### Speakers

This endpoint requires an Auth Token.

List of voices you can use to create books. Your decision will affect the voice of the book.

This endpoint requires a query parameter [**book_language**](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#book-languages).

Speakers [endpoint](https://hermes.books.speechkit.ru/docs#/speech_settings.v1/get_speakers_handler_api_v1_speech_settings_speakers__get).


### Order


#### Create Order

This endpoint requires an Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/create_order_handler_api_v1_orders_orders__post).


##### Request Body

`url`: required field for the URL address of the text document. Supports **DOCX** and **PDF** formats. It is recommended to upload files without pictures and tables.

`type_productions` supports several options: 

- `client` is used if the client is working on the book independently (without Speechki putsource options).
- `outsource` is used if Speechki editors are working on the book.
- `independently` is used if Customer editors are working on the book.
- If this field is not filled, our system will set a default value for this field.

`details`: required dictionary with settings:

- `comment`: optional text field;

- `remote_key`: optional field for storing IDs from your system;

- `speaker`: required field for speaker ID which can be taken from the Speakers endpoint;

- `speed`: optional field for choosing audio speed. Default value is 1.0;

- `volume`: optional field for choosing audio volume. Default value is 1.0;

- `isbnx`: optional field for the ISBN identifier;

- `book_language`: required field for the language name from Book Languages endpoint;

- `raw_authors`: required field. Object contains `first_name` (required), `last_name` (optional), patronymic (optional field for the middle name).


##### Response

In case of success service returns HTTP Code 202 and a message.


##### Getting the order information

After the order was create in Speechki system, it will send a notification on your WebHook which your will set in our system. Notification contains information about orders, builds, and transitions.


#### Retrieve Order

This endpoint requires an Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/get_order_handler_api_v1_orders_orders__order_id___get).

Returns one order.


##### Response

Response contains information about the order, builds, and available transitions.

If the order contains a `book_id` and `type_productions` is equal to client, then the `guest_token_link` field will contain a link to create a guest link.

Available transitions contain links to make the transition. Every transition performs some actions that change the order.


#### List Order

This endpoint requires an Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/get_orders_handler_api_v1_orders_orders__get).

Returns 20 last orders.


###### Response

Response contains information about the orders.

If the order contains `book_id` and `type_productions` is equal to client, then the `guest_token_link` field will contain a link to create a guest link.


#### Guest Link

Returns link which redirects to the Speechki editor.

This endpoint requires an Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/create_guest_link_handler_api_v1_orders_orders__order_id__link__post).


##### Request Body

`comeback_url`: optional field. This service sets the url to which the user can be returned from the Speechki editor.


#### Change State

Change current state. All of the available state changes for the order will be contained in the endpoint.

This endpoint requires an Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/change_order_state_handler_api_v1_orders_orders__order_id__change_state__state___post).


### WebHook

Notification request use **POST** method.


#### WebHook Schema

```json
{
  "notification_id": 1,
  "event_type": "create",
  "order": {
    "id": "string",
    "details": {
      "name": "string",
      "raw_authors": [
        {
          "first_name": "string",
          "last_name": "",
          "patronymic": ""
        }
      ],
      "comment": "string",
      "remote_key": "string",
      "speaker": 0,
      "speed": 1,
      "volume": 1,
      "isbnx": "string",
      "book_language": "russian",
      "is_completed": true
    },
    "book_id": 0,
    "state": {
      "id": 0,
      "label": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      },
      "slug": "string"
    },
    "created": "2021-07-14T13:32:48.958Z",
    "modified": "2021-07-14T13:32:48.959Z",
    "isbnx": "string",
    "type_productions": "independently"
  },
  "transitions": [
    {
      "id": 1,
      "created": "2021-07-14T13:32:48.958Z",
      "source_state": {
            "id": 5,
            "label": {
                "eng": "New order",
                "rus": "Новая заявка"
            },
            "slug": "new_order"
        },
      "destination_state": {
            "id": 6,
            "label": {
                "eng": "Approved",
                "rus": "Одобрено"
            },
            "slug": "approved"
        }
    }
  ],
  "builds": [
    {
      "bit_rate": 0,
      "duration": 0,
      "modified": "2021-07-14T13:32:48.959Z",
      "link": "string",
      "build_type": "raw"
    }
  ]
}
```

#### Fields

**event_type** variants:

- create
- change_state
- build
- book
- voice
- transfer
- other


**type_productions** variants:

- independently
- outsource
- client


## Case 1 (type of production - "client"):

If you have clients who will edit books themselves.

Main different from other type of productions is that editing the book is possible from outside the tracker (see [*Embedded*](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#embedded))

### Step 1:

These steps need execute before creates orders in the Tracker.

- You need have Customer in Speechki Tracker;
- Set Webhook address in the Customer settings;
- Create API key for your Customer admin or manager. All orders will be created from this user.


### Step 2:

Now you can create orders.

- Take languages list over [endpoint](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#book-languages);
- Now you can take voices list with filter by language over [endpoint](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#speakers);
- After that you can build request body

```json
{
  "url": "https://source.com/test_file.docx",
  "type_productions": "client",
  "details": {
    "name": "Test Book",
    "raw_authors": [
      {
        "first_name": "Frank",
        "last_name": "Herbert"
      }
    ],
    "comment": "Some comment for us",
    "remote_key": "id-from-your-system",
    "speaker": 214,
    "speed": 1,
    "volume": 1,
    "isbnx": "111111111",
    "book_language": "english"
  }
}

```

We recommend split author name by part. It will help us correct create meta information about the book.

- Send request to the server. Server will return status 202;
- Once the order is created, our system will send notification on Webhook;
- Now you can get information about the order over API with help order ID.


### Step 3

Audio editing.

Next workflow can be different rely on flow which sets for your.

Take the next flow:

None -> Voiced

Voiced -> Complete Order

Previous step:
- You create the order over API;

Current steps:

- After that our system create book and voice it;
- You take information about the order over API [endpoint](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#retrieve-order);
- The order contain field "guest_token_link". If this field is not null you can use this link for redirect to audio editor;
- Audio editing;
- Once you complete audio editing you can push button "complete editing" in editor. Our system will start build book and send notification after complete.

Information about book builds you can get from the order information (*30 days after build*).

If you want to embed editor in to your site use [this](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#embedded)


## Case 2 (type of production - "outsource"):

If you want that our editors work on books.


### Step 1:

These steps need execute before creates orders in the Tracker.

- You need have Customer in Speechki Tracker;
- Set Webhook address in the Customer settings;
- Create API key for your Customer admin or manager. All orders will be created from this user.


### Step 2:

Now you can create orders.

- Take languages list over [endpoint](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#book-languages);
- Now you can take voices list with filter by language over [endpoint](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#speakers);
- After that you can build request body

```json
{
  "url": "https://source.com/test_file.docx",
  "type_productions": "outsource",
  "details": {
    "name": "Test Book",
    "raw_authors": [
      {
        "first_name": "Frank",
        "last_name": "Herbert"
      }
    ],
    "comment": "Some comment for us",
    "remote_key": "id-from-your-system",
    "speaker": 214,
    "speed": 1,
    "volume": 1,
    "isbnx": "111111111",
    "book_language": "english"
  }
}

```

We recommend split author name by part. It will help us correct create meta information about the book.

- Send request to the server. Server will return status 202;
- Once the order is created, our system will send notification on Webhook;
- Now you can get information about the order over API with help order ID.


### Step 3:

- Our editors work on the book (audio editing and proofing);
- Once the editors complete work on the order, our system will start build book process and notification after complete;
- You can take information about builds from webhook notification or [order endpoint](https://github.com/speechki-book/speechki-open-api/blob/master/hermes/index.md#retrieve-order).