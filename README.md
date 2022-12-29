
# Nx

## Intro

- <https://nx.dev/getting-started/intro>
- Monorepo: <https://nx.dev/more-concepts/why-monorepos>
- Starter App (standalone app, optional): <https://nx.dev/getting-started/angular-standalone-tutorial>

### Know more about Nx:

1. Josh Morony: An Introduction to Nx Workspaces - Understanding Nx & Monorepos #1: <https://www.youtube.com/watch?v=QqM3MlyurUA>
2. Josh Morony: This is what your Ionic app looks like on Nx (Folder structure, not much about Ionic in it): <https://www.youtube.com/watch?v=s37Gu4z878I>
3. Josh Morony: How Nx and Monorepos just made my life 10x better: <https://www.youtube.com/watch?v=1eHlaVoeDfU>
4. Josh Morony: Architecting a (boring) real life project with Nx: <https://www.youtube.com/watch?v=XBaGOKtaEvM>

## Install Nx

This statement installs nx and starts a wizard:

```cmd
npx create-nx-workspace@latest
```

Wizard questions:

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

This statement installs nx and starts a wizard: (say no to distributed caching - paid option)

```cmd
npx create-nx-workspace myapp --preset=empty
```

An Introduction to Nx Workspaces - Understanding Nx & Monorepos: <https://www.youtube.com/watch?v=QqM3MlyurUA>

- 1 package.json for all apps in the monorepo: 
  - all apps have the same versions (angular, libraries)
  - update it often (easy, you use the same workspace for all your apps)
  - a set of well defined test is essential

### Install nx Angular

Docs: <https://nx.dev/packages/angular>

```cmd
cd myapp
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
npx nx serve customer-app
```

Same statement for 

- serve
- build
- test
- lint

## Folder structure

Josh Morony: This is what your Ionic app looks like on Nx (not much about Ionic in it):<https://www.youtube.com/watch?v=s37Gu4z878I>

- apps\<my-app>\...
- libs\[web, mobile]\[shell, home, ...], [feature, ui, data-access, utility]

- descriptions:
  - feature: smart-components
  - ui: dumb components
  - data-access: services, ngrx files
  - utility: helper functions
- every app should have a shell: libs\[web, mobile]\shell
  - create it using nx. (creates file libs\mobile\shell\feature\src\lib\mobile-shell.module.ts) 
  - gets stuff from apps\<my-app>\...
    - apps\<my-app>\src\app\app.routing.module.ts --> libs\mobile\shell\feature\src\lib\mobile-shell-routing.module.ts
    - apps\<my-app>\src\app\app.module.ts --> libs\mobile\shell\feature\src\lib\mobile-shell.module.ts

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
