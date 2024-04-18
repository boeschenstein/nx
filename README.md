
# Nx

## Intro

- <https://nx.dev/getting-started/intro>
- Monorepo: <https://nx.dev/more-concepts/why-monorepos>
- Starter App (standalone app, optional): <https://nx.dev/getting-started/angular-standalone-tutorial>

### Monorepo

- Stores multiple, or even all, of your apps in one repository
- Nx makes creating/managing this monorepo easier
- Code shared between multiple apps in the repo can be abstracted into libraries
- All of the dependencies for all of the apps *can* be managed and updated from a single package.json file

### Know more about Nx:

1. Josh Morony: An Introduction to Nx Workspaces - Understanding Nx & Monorepos #1: <https://www.youtube.com/watch?v=QqM3MlyurUA>
2. Josh Morony: This is what your Ionic app looks like on Nx: <https://www.youtube.com/watch?v=s37Gu4z878I>
  - Folder structure, not much Ionic ionic specific stuff
3. Josh Morony: How Nx and Monorepos just made my life 10x better: <https://www.youtube.com/watch?v=1eHlaVoeDfU>
  - real live examples of nx mono repo
4. Josh Morony: Architecting a (boring) real life project with Nx: <https://www.youtube.com/watch?v=XBaGOKtaEvM>

## Install Nx

This statement installs nx and starts a wizard:

```cmd
npx create-nx-workspace@latest
```

Wizard questions: 

>The wizard does not ask for standalone component (no module) atm, nx v15.4.2)

```bash
Need to install the following packages:
  create-nx-workspace@15.3.2
Ok to proceed? (y) y

 >  NX   Let's create a new workspace [https://nx.dev/getting-started/intro]

√ Choose what to create                 · angular
√ Application name                      · demoApp
√ Default stylesheet format             · scss
√ Enable distributed caching to make your CI faster · No
```

## Install Workspace and app (no federation)

A Nx workspace is a monorepo. Google has thousands of apps in a single monorepo.

### Generate workspace manually

Read about monorepo: (in theory) you need only 1 monorepo in your company. So the repo name yould be your company name.

The follwoing statement installs nx and starts the installation: (say no to distributed caching (--nxCloud=false) if you want to reject the paid option)

```cmd
npx create-nx-workspace demo --preset=empty --nxCloud=false --skipGit
```

An Introduction to Nx Workspaces - Understanding Nx & Monorepos: <https://www.youtube.com/watch?v=QqM3MlyurUA>

- 1 package.json for all apps in the monorepo: 
  - all apps have the same versions (angular, libraries)
  - update it often (easy, you use the same workspace for all your apps)
  - a set of well defined test is essential

### Install nx Angular

Docs: <https://nx.dev/packages/angular>

```cmd
cd demo
npm install --save-dev @nrwl/angular
```

### Generate Apps

>Use `--standalone` for new Angular Standalone Components (eliminate module, from Angular v15)

```cmd
npx nx generate @nrwl/angular:application --standalone --routing=true --style=scss adminApp
npx nx generate @nrwl/angular:application --standalone --routing=true --style=scss customerAppTester
```

### Use CLI

```cmd
nx serve appName
nx build appName
nx test appName
nx lint appName
nx e2e appName
```

### Add a library project (examples)

Docs: 

- <https://nx.dev/more-concepts/applications-and-libraries>
- <https://nx.dev/more-concepts/creating-libraries>
- <https://nx.dev/more-concepts/library-types>

Comments:

- Use libraries for cleaner code - not for reuse in the first place
- Mental Model: place 80% of your logic into the `\libs` folder and 20% into `\apps`

```cmd
npx nx g @nrwl/angular:lib shared/ui         --standalone --style=scss --routing=true --lazy --parent=apps\admin-app\src\app\app.routes.ts
npx nx g @nrwl/angular:lib shared/dataAccess --standalone --style=scss
npx nx g @nrwl/angular:lib shared/utilMath   --standalone --style=scss --buildable --publishable
```

### Add a component

```cmd
npx nx g @nrwl/angular:component MyAppTest --standalone --project=admin-app
npx nx g @nrwl/angular:component MyLibTest --standalone --project=shared-ui
```

### Test the apps

>Add -o to open browser

```cmd
npx nx s admin-app
npx nx s customer-app-tester
```

## Federation

> Separate app into small modules for faster builds

docs: 
- Federationn: <https://nx.dev/recipes/module-federation/faster-builds>

The next statement installs nx and starts a wizard to create an empty workspace:

```cmd
npx create-nx-workspace myapp --preset=empty
```

Wizard questions: disable paid version (distributed caching)

```bash
√ Enable distributed caching to make your CI faster · No
```

Install nx Angular

```cmd
npm install --save-dev @nrwl/angular
```

```cmd
cd myapp

npx nx g @nrwl/angular:host adminApp --remotes=about,customerApp
npx nx g @nrwl/angular:host customerAppTester
```

Manually adopt (TODO: doing 2. + 3. the app will fail: why ???)

1. `module-federation.config.js` (add missing routes in customerAppTester)
2. `app.component.html` (add missing route to about)
3. `app.routes.ts` (add missing route to about)

Run both apps and check routes (upper left corner):

```cmd
npx nx serve admin-app
npx nx s -o customer-app
```

Same statement for 

- serve
- build
- test
- lint

## Folder structure

### Nx Reference Folder Structure

<https://github.com/code-star/nx-reference>

### Best to start

Josh Morony: This is what your Ionic app looks like on Nx (not much about Ionic in it): <https://www.youtube.com/watch?v=s37Gu4z878I>

- this is not really Ionic specific
- you learn 
  - how to refactor Router-Module (lazy) and Lib-Module out from app to lib
  - how to structure app and libs in nx
  - use of generator
- apps\<my-app>\...
- libs\[web, mobile]\[shell, home, domain-a[\feature-a,...], domain-b, ...], [feature, ui, data-access, utility, auth, shared-ui, ...]
- some descriptions:
  - feature: smart-components
  - ui: dumb components
  - data-access: services, ngrx files
  - utility: helper functions
- every app should have a shell: libs\[web, mobile]\shell
  - create it using nx. (creates file libs\mobile\shell\feature\src\lib\mobile-shell.module.ts) 
  - gets stuff from apps\<my-app>\...
    - apps\<my-app>\src\app\app.routing.module.ts --> libs\mobile\shell\feature\src\lib\mobile-shell-routing.module.ts
    - apps\<my-app>\src\app\app.module.ts --> libs\mobile\shell\feature\src\lib\mobile-shell.module.ts

## Extension

VS Code: Install `Nx Console` from nrwl

## Nx Console: Generate code

> NgRx/Schematics + nx ngrx schematics: both do not support Angular Standard Components (29.12.2022)

My Feature Request: <https://github.com/nrwl/nx-console/issues/1460>

### Generate code: Angular

Learn NgRx using Pluralsight: Angular NgRx: Getting Started

- by Deborah Kurata and Duncan Hunter
- <https://app.pluralsight.com/library/courses/angular-ngrx-getting-started>

Good lesson to learn Nx: Create an Angular App from scratch: create APM-Demo0 app yourself, using Nx and Nx Angular Schematics

App to create: APM-Demo0 from <https://github.com/DeborahK/Angular-NgRx-GettingStarted>

Demo app explained: <https://app.pluralsight.com/course-player?clipId=79e32924-ae98-4416-adf4-45514ae90ec6>

Steps:

- Create a new Nx Workspace or use an existing Nx Workspace
- Open Nx Console (VS Code extension)
- Select `generate` to generate a new app
- Select `generate` to generate new components (menu, page-not-found, shell, welcome)
- Select `generate` to generate new module: products
  - Select `generate` to generate new components within this module (product-edit, product-list, product-shell)
  - add input fields on product-edit
  - add buttons like in the example
- Select `generate` to generate new modules: user
  - Select `generate` to generate new components within this module (login)
  - add email and password input fields
  - add login and cancel button, make them work

### Generate code: NgRx

Install ngrx schematics: <https://nx.dev/recipes/other/misc-ngrx>

`nx g @nrwl/angular:ngrx <featurename> --module=<path-to-module> --no-interactive [options]`

Install root store

`nx g @nrwl/angular:ngrx app --module=apps/<appname>/src/app/app.module.ts --root`

Use Generators:

- Open Nx Console (VS Code extension)
- Select `generate`
  - even better: right-click on target folder and press `Nx generate...` (helpful in some situations)
- Option a) ngrx schematics (module not updated, not so rich files generated)
  - filter for ngrx
  - generate what you need ('feature' for complete set of files)
- Option b) Nx ngrx schematics (module updated, richer files generated)
  - filter for ngrx: select `@nrwl/angular - ngrx Add an ngrx config to a project`)

>`Feature`: Deborah Kurata: Angular apps are often organized in feature modules, it makes sence to layout the state by feature as well:
minute 1:30: <https://app.pluralsight.com/course-player?clipId=64a235e1-b03a-4a57-ac05-326fc24fce75>

APM structure, separated in features (sometimes called slices), called "Feature Module State Composition"

>Use `Feature` to separate store into separate slices. This is called "Feature Module State Composition"

- store (root, empty: has no redurcers)
  - app feature
  - user feature
  - products feature
    - products-list
    - products-data
  - customers feature

Steps:

- generate empty root store (without any reducers):
    `npx nx generate @nrwl/angular:ngrx needed-but-not-used --module=apps/apm/src/app/app.module.ts --directory=apps/apm/src/app --barrels --facade --root --no-interactive`
- generate feature store for product-list-form: (different idea than PluralSight: @ngrx/schematics:store does not use ngrx\entity)
  `npx nx generate @ngrx/schematics:store productListForm --module=products/product.module.ts --project=apm --skipTests --no-interactive`
WIP  
- generate feature store for product list (@nrwl/angular:ngrx is using ngrx\entity)
  `npx nx generate @nrwl/angular:ngrx productListForm --module=apps/apm/src/app/products/product.module.ts --barrels --facade --no-interactive `

Check result

- install dev tools: <https://ngrx.io/guide/store-devtools>
- run app, toggle checkbox, check data in dev tools

## Information

- Publishable: <https://nx.dev/more-concepts/buildable-and-publishable-libraries#publishable-libraries>
- Buildable: <https://nx.dev/more-concepts/buildable-and-publishable-libraries#buildable-libraries>

### Standalone component (no modules, new in Angular v15)

Source: <https://www.youtube.com/watch?v=uY_Cy_wUmQI>

### Micro Frontend

<https://nx.dev/more-concepts/micro-frontend-architecture>

### Project Graph

<https://nx.dev/angular-tutorial/2-project-graph>

### Package-based vs Integrated

- package-based: no cli (optional: manually add nx.json)
- integrated: use cli
- <https://nx.dev/concepts/integrated-vs-package-based>

## Upgrade Nx

- Upgrade node/npm to latest LTS version
- `npm upgrade -g`

Check installation so far: Both must run without any errors:

- `npm i -f`
- `npm ci`

Upgrade Nx (workplace only?):

- Details: <https://nx.dev/core-features/automate-updating-dependencies>
- `npx nx migrate latest`
  - old:! `npx nx migrate @nrwl/workspace@latest`
- `npm i -f`
- `npm ci`
- if a migration file has been created, run it now: `nx migrate --run-migrations` and delete it (you do not need it anymore)
- Check if Update did succeed:

- `npm i -f`
- `npm ci`

Upgrade Nx (workplace and angular):

- `npx nx@latest migrate latest`
- for cypress and if needed in company: SET HTTP_PROXY=<your_company_proxy>
- `npm i -f`
- `npm ci  --legacy-peer-deps` // there was no other way
- if a migration file has been created, run it now: `nx migrate --run-migrations` and delete it (you do not need it anymore)
- Check if Update did succeed:

- `npm i -f`
- `npm ci`

New Upgrade features: <https://www.youtube.com/watch?v=AQV4WFldwlY>
- How to update Angular within Nx
- Upgrade Nx without upgrading Angular or Typescript

## Schematics vs Generators

- in Nx, `Schematics` is called `Generators`
- if in doubt, use `ngrx` generators
  - Nrwl (preferred): `@nrwl/angular:library` [description](https://github.com/nrwl/nx/blob/master/packages/angular/src/generators/library/schema.json)
  - Angular: `@schematics/angular:library` [description](https://github.com/angular/angular-cli/blob/main/packages/schematics/angular/library/schema.json)
  - old:`@nrwl/workspace` [description](https://nx.dev/recipes/adopting-nx/migration-angular#transforming-an-angular-cli-workspace-to-an-integrated-nx-monorepo)
  
