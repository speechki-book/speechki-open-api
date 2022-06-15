# Speechki Speakers Widget

This is a widget which demonstrates Speechki speakers

## Usage

### Default

1. Paste script code somewhere on your page

```html
<script defer src="https://widget.speechki.org/widget.js"></script>
```

2. Create widget with `Speechki` library

```javascript
const widget = Speechki.widget({
    target: '#widget1',
    customer_id: YOUR_CUSTOMER_ID,
    book_language: DEFAULT_LANGUAGE,
});
```

### ESM

1. Install package through npm (or your prefered package manager)

```
npm i @speechki/widgets
```

2. Import `Speechki` module in your code

```
import Speechki from '@speechki/widgets'
```

2. Use `widget` method to create a widget

```javascript
const widget = Speechki.widget({
    target: '#widget1',
    customer_id: YOUR_CUSTOMER_ID,
    book_language: DEFAULT_LANGUAGE,
});
```

### Options

| Name            | Description                                                                                                                         |
| --------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| `target`        | CSS Selector or DOM element which widget will mount to <br> \*By default widget takes all the available space inside it's container |
| `customer_id`   | Your specific customer id in Speechki                                                                                               |
| `book_language` | Default language of the book, speakers will be filtered according to language                                                       |

### Methods

#### `on(name, callback)`

subscribes to widget event

##### Events

| Name     | Description                             | Data                                                                    |
| -------- | --------------------------------------- | ----------------------------------------------------------------------- |
| `select` | Triggers when users selects the speaker | `name` - speaker name <br> `id` - speaker id <br> `slug` - speaker slug |

Events data:

```javascript
{
  event // name of the event
  data: {
    ... // data passed with the event
}
```

#### `off(name, callback)`

unsubscribes from widget event

`name` - event name

`callback` - callback function to be called

#### `changeLanguage(language)`

changes the language filter in widget

`language` - language of your book correlating with our. [List of languages]()

## Examples

### Default

```html
...
<head>
    <script defer src="https://widget.speechki.org/widget.js"></script>
</head>
<body>
    ...
    <div id="widget1"></div>
    ...
    <script>
        document.addEventListener('DOMContentLoaded', function () {
            const widget1 = Speechki.widget({
                // creates widget
                target: '#widget1',
                customer_id: 3,
                book_language: 'english',
            });

            function onSelect(event) {
                alert(event.data.name);
            }

            widget1.on('select', onSelect); // subscribe to the event
            widget1.off('select', onSelect); // unsibscribe from the event

            widget1.changeLanguage('spanish'); // change language filter to spanish
        });
    </script>
</body>
```

### With simple form

<a href="https://widget.speechki.org/demo.html" target="_blank">Demo</a>

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width,initial-scale=1" />

        <title>Speechki Speakers</title>
        <link rel="stylesheet" href="https://cdn.simplecss.org/simple.min.css" />

        <script defer src="https://widget.speechki.org/widget.js"></script>
    </head>

    <body>
        <main>
            <h2>Form Demo</h2>
            <form id="form">
                <p>
                    <label for="title">
                        Book Title
                    </label><br>
                    <input type="text" id="title" placeholder="Title" name="title" />
                </p>
                <p>
                    <label for="title">ISBN</label><br>
                    <input type="text" id="title" placeholder="ISBN" name="ISBN" />
                </p>
                <p>
                    <label for="language">Language</label><br>
                    <select name="language" id="language">
                        <option selected value="english">English</option>
                    </select>
                </p>
                <p>
                    <label for="speaker">
                        Speaker
                    </label><br>
                    <div style="max-height: 300px" id="widget1"></div>
                </p>
                <button>Submit</button>
            </form>
        </main>
        <script>
            document.addEventListener('DOMContentLoaded', function () {
                const w1 = Speechki.widget({ // initialize widget
                    target: '#widget1',
                    customer_id: 3,
                    book_language: 'english',
                });

                language.addEventListener('change', function (event) { //change language
                    w1.changeLanguage(event.target.value);
                });

                let speaker;

                w1.on('select', (event) => { // subscribe to select event
                    speaker = event.data.slug;
                });

                form.addEventListener('submit', (event) => {
                    event.preventDefault();

                    let data = new FormData(event.target);

                    data.append('speaker', speaker);

                    data = JSON.stringify(Object.fromEntries(data));

                    alert(data);
                })
            });
        </script>
    </body>
</html>

```

## Self hosting

If you want to serve this widget from your server, then clone/fork the repository, store it somewhere on your servers and replace `WIDGET_URL=https://widget.speechki.org` to `WIDGET_URL=your_domain` in `.env.production` file.
