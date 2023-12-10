# HTMLscript
HTMLScript is a scripting language inspired by JSX and HTMLX that allows you to embed dynamic behavior directly within your HTML, making it versatile for both server-side rendering and client-side interactions. 

This is possible thanks do Javascript doing all its work behind the scenes.

It works by processing tags both at compile time (useful for server side rendering or static generation) and at run time (useful for client side rendering).

```html
<html>
  <title>HTMLScript</title>
  <script src="assets/client.js"></script>
  <body>

    <!-- This is imported when the code is transpiled -->
    <import from="/header.html" />

    <variable name="books" value="['libro 1','libro 2']" />
    <div class="books">
      <for each="book" of="books">
        <div class="book">
          <variable name="book" />
        </div>
      </for>
    </div>
  
    <!-- Everything inside the interpret tag can executed at runtime -->
    <h1>Hello Micheal! ...What your name is not Micheal? What is it then?</h1>
    <interpret>
      <input type="text" name="yourname" placeholder="Insert your name" />
      <if condition="yourname">
        Sorry <variable name="yourname" />, I didn't know it was you.
      </if>
  
      <!-- This is imported when code is executed -->
      <import from="/footer.html" />
    </interpret>
    <script>
        //You can still mix it with regular javascript of corse
    </script>
  </body>
</html>
```

If the code has at least an interpret tag the transpiler will

## Disclaimer
This project was created for fun and experimental purposes. It's a proof of concept and is not advised for production use due to its use of eval, which can pose security risks. Please be cautious and avoid using it in critical applications.

## Features
- Dynamic Rendering: HTMLScript introduces custom tags like <import>, <variable>, <if>, <else>, <for>, and more to enable dynamic content rendering.
- One single js file to import
