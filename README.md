# Design Manual Scraper
Generate components.json file for [Design Manual](https://github.com/EightMedia/design-manual) capturing HTML comments and their corresponding HTML components from a website.


## Usage
Use the keyword @component to flag html comments in your templates. Comments should be written in properly formatted [YAML](http://en.wikipedia.org/wiki/YAML) format.

```html
<!-- @component
    name: My Component
    description: this is a description for my component
-->
```

The immediate next DOM-node after the comment will be used as HTML-source for the component. If you need to capture multiple blocks, see [Capture multiple](#capture-multiple-blocks) blocks.

```html
<!-- @component
    name: My Component
-->

<div>This is my component</div>
<div>This isn't</div>
```

---


### Output file
Output will look something like this.

```json
[
  {
    "meta": {
      "name": "foo",
      "description": "foo description"
    },
    "file": "file.html",
    "source": "<div>this is my component</div>"
  }
]
```


---

### Examples
You can add an example or multiple examples with the `example` or `examples` keyword, where the former is a YAML string and the latter a YAML list. The `{{block}}` flag will be replaced by the captured block

_single example_
```html
<!-- @component
    name: My Component
    example: |
        <div class="max-width: 300px">
            {{block}}
        </div>
-->

<p>this is my example</p>
```

_multiple example_
```html
<!-- @component
    name: My Component
    examples: 
        - >
            <div class="max-width: 300px">
                {{block}}
            </div>
        - >
            <div class="max-width: 300px">
                {{block}}
            </div>
-->

<p>this is my example</p>
```

---

### Capture multiple blocks
The `capture` keyword specifies how many blocks after the comment will be returned. Use `capture: all` to capture the rest of the document. Use `capture: section` to capture all items until the next @component tag.

```html
<!-- @component
    name: My Component
    capture: 3
-->

<div>1</div>
<div>2</div>
<div>3</div>
<div>nope</div>
<div>nope</div>
```


---

### Reserved words
* `example` for an example block
* `examples` for multiple example blocks
* `capture` for the number of DOM-nodes to be captured
    - `all`: the rest of the DOM-nodes within the comments` parent
    - `section`: the rest of the DOM-nodes within the comments` parent or a new @component comment
    - `number`: the exact positive number of DOM-nodes

---

## How to use
```js
var scraper = require('design-manual-scraper');

scraper({
    url: 'http://www.google.com/',
    input: {
        home: 'index.html',
        news: 'news.html',
        content: 'content.html'
    },
    keyword: '@component',
    block: '{{block}}',
    output: 'components.json',
    complete: function(){}
});

```
