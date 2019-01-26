# react-native-typed-less-transformer

[![NPM version](http://img.shields.io/npm/v/react-native-typed-less-transformer.svg)](https://www.npmjs.org/package/react-native-typed-less-transformer)
[![Downloads per month](https://img.shields.io/npm/dm/react-native-typed-less-transformer.svg)](http://npmcharts.com/compare/react-native-typed-less-transformer?periodLength=30)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://egghead.io/courses/how-to-contribute-to-an-open-source-project-on-github)

Load Less files to [react native style objects](https://facebook.github.io/react-native/docs/style.html).

This transformer also generates `.d.ts` Typescript typings for the Less files. Notice that platform specific extensions are not supported in the Typescript typings.

> This transformer can be used together with [React Native CSS modules](https://github.com/kristerkari/react-native-css-modules).

_Minimum React Native version for this transformer is 0.52. If you are using an older version, please update to a newer React Native version before trying to use this transformer._

## Usage

### Step 1: Install

```sh
yarn add --dev react-native-typed-less-transformer less
```

### Step 2: Configure the react native packager

#### For React Native v0.57 or newer / Expo SDK v31.0.0 or newer

Add this to `rn-cli.config.js` in your project's root (create the file if it does not exist already):

```js
const { getDefaultConfig } = require("metro-config");
module.exports = (async () => {
  const {
    resolver: { sourceExts }
  } = await getDefaultConfig();
  return {
    transformer: {
      babelTransformerPath: require.resolve(
        "react-native-typed-less-transformer"
      )
    },
    resolver: {
      sourceExts: [...sourceExts, "less"]
    }
  };
})();
```

If you are using [Expo](https://expo.io/), you also need to add this to `app.json`:

```json
{
  "expo": {
    "packagerOpts": {
      "config": "rn-cli.config.js"
    }
  }
}
```

#### For React Native v0.56 or older

If you are using React Native without Expo, add this to `rn-cli.config.js` in your project's root (create the file if you don't have one already):

```js
module.exports = {
  getTransformModulePath() {
    return require.resolve("react-native-typed-less-transformer");
  },
  getSourceExts() {
    return ["js", "jsx", "less"];
  }
};
```

#### Expo SDK v30.0.0 or older

If you are using [Expo](https://expo.io/), instead of adding the `rn-cli.config.js` file, you need to add this to `app.json`:

```json
{
  "expo": {
    "packagerOpts": {
      "sourceExts": ["js", "jsx", "less"],
      "transformer": "node_modules/react-native-typed-less-transformer/index.js"
    }
  }
}
```

## How does it work?

Your `App.less` file might look like this:

```less
@nice-blue: #5b83ad;
@light-blue: @nice-blue + #111;

.myClass {
  color: @light-blue;
}
.myOtherClass {
  color: red;
}
```

When you import your stylesheet:

```js
import styles from "./App.less";
```

Your imported styles will look like this:

```js
var styles = {
  myClass: {
    color: "#6c94be"
  },
  myOtherClass: {
    color: "red"
  }
};
```

The generated `App.less.d.ts` file looks like this:

```ts
export const myClass: string;
export const myOtherClass: string;
```

You can then use that style object with an element:

```jsx
<MyElement style={styles.myClass} />
```
