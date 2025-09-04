# Project setup

$ npm install

# Lerna setup & init

$ npx lerna init --dryRun --exact
--dryRun: see the actual options before applied
--exact: set exact versions to packages

After review remove --dryRun flag
`package.json`
the workspaces key is already setup from lerna
so that it could be managed by the package manager (e.g. yarn)
Nore useWorkspaces is deprecated lerna > v6.x
https://lerna.js.org/docs/legacy-package-management#migrating-from-lerna-bootstrap-lerna-add-and-lerna-link-in-lerna-v7-and-later

Custom hoisting
https://yarnpkg.com/configuration/yarnrc/#nmHoistingLimits

`lerna.json`
The packages key is optional

Then run
$ npm install

Add packages
$ npx lerna create package_1
$ npx lerna create package_2

## Link package dependencies

Workspaces:

```
// package_2/package.json
{
  ...,
  "dependencies": {
    // workspace
    "package_1": "*"
  }
}
```

Via package manager:
$ npm install <dependency> -w <package1> -w <package2>

Local path:

```
// package_2/package.json
{
  ...,
  "dependencies": {
    "package_1": "file:../package_1"
  }
}
```

Go back to root and run
$ npm install

### Commands

Repair after migration - Remove legacy options
$ npx lerna repair

List packages
$ npx lerna list

Build monorepo
$ npx lerna run build

Build package only
$ npx lerna run build --scope=<package>`or`--scope <package> // -> >=v7 syntax

Clean packages node_modules
$ npx lerna clean

# Nx

$ npm nx graph
