# Webpack with TypeScript

## Install Project Dependencies

```bash
pnpm add -D webpack webpack-cli typescript ts-loader
```

1. `webpack` is the module bundler
In order to use Webpack from the command line, or to call it from within our package.json, `webpack-cli` is also needed.

2. `typescript` to make sure TypeScript with the particular version is in the dependencies.

3. `ts-loader`, TypeScript loader, is the middleman between TypeScript and Webpack.
It is going to call TypeScript, basically call the TSC command and compile our TypeScript into JavaScript, and then hand that over to Webpack, which will then bundle everything together.

## Create webpack config file

[TypeScript](https://webpack.js.org/guides/typescript/) integration doc

Create `webpack.config.js` file in the root folder

Inside webpack.config.js write the following config. (This config can be found in webpack documentation.)

```javascript
const path = require('path');

module.exports = {
  entry: './src/index.ts',
  module: {
    rules: [
      {
        test: /\.tsx?$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
  resolve: {
    extensions: ['.tsx', '.ts', '.js'],
  },
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
  },
};
```

## Add build script to package.json

```JSON
  "scripts": {
    "start": "lite-server",
    "build": "webpack"
  },
```

## Remove all the .js file extensions from imports

`.js` file extensions have to be removed because webpack is going to add the extensions

## Run the build script

```bash
pnpm build
```

## Update the script source in index.html

Update the `src` of `script` tag in the `index.html` with `bundle.js` in the `dist` folder and remove the `type="module"` because now it is a single bundle file that includes everything, no imports or exports.

```html
    <script src="dist/bundle.js"></script>
```

## Run the development server

```bash
pnpm start
```

## Add source maps

Source maps will basically take that minified bundle and map it backwards to its prebuilt state so that the actual code that makes up that bundle can be seen where it is coming from.

Add `sourceMap` property `true` to `tsconfig.json`

```JSON
{
    "compilerOptions": {
      "outDir": "./dist/",
      "sourceMap": true,
      "noImplicitAny": true,
      "module": "commonjs",
      "target": "es5",
      "jsx": "react",
      "allowJs": true,
      "moduleResolution": "node",
    }
  }
```

Add the `devtool: 'inline-source-map'` in `webpack.config.js` file, to extract those source maps and include them in the final bundle.

```javascript
 module.exports = {
    entry: './src/index.ts',
    devtool: 'inline-source-map',
    module: {
```

Restart server

## Add Webpack development server configuration

Add `mode: 'development'` to `webpack.config.js`,

In order to switch from `lite-server` to Webpack dev server;

Install `webpack-dev-server`,

```bash
pnpm add -D webpack-dev-server
```

Add `devServer` property to `webpack.config.js`

```javascript
entry: './src/index.ts',
  devtool: 'inline-source-map',
  devServer: {
    static: {
      directory: path.join(__dirname, './'),
    },
  },
```

Add `serve` script to `package.json`
Replace it with `start` script which starts the server with `lite-server`.

```JSON
{
    "scripts": {
        "serve": "webpack serve"
    }
}
```

Add `publicPath` property to `webpack.config.js` file

```javascript
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
    publicPath: '/dist',
  },
```
