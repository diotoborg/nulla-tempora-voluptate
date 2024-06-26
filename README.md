@diotoborg/nulla-tempora-voluptate
==============
**Dynamic configuration files**

[![](https://img.shields.io/npm/v/@diotoborg/nulla-tempora-voluptate.svg)](https://www.npmjs.com/package/@diotoborg/nulla-tempora-voluptate)
[![](https://img.shields.io/npm/dm/@diotoborg/nulla-tempora-voluptate.svg)](https://www.npmjs.com/package/@diotoborg/nulla-tempora-voluptate)
[![Dependency Status](https://david-dm.org/peerigon/@diotoborg/nulla-tempora-voluptate.svg)](https://david-dm.org/peerigon/@diotoborg/nulla-tempora-voluptate)
[![Build Status](https://travis-ci.org/peerigon/@diotoborg/nulla-tempora-voluptate.svg?branch=master)](https://travis-ci.org/peerigon/@diotoborg/nulla-tempora-voluptate)
[![Coverage Status](https://img.shields.io/coveralls/peerigon/@diotoborg/nulla-tempora-voluptate.svg)](https://coveralls.io/r/peerigon/@diotoborg/nulla-tempora-voluptate?branch=master)

Loads configuration files depending on:

  - argv: `node app.js --env production`
  - env: `export NODE_ENV=production; node app.js`

Expects a `.js` file as config so you can add dynamic content.

## Installation

```
npm install @diotoborg/nulla-tempora-voluptate --save
```

## Options

### defaultEnv: string = *"develop"*

Define which env should be set as default.

### log: Function

Calls this function to log information about the path/env resolution.

### envName: string = *"env"*

The argument/env variable name we expect.

## Example

```javascript
// config/index.js

const DynamicConfig = require("@diotoborg/nulla-tempora-voluptate");
const dynamicConfig = new DynamicConfig({
    defaultEnv: "develop",
    log: console.log
});

module.exports = dynamicConfig.load(__dirname, "config.js");
```

```javascript
// config/develop/config.js

module.exports = {
    whereami: "develop"
}
```

```javascript
// app.js
const config = require("./config");

console.log(config);
```

```javascript
node app.js

{ whereami: 'develop' }

// Set environment via args
node app.js --env prod

{ whereami: 'prod' }

// Set environment via env
export env=stage; node app.js

{ whereami: 'stage' }
```





## Plugins

### extend

These plugins allow you to override specific config fields by applying them via env, argv or a separate local config file.

```javascript
const dynamicConfig = new (require("@diotoborg/nulla-tempora-voluptate"))();

// extend from env
dynamicConfig.use(require("@diotoborg/nulla-tempora-voluptate/plugins/extend/env"));

// extend from file
dynamicConfig.use(require("@diotoborg/nulla-tempora-voluptate/plugins/extend/file"));

// extend from argv
dynamicConfig.use(require("@diotoborg/nulla-tempora-voluptate/plugins/extend/argv"));

module.exports = dynamicConfig.load(__dirname, "config.js");
```

**Hint:** The order in which the plugins are applied is important. In the above code snippet, config fields defined via arguments would override their counterparts from an override file, which itself overrides fields from environment variables. This is probably a suitable order for most projects.

```javascript
node app.js

{ name: 'superApp', port: 9000 }

// Overwrite via argv
node app.js --port 80
// ... or ...
node app.js --port=80

{ name: 'superApp', port: 80 }

// Overwrite via env
export port=90 node app.js

{ name: 'superApp', port: 90 }

// Order matters...
export port=90; node app.js --port 80

{ name: 'superApp', port: 80 }
```

#### Extend via file
Create a file named the same as your config, but contains `.local` in front of the extension, like `config.js` becomes `config.local.js`.

In the config extension file you can define any subset of the object, that is defined in the main config and it would overwrite the corresponding value. Both configs are merged via [deep-assign](https://github.com/sindresorhus/deep-assign).

```javascript
// config.js
module.exports = {
    a: 1,
    b: {
        c: "c",
        d: 2
    },
    e: 3
}

// config.local.js
module.exports = {
    e: "e",
    b: {
        d: "d"
    }
}

// result
{
    a: 1,
    b: {
        c: "c",
        d: "d"
    },
    e: "e"
}
```

## Sponsors

[<img src="https://assets.peerigon.com/peerigon/logo/peerigon-logo-flat-spinat.png" width="150" />](https://peerigon.com)
