# Lerna setup & init

`npx lerna init --dryRun --exact`
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
`npm install`

Add packages
`npx lerna create package_1`
`npx lerna create package_2`

## Link package dependencies

Via package manager:
npm install <dependency> -w <package1> -w <package2>

Manually:
Go to package_2 package.json file
add in dependencies add local path association to package_1

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
`npm install`

### Commands

Build monorepo
`npx lerna run build`

Build package only
`npx lerna run build --scope=<package>` or `--scope <package>` -> >=v7 syntax

# Nx

`npm nx graph`
