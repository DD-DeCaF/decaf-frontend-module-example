# Decaf Component
> A component for the Decaf Platform.

## Installation
----------------
Download the [component](https://github.com/biosustain/decaf-frontend-module-example/archive/master.zip) or clone it `git clone git@github.com:biosustain/decaf-frontend-module-example.git`.

**NOTE**: Cloning this project will also keep all the commit history, so if you wish to start with a fresh commit history, I advise downloading it.

Install dev and runtime dependencies:
* `npm install`
* `$(npm bin)/typings install`


## Setup
---------
If you successfully setup the project using the steps above, you can run the app with `$(npm bin)/gulp serve`.

**NOTE**: [JSPM](http://jspm.io/0.17-beta-guide/index.html) is used as package manager and for installing the components itself as well. 

You should only be working in the `src/` folder of the component and you should never remove `index.ts` unless you know what your're doing.
But make sure to rename the `example.component.ts|css|html` with your own component name (e.g. `hero.component.ts|css|html`).
After you renamed it, make sure to update the component path/name in the `index.ts` file:
```js
export * from './<component name>.component';
import main from './<component name>.component';
```

Furthermore, make sure you export the angular module as default from your component (name does not matter):
```js
// src/my-component.component.ts
import {dirname} from 'decaf-common';

export const COMPONENT_NAME = 'example';
const myComponent = angular.module(COMPONENT_NAME, []);


myComponent.config(function (platformProvider) {
	platformProvider
		.register(COMPONENT_NAME)
		.state(COMPONENT_NAME, {
			url: `/${COMPONENT_NAME}`,
			views: {
				'content@': {
					templateUrl: `${dirname(module.id)}/my-component.component.html`,
					controller: MyComponentController,
					controllerAs: 'myComponent'
				}
			}
		});
});


class MyComponentController {
	constructor() {}
}

// Note the default export
export default myComponent;
```

In the above example, there are a few things that are important:
* `platformProvider.register(COMPONENT_NAME)` - This is a mandatory action. You use that to register a component.
* `dirname(module.id)` - This is just a helper to get the path for where the component resides, you should always use it.
* `platformProvider.state()` - This sets the states/routes for the component.
It is just a proxy to Angular UI Router's [$stateProvider.state()](http://angular-ui.github.io/ui-router/site/#/api/ui.router.state.$stateProvider), thus everything you'd configure with it, you also do it with the `platformProvider.state()`.

Note that the `{view}` property of the state contains the key `content@`.
That will be the entry point of the component markup when navigating to the component route or any subroutes of the component.
Besides `content@`, you also have the option to overide the toolbar by providing a view with the key `toolbar@`.


## Development
---------------
I advise linting the source files before you commit, use `$(npm bin)/gulp lint`.

For other tasks run `$(npm bin)/gulp --tasks`.

### New dependencies
--------------------
New packages can be used by first making sure you have `jspm` installed
```
npm install jspm -g
```

then

```
jspm install <package>
```

this will automatically adjust `package.json`. Next install the typics
```
typings install dt~<package> --global --save
```
Make sure your component.ts file has 
```
/// <reference path="../typings/index.d.ts"/>
```
to indicate where to look for imports. Then you import your package with
```
import '<package>'
```

Make sure you inject the new package (string) when you create the
module (call to `angular.module`) and inject the new class to your
class by defining this in the constructor e.g.

```
...
myNewDep: NameOfClass;

constructor(..., NewDep: NameOfClass) {
   this.myNewDep = NewDep;
   ...
}
```

Knowing the `NameOfClass` is not necessarily obvious but studying
installed typings you may find a clue as to what you should import.

### Deployment
--------------------

When frontend module is changed (updated on github), a few manual actions are needed to deploy it.

1. Trigger a build on
   [decaf-frontend docker hub](https://hub.docker.com/r/dddecaf/decaf-frontend/~/settings/automated-builds/). Pay
   attention to which image is deployed at the moment.
2. Wait until new image is built (see the [build status page](https://hub.docker.com/r/dddecaf/decaf-frontend/builds/))
3. Redeploy `decaf-frontend`
   [stack on docker cloud](https://cloud.docker.com/app/dddecaf/stack/4ceb7e27-963f-4d22-85dd-96eaa210ffee/general). Make
   sure to not "Reuse existing container volumes?" as old frontend
   files are otherwise reused. Frontend container status being "stopped" is normal.
