AMD Module Loader
=====

Supports loading modules via combo URL. Modules can be loaded automatically when some other module is being triggered, but only if some condition is met.

How to build it?
-------------

1. Clone or fork the repository on your machine.
2. Install NodeJS.
3. Install Gulp `[sudo] npm install -g gulp`.
4. Run `npm install` in the cloned/forked repository.
5. Run `gulp` to build it.

This will build the loader in 'dist' directory.

How to run the demo?
-------------
1. The default configuration and the demo require a combo loader. Go to the folder, where you cloned the loader, then run `node combo.js`. This will run a combo handler on port 3000.
2. Download [mongoose](https://github.com/cesanta/mongoose) or if you have brew just do `brew install mongoose`, then navigate to Loader folder and run mongoose with the following params:
    `mongoose -listening_port 8080 -document_root dist`.
3. Open a browser, for example Chrome and load  `http://localhost:8080/demo/`. Open the console and look for the messages. You will see that resouces are being loaded.

Loader features
======

1. Supports combo loading of the resources.
3. Supports conditional loading.
4. The configuration can be auto generated.

Registering modules
======

Use `define` function:

```javascript```
define('aui-dialog', ['aui-node', 'aui-plugin-base'], function(node, pluginBase) {
    return {
        log: function(text) {
            console.log('module aui-dialog: ' + text);
        }
    };
}, {
	condition: {
        trigger: 'aui-test',
        test: function() {
            return true;
        }
    },
    path: 'aui-dialog.js'
});
```

You may omit the condition (the fourth param in define function) if you don't need it.

Alternatively, you may use ES6 syntax in your modules:

```javascript```
'use strict';

import {log as logBase} from 'aui-base';
import {log as logCore} from 'aui-core';
import {log as logEvent} from 'aui-event';

function log(text) {
    logEvent('module aui-dialog says via aui-event: ' + text);
    logBase('in module aui-dialog logBase is available: ' + text);
    logCore('in module aui-dialog logCore is available: ' + text);
}

export {log};

/**
 * The code below is meta configuration, in this case it includes module condition only.
 * You may delete the whole function statement if you don't need it.
 */
(function META() {
    return {
        condition: {
            test: function() {
                return true;
            },
            trigger: 'nate'
        },
        path: 'nate.js'
    };
});
```
Loader understands only AMD currently and in more details - exactly what https://github.com/esnext/es6-module-transpiler generates. As soon as you transpile ES6 modules to AMD, the config generator will parse them successfully in order to generate the configuration and Loader will load them too!

Loading modules
======

Use `require` method:
```javascript```
require('aui-base', 'aui-test', function(base, test) {
	    // your code here
	}, function(error) {
	    console.error(error);
	});
```

Automatically generating the configuration
======

In order to generate the configuration, there is a small NodeJS program, called `config-generator`. You may use it to generate the configuration like this:
```bash```
$ node config-generator.js -c src/config/config-base.json -o src/config/config.js src/modules
```

My advice is to create a separate, base config file and pass it to the config generator as in the code above. In the base file you may define the groups, the URLs, combine flags, etc. and then leave config generator to add the rest.
Look on the example modules and the demo for more information. Then, just load the generated configuration to the browser and the Loader will do the rest.

Enjoy!

