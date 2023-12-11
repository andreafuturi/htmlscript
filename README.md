# HTMLScript
HTMLScript is a scripting language inspired by JSX and HTMLX that allows you to embed dynamic behavior directly within your HTML, making it versatile for both server-side rendering and client-side interactions. 

This is possible thanks to Javascript doing all the work behind the scenes.

It works by processing tags both at compile time (useful for server side rendering or static generation) and at run time (useful for client side rendering).

```html
<html>
  <title>HTMLScript</title>
  <script src="/htmlscript.js"></script>
  <body>

    <!-- This is imported when the code is transpiled -->
    <import from="/header.html" />

    <!-- Loops -->
    <variable name="books" value="['Book 1','Book 2']" />
    <ul class="books">
      <for each="book" of="books">
        <li class="book">
          <variable name="book" />
        </li>
      </for>
    </ul>

    <!-- Conditions -->
    <if condition="books.length > 100">
        Wow you have quite a lot of books here!
    </if>
    <else>
        I think you should buy a book!
    </else>

  
    <!-- Everything inside the interpret tag can executed at runtime -->
    <h1>Hello Micheal! ...What your name is not Micheal? What is it then?</h1>
    <interpret>
      <input type="text" name="yourname" placeholder="Insert your name" />
      <if condition="yourname">
        Sorry <variable name="yourname" />, I didn't know it was you.
      </if>
  
      <!-- This is imported when code is executed -->
      <import from="/footer.html">Loading footer...</import>
    </interpret>
    <script>
    //You can still mix it with regular javascript of corse
    </script>
  </body>
</html>
```


## Disclaimer
This project was created for fun and experimental purposes. It's a proof of concept and is not advised for production use due to its use of eval, which can pose security risks. Please be cautious and avoid using it in critical applications.

## Features
- Dynamic Rendering: HTMLScript introduces custom tags like `<function>`, `<if>`, `<else>`, `<for>`, and more to enable dynamic content rendering.
- 0 dependencies single js file to import (around 2kb gzipped, still not complete but still not optimized)
- It's regular XML syntax processed by Javascript nothig crazy (to be precise HTML6 express tags)
- Automatic input to variable binding, with automatic reactive variables
- Works on both client and server environments thanks to its usage of common DOM manipulation APIs (tested on server with [DenoDOM](https://github.com/b-fuze/deno-dom)
- Easy code splitting thanks to `<import>` that works both on client and server (only Deno server for now)
- Easily specify what parts of your code needs to be executed at compile and which needs to be executed at runtime for user interactivity.

## Usage
1. Include HTMLScript
```html
<script src="path/to/htmlscript.js"></script>
```
2. Apply processing after any HTMLScript code:
```html
<script>renderHTMLScript()</script>
```
You can pass to renderHTMLScript any DOM node, if nothing is passed than the whole document will be parsed by default.

It works both in the client and the server, I have tested it only with Deno but it should work as well with Node.js using another DOM library)

```jsx
import {renderHTMLScript} from 'htmlscript'

Deno.serve(async () => {
  function Page() {
    return <html>Your HTMLScript code here</html>
  }
  
  const html = renderToString(<App />)
  const serverDocument = new DOMParser().parseFromString(html, "text/html");
  
  renderHTMLScript(serverDocument)
  
  //send processed html string back to the client (<interpret> tags will be processed there)
  const processedHTML = serverDocument.documentElement.outerHTML;
  new Response(processedHTML, {
      headers: { "content-type": "text/html; charset=utf-8" },
  });
})
```
3. Except for the `<interpret>` tag, by default all HTMLScript tags will be removed after they're processed.
You will be left with simple basic HTML and nothing in between, but you can disable that by setting `window.debug = true` before running the processor.

In debug mode a CSS style is added to the document so that HTMLScript tags will have a display: contents and therefore they will not interfere with the rest of the html tags

## How it works
- HTMLScript is made in Vanilla JS and run functions to handle various HTMLScript tags, such as `<if>`, `<for>`, `<variable>`, and more.
- It switches logic if document variable is defined. For example when using the `<import>` tag, `Deno.readTextFileSync` is used on the server while `await fetch` is used on the client.
- When an interpret tag is found the processor ingores it and ships itself in the html through a `<script>` so that it can continue its processing at runtime (`<interpret>` is switched to `<interpreted>` for this to be possible)
  
```javascript
processImports = ${processImports.toString()};
  processVariables = ${processVariables.toString()};
  processLoops = ${processLoops.toString()};
  processConditions = ${processConditions.toString()};
  processInputs = ${processInputs.toString()};
  processFunctions = ${processFunctions.toString()};
  cleanHTMLScript = ${cleanHTMLScript.toString()};
  renderHTMLScript = ${renderHTMLScript.toString()};
  window.debug = ${window.debug || false};
  const interpretedTags = document.querySelectorAll("interpreted");
  interpretedTags.forEach(async (interpretedTag) => {
    processImports(interpretedTag);
    processInputs(interpretedTag);
    processVariables(interpretedTag);
    processLoops(interpretedTag);
    processConditions(interpretedTag);
    processFunctions(interpretedTag);
  });`;
}
```

## Philosophy
- It needs to be modular and extensible by people that provides more scripting tags to it
- The tags must be as simple as possible and use valid XML syntax
- It needs to work both on server and client environments
- It needs to be intuitive and easy to learn
- It needs to be concise and easy to read
- It needs to be performant

## Examples




## Experimenting
I'm experimenting with a `<transpile>` tag (wish I could find a better name for it)
It basically does the same thing of `<interpret>` but instead of shipping the transpiler, it will directly ship the needed js for that piece of code.
For example:
```html
<variable name="debug" value="true" />
```
can be converted to
```html
<script>
  const debug = true
</script>
```
I'm still trying to understand it there's any possible use case because I don't see a lot of benefits in it, but definitely worth investigating.
Maybe it should be just an interpret tag with a special withoutProcessor attribute.



## Future
- There are still a lof of Javascript features to be ported
- It would be nice if a VSCode extension helps you with typing of tags and js expressions by treating special tags attributes as Javascript.
- It would be nice if variables can have an external src which can be any web resource (including json for api endpoints)
- It would be nice to implement some sort of variables scoping
- It would be nice if a variable can be used also inside attributes for example:
```html
<variable src="/api/books" name="books" /> 
<for each="book" of="books">
  <li class="book">
    <variable name="book" value="book.title" />
    <img src="<variable value='book.img' />" />
  </li>
</for>

```





