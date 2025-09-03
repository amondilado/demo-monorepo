# Monorepo Setup with Lerna and Git Submodules

**Basic migration setup**

1. Create the monorepo structure
   Make a new root folder for your monorepo (e.g., my-monorepo) and move both repositories inside:

```
my-monorepo/
  package.json   <-- root package.json (workspace config)
  lerna.json     <-- lerna config
  packages/
    library/
      package.json
    app/
      package.json

```

2. Initialize Lerna with workspaces
   From the root of the monorepo:

`npx lerna init --exact`

This will generate a lerna.json.
Since Lerna v8 uses npm/yarn/pnpm workspaces, you also need a root package.json that declares them.

```
// Root package.json:
{
  "name": "my-monorepo",
  "private": true,
  "devDependencies": {
    "lerna": "^8.0.0"
  },
  "workspaces": [
    "packages/*"
  ]
}

```

3. Configure lerna.json
   Update it to point to your workspaces:

```
{
  "version": "0.0.0",
  "useWorkspaces": true,
  "npmClient": "npm",   // or yarn/pnpm if you prefer
  "packages": [
    "packages/*"
  ]
}

```

4. Fix app’s dependency on library
   Inside packages/app/package.json, instead of pointing to a version on npm, make sure the dependency points to your local package name (the name field of packages/library/package.json).

For example:

```
// packages/library/package.json:
{
  "name": "@myorg/library",
  "version": "1.0.0"
}

// packages/app/package.json
{
  "name": "@myorg/app",
  "version": "1.0.0",
  "dependencies": {
    "@myorg/library": "1.0.0"
  }
}
```

Lerna (via workspaces) will automatically symlink @myorg/library into app/node_modules.

5. Bootstrap / install dependencies
   Now, just install dependencies at the root:

```
$ npm install
// or
$ yarn install
// or
$ pnpm install

```

Lerna + workspaces will link library into app.

6. Run and build

If each package has its own scripts (like build, test, etc.), you can run them with Lerna:

```
// Run build in all packages
$ npx lerna run build

// Run test only in app
$ npx lerna run test --scope @myorg/app

```

✅ At this point:
. Both app and library live in the same repo.
. app automatically uses the local library.
. You can still publish them separately if needed.

---

**Q:** Is it necessary to move both repositories inside the new monorepo root folder?

**A:** Yes, for true monorepo management, both must be moved, but alternatives include relative paths; or importing repos if needed.

---

## Git Submodules

Git submodules for keeping history separate but still using Lerna.

. Keep library and app as separate git repos.
. Create a new “umbrella” repo (your monorepo root).
. Add both as submodules inside packages/.

```
$ git submodule add git@github.com:your-org/library.git packages/library
$ git submodule add git@github.com:your-org/app.git packages/app
```

✅ Pros:
. Each repo keeps its own commit history.
. You can still develop inside the monorepo with lerna + workspaces.
. You can push commits to each repo separately.

⚠️ Cons:
. Submodules can be tricky to manage (require explicit git submodule update --init --recursive).
. Extra friction for contributors not familiar with submodules.

**Azure DevOps**
Azure DevOps supports submodules out of the box. There are a few caveats you should be aware of.

---

### Git Submodule Cheat Sheet

**Cloning with Submodules**

Clone repo WITH submodules
`git clone --recursive <repo-url>`
If you already cloned without --recursive
`git submodule update --init --recursive`

**Updating Submodules**

```
// Pull latest changes in submodules (based on the commit the parent repo tracks)
$ git submodule update --remote --merge

// Update ALL submodules to the commit defined in parent repo
$ git submodule update --recursive --remote
```

**Changing Submodule Commit**
Inside the submodule directory:

```
$ cd packages/library
$ git checkout main
$ git pull origin main
```

Then commit the new submodule pointer in the parent repo:

```
$ cd ../..
$ git add packages/library
$ git commit -m "Update library submodule to latest main"

```

**Adding a New Submodule**

```
$ git submodule add <repo-url> packages/<name>
$ git commit -m "Add <name> submodule"

```

**Removing a Submodule**

```
$ git submodule deinit -f packages/<name>
$ rm -rf .git/modules/packages/<name>
$ git rm -f packages/<name>

```

**Team Workflow Guidelines**
. Always clone with --recursive (or run git submodule update --init).
. Treat submodules like locked dependencies: the parent repo tracks a specific commit of the submodule.

To update a submodule:
. Go into the submodule and pull the new commit.
. Return to the parent repo and commit the updated pointer.

Pipelines/CI need to fetch submodules:

```
// yaml
steps:
- checkout: self
  submodules: true

```

With these commands, your team won't get tripped up by detached HEAD states or missing submodule content.
