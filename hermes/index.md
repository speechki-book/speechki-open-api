# Hermes

Hermes is service which provide integration with CRM and give opportunity to create orders (books) over REST API.

At the moment we haven't sandbox, so all test for integration will be makes on production environment.

## Requirements

To work with API you need have:
- **Customer** in Speechki CRM
- Customer token - needs for call Hermes REST API
- Prepare endpoint for WebHook


## Customer Token

Customer admin and manager can create this token. The token has no lifetime limit. To revoke a token, it must be destroyed.

Token help us identify current Customer and user. All the actions that will be performed will be written to this user.

Http Header - **Authorization**.

Authorization header format - **Token waGpB26t.JXD7RPEHqJVdTCy2AZl7HM0Garmv9DQB**

## Swagger Doc

Actual documentation for endpoints contains in https://hermes.books.speechkit.ru/docs


### Book languages

This endpoint absolutely open for requests.

It is dictionary where key - unique id for language and value - label for this language.

These values used for filter voices (speakers) and creating book (text parsing).

Book languages [endpoint](https://hermes.books.speechkit.ru/docs#/speech_settings.v1/get_book_languages_handler_api_v1_speech_settings_languages__get)


### Speakers

This endpoint requires Auth Token.

List of voices you can use to create books. Your decision will affect the voice of the book.

This endpoint has required query parameter - **book_language** (endpoint Book Languages)

Speakers [endpoint](https://hermes.books.speechkit.ru/docs#/speech_settings.v1/get_speakers_handler_api_v1_speech_settings_speakers__get)


### Order


#### Create Order

This endpoint requires Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/create_order_handler_api_v1_orders_orders__post)

##### Request Body

url - Require field. Text document URL address. Supports **docx** and **pdf** formats.It is recommended to give files without pictures and tables.


type_productions - Supports 2 options. Client - it is mean that the client is independently working on the book. If this field is not used, our system will set a default value for type_productions for your Customer.


details - Required Dictionary with settings:


- comment - Optional text for us.


- remote_key - Optional field but can help you identify the order in your system. Use id from your system.


- speaker - Require field. Needs to use speaker id which you can get from Speakers endpoint.


- speed - Optional field. You can choose audio speed. Default value 1.0.


- volume - Optional field. You can choose audio volume. Default value 1.0.


- isbnx - Optional field. If you have ISBN, you can set it.


- book_language - Require field. Needs to use language name from Book Languages endpoint.


- raw_authors - Require field. Object contains first_name (required), last_name (Optional), patronymic (Optional. This is third name)


##### Response

In the good case service return HTTP Code 202 and message.


##### How get information about order.

After the order will create in our system, We will send notification on your webhook, which your set in our system. Notification contains information about orders, builds, transitions.


#### Retrieve Order


This endpoint requires Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/get_order_handler_api_v1_orders_orders__order_id___get)


Returns one order.


##### Response


Response contains information about order, builds and available transitions.

If the order contains book_id and type_productions is equal to client, then the guest_token_link field will contain a link to create a guest link.


Available transitions contains links for make transition. Every transition performs some actions that change the order.


#### List Order

This endpoint requires Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/get_orders_handler_api_v1_orders_orders__get)

Returns 20 last orders.


###### Response

Response contains information about orders.


If the order contains book_id and type_productions is equal to client, then the guest_token_link field will contain a link to create a guest link.


#### Guest Link

Returns link which redirect to Speechki editor.

This endpoint requires Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/create_guest_link_handler_api_v1_orders_orders__order_id__link__post)

You can redirect user to that URL or embed an iframe with it. Recommended minimum width for the iframe is 1200px.

##### Request Body

comeback_url - Optional field. The service will set the url to which the user can be returned from the Speechki editor.



#### Change State

Change current state. Variants for use this endpoint contains in the order.

This endpoint requires Auth Token.

[Endpoint](https://hermes.books.speechkit.ru/docs#/orders.v1/change_order_state_handler_api_v1_orders_orders__order_id__change_state__state___post)


### WebHook

Notification request use **POST** method

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
      "book_language": "russian"
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
