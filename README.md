
## NX

### Intro

- <https://nx.dev/getting-started/intro>
- Monorepo: <https://nx.dev/more-concepts/why-monorepos>
- Starter App (standalone app, optional): <https://nx.dev/getting-started/angular-standalone-tutorial>

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

### Generate app manually (no federation)

This statement installs nx and starts a wizard: (say no to distributed caching - paid option)

```cmd
npx create-nx-workspace myapp --preset=empty
```

Install nx Angular

Docs: <https://nx.dev/packages/angular>

```cmd
cd myapp
npm install --save-dev @nrwl/angular
```

Use CLI

>Install @nrwl/angular globally or add 'npx' at the beginning

```cmd
nx serve appName
nx build appName
nx test appName
nx lint appName
nx e2e appName
```

Generate Apps

```cmd
npx nx generate @nrwl/angular:application adminApp --style=scss
npx nx generate @nrwl/angular:application customerAppTester --style=scss
```

Add a library project (examples)

Docs: 

- <https://nx.dev/more-concepts/applications-and-libraries>
- <https://nx.dev/more-concepts/creating-libraries>
- <https://nx.dev/more-concepts/library-types>

Comments:

- Use libraries for cleaner code - not for reuse in the first place
- Mental Model: place 80% of your logic into the '\libs' folder and 20% into '\apps'

```cmd
npx nx g @nrwl/angular:lib shared/ui --buildable
npx nx g @nrwl/angular:lib shared/dataAccess --buildable
npx nx g @nrwl/angular:lib shared/utilMath --buildable
```

Test the apps

>add -o to open browser

```cmd
npx nx s admin-app
npx nx s customer-app-tester
```

### Federation

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

1. 'module-federation.config.js' (add missing routes in customerAppTester)
2. 'app.component.html' (add missing route to about)
3. 'app.routes.ts' (add missing route to about)

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

### Project Graph

<https://nx.dev/angular-tutorial/2-project-graph>

### Package-based vs Integrated

- package-based: no cli (optional: manually add nx.json)
- integrated: use cli
- <https://nx.dev/concepts/integrated-vs-package-based>

### Micro Frontend

<https://nx.dev/more-concepts/micro-frontend-architecture>

