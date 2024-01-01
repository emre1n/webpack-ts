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

## Add Webpack production server configuration

Add a file `webpack.config.prod.js` to root directory

Copy and paste the content of the original `webpack.config.js` file into the `webpack.config.prod.js`

Rename the `mode` from `development` to `production`

Update the `build` script in `package.json` pointing the `webpack.config.prod.js` file using the `--config` option.

```JSON
  "scripts": {
    "serve": "webpack serve",
    "build": "webpack --config webpack.config.prod.js"
  },
```

To differentiate each build after making changes to the source code, update the `filename` of the `output` property of the `webpack.config.prod.js` with `[contenthash]` prefix.

This is important for the browser to catch when there is a change in the bundle file.

```javascript
  output: {
    filename: '[contenthash].bundle.js',
    path: path.resolve(__dirname, 'dist'),
    publicPath: '/dist',
  },
```

Now each build file will start with a different hash.

To clean the old build files, [clean-webpack-plugin](https://www.npmjs.com/package/clean-webpack-plugin) will be used.

It will automatically empty out the `dist` directory everytime there is a new build instead of adding on.

Install `clean-webpack-plugin`

```bash
pnpm add -D clean-webpack-plugin
```

Go to `webpack.config.prod.js` require the plugin.

```javascript
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
```

Add a `plugins` section to the config.

```javascript
  plugins: [
    /**
     * All files inside webpack's output.path directory will be removed once, but the
     * directory itself will not be. If using webpack 4+'s default configuration,
     * everything under <PROJECT_DIR>/dist/ will be removed.
     * Use cleanOnceBeforeBuildPatterns to override this behavior.
     *
     * During rebuilds, all webpack assets that are not used anymore
     * will be removed automatically.
     *
     * See `Options and Defaults` for information
     */
    new CleanWebpackPlugin(),
  ],
```

At the end undo the [contenthash] to keep it clean for future reference.
Since the script source of the `index.html` is just `bundle.js`.

Rename the `webpack.config.js` to `webpack.config.dev.js`
And in `package.json` update the script, no longer the default config file but pointing the `webpack.config.dev.js` file.

```JSON
  "scripts": {
    "serve": "webpack serve --config webpack.config.dev.js",
    "build": "webpack --config webpack.config.prod.js"
  },
```
