
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
npx nx serve customer-app
```

Same statement for 

- serve
- build
- test
- lint

## Folder structure

### Nx Reference Folder Structure

<https://github.com/code-star/nx-reference>

### Others

Josh Morony: This is what your Ionic app looks like on Nx (not much about Ionic in it): <https://www.youtube.com/watch?v=s37Gu4z878I>

- apps\<my-app>\...
- libs\[web, mobile]\[shell, home, ...], [feature, ui, data-access, utility, auth, shared-ui, ...]
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

- Install Nx Console from nrwl

## NgRx

Install ngrx schematics: <https://nx.dev/recipes/other/misc-ngrx>

- Open Nx Console (extension)
- select `Generate`
- filter for ngrx
- generate what you need

!!!!!!!!!!!!! NgRx/Schematics does not support Angular Standard Components (29.12.2022) !!!!!!!!!!! todo

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
