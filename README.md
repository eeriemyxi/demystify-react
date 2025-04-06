# React
This project aims to demistify React projects.

- References:
  * [Legacy Documentation for React](https://web.archive.org/web/20250401144427/https://legacy.reactjs.org/docs/introducing-jsx.html)
  * [React is NOT Magic: Understanding JSX](https://web.archive.org/web/20191126183639/https://medium.com/schubiedoobycode/react-is-not-magic-understanding-jsx-a5fcbbdd5f15)

# Babel
* Babel is a JavaScript compiler.
* It supports extensions, which it calls _plugins_.
* It also supports _presets_, which are basically preconfigured set of _plugins_, et cetera.

# JSX/TSX
JSX by itself is basically a spec for addional syntax for JavaScript. It was
founded by Facebook. There is also TSX which is an addition to syntax for
TypeScript. TypeScript itself is another compiler for JavaScript.

# React
React is a front-end framework for JavaScript that helps with making UI
components for the web.

# Relation of JSX/TSX to React
Here are two sample JSX files:

```jsx
const App = () => {
  return <h1>Hello, JSX!</h1>;
};

export default App;
```

```jsx
function HelloMessage({ name }) {
    return <div>Hello {name}</div>;
}

const root = ReactDOM.createRoot(document.getElementById('container'));
root.render(<HelloMessage name="Taylor" />);

```

These are JSX files. The extension for these files is `.jsx`. A babel _preset_
is used to compile JSX files to plain `.js` files. Here are the compiled output:

```js
const App = () => {
  return React.createElement("h1", null, "Hello, JSX!");
};
export default App;

```

```js
function HelloMessage({name}) {
  return React.createElement("div", null, "Hello ", name);
}

const root = ReactDOM.createRoot(document.getElementById('container'));

root.render(React.createElement(HelloMessage, {
  name: "Taylor"
}));
```

To compile JSX files manually, one could follow this procedure:

We add three packages from NPM package index: `@babel/core`, `@babel/cli`, `@babel/preset-react`

```
mkdir /tmp/react-test/ 
cd /tmp/react-test 
bun init
bun install @babel/{core,cli,preset-react}
```

> [!NOTE] 
> I will be using Bun instead of Node, or Deno, or something else, for
> my own convenience.

* `core` contains the compiler from Babel
* `cli` is source code for their CLI interface
* `preset-react` brings in the preset that lets Babel understand JSX files.

You can now compile (e.g., `App.js` to `App.vanilla.js`) your script by doing:

```
bun run sh
babel App.js --outfile App.vanilla.js --presets "@babel/preset-react"
```

We specify the preset to use by the `--presets` CLI flag, some may use another
method, like creating a configuration file.

# Minimal Example For React v18
All the help that React brings is in a single JavaScript file that doesn't
require any server-side runtimes. It is all client-side.

The server-side libraries that React in its documentation tells you to use, only
provide a bunch of magical functionalities that aid in generating client-side
Javascript code.

We will now be creating a new project directory, then moving our generated
`App.vanilla.js` there:

```jsx
function HelloMessage({name}) {
  return React.createElement("div", null, "Hello ", name);
}

const root = ReactDOM.createRoot(document.getElementById('container'));

root.render(React.createElement(HelloMessage, {
  name: "Taylor"
}));
```

```bash
DIR=$(mktemp -d)
cp App.vanilla.js $DIR
cd $DIR
```

Now we need to grab the React client-side runtime. I'll using [jsDelivr](https://jsdelivr.net) CDN,
but there is also [https://unpkg.com/](UNPKG).

You can find the files for React [here](https://app.unpkg.com/react@18.3.1). 

Now, we create an `index.html` file,

```html
<html>
  <head>
    <title>React App</title>
  </head>
  <body>
    <div id="container"></div>
    <script src="https://cdn.jsdelivr.net/npm/react@18.3.1/umd/react.production.min.js" crossorigin></script>
    <script src="https://cdn.jsdelivr.net/npm/react-dom@18.3.1/umd/react-dom.production.min.js" crossorigin></script>
    <script src="App.vanilla.js"></script>
  </body>
</html>
```

Then we simply run it on any web server. I'll using a simple one that Python
provides:

```bash
python3 -m http.server -d . 8080
```

Visit `http://0.0.0.0:8080`, you should see "Hello Taylor".

# Minimal Example For React v19
The previous example will no longer work out like it should anymore for React
versions above v18. To understand why, we need to understand how modules in
JavaScript work.

Modules in JavaScript are a complicated mess and I would highly recommend going
through the following resources entirely:

* https://developer.mozilla.org/en-US/docs/Web/HTML/Element/script
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules
* https://eloquentjavascript.net/10_modules.html

Earlier, React was providing prebuild [UMD
modules](https://github.com/umdjs/umd), however they've since stopped doing that
and now because of all that, I'll also be demonstrating the new way:

This is the `App.vanilla.js` file:
```js
import React from "react"
import ReactDOM from "react-dom/client"


function HelloMessage({name}) {
  return React.createElement("div", null, "Hello ", name);
}

const root = ReactDOM.createRoot(document.getElementById('container'));

root.render(React.createElement(HelloMessage, {
  name: "Taylor"
}));
```

This is the `index.html` file:
```html
<html>
  <head>
    <title>React App</title>
  </head>
  <body>
    <div id="container"></div>
    <script type="importmap">
      {
        "imports": {
           "react": "https://esm.sh/react@18.2.0",
           "react-dom/": "https://esm.sh/react-dom@18.2.0/"
         }
      }
    </script>
    <script type="module" src="App.vanilla.js"></script>
  </bod>y
</html>
```

# Additional Resources
  * [Sunsetting Create React App](https://react.dev/blog/2025/02/14/sunsetting-create-react-app)
  * [Rendering on the Web](https://web.dev/articles/rendering-on-the-web)
  * <https://esm.sh/#docs>
  * [How Does Esm.Sh Convert These ESM Modules from CJS Modules?](https://github.com/esm-dev/esm.sh/discussions/595)
