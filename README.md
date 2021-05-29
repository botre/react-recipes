# React recipes

```bash
mkdir src
mkdir static
```

tsconfig.json

```json
{
  "compilerOptions": {
    "esModuleInterop": true,
    "jsx": "react",
    "lib": ["es2020", "dom"],
    "module": "es2020",
    "moduleResolution": "node",
    "noImplicitAny": true,
    "noImplicitThis": true,
    "resolveJsonModule": true,
    "skipLibCheck": true,
    "sourceMap": true,
    "strictBindCallApply": true,
    "strictNullChecks": true
  },
  "include": ["src/**/*", "__tests__/**/*"]
}
```

```bash
npm install react react-dom
```

```bash
npm install --save-dev http-server-spa @types/react @types/react-dom typescript
```

/src/main.tsx

```tsx
import React from "react";
import ReactDOM from "react-dom";
ReactDOM.render(
  <React.StrictMode>
    <div>Hello, World!</div>
  </React.StrictMode>,
  document.getElementById("root")
);
```

/static/robots.txt

```text
User-agent: *
Disallow: /
```

serve.sh

```bash
#!/bin/bash

set -e

node_modules/.bin/http-server-spa dist
```

typecheck.sh

```bash
#!/bin/bash

set -e

node_modules/.bin/tsc --noEmit -p tsconfig.json
```

package.json

```json
{
  "bundlesize": [
    {
      "path": "./dist/main.*.js",
      "maxSize": "300kB"
    }
  ],
  "browserslist": ["defaults"]
}
```

## Parcel 2

```bashdev bundlesize
npm install --save-dev parcel@nightly parcel-reporter-static-files-copy
```

.parcelrc

```json
{
  "extends": ["@parcel/config-default"],
  "reporters": ["parcel-reporter-static-files-copy"]
}
```

build.sh

```bash
#!/bin/bash

set -e

BUILD_DIRECTORY="dist"

rm -rf $BUILD_DIRECTORY
export NODE_ENV="production"
node_modules/.bin/parcel build src/index.html --detailed-report
node_modules/.bin/bundlesize
```

dev.sh

```bash
#!/bin/bash

set -e

export NODE_ENV="development"
node_modules/.bin/parcel serve src/index.html --no-autoinstall
```

/src/index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="main.tsx"></script>
  </body>
</html>
```

## Tailwind 2 (PostCSS)

```bash
npm install @tailwindcss/aspect-ratio @tailwindcss/forms @tailwindcss/typography tailwindcss
```

```
npm install --save-dev @types/tailwindcss
```

```bash
npm install --save-dev postcss autoprefixer
```

postcss.config.js

```js
module.exports = {
  plugins: [require("tailwindcss"), require("autoprefixer")],
};
```

tailwind.config.js

```js
module.exports = {
  plugins: [
    require("@tailwindcss/aspect-ratio"),
    require("@tailwindcss/forms"),
    require("@tailwindcss/typography"),
  ],
  purge: {
    content: ["./src/**/*.tsx"],
  },
};
```

/src/index.css

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

```tsx
import "./index.css";
```

## React Router 5

```bash
npm install query-string react-router-dom use-query-params
```

```bash
npm install --save-dev @types/react-router-dom
```

```tsx
import React, { lazy, Suspense } from "react";
import { BrowserRouter as Router, Route, Switch } from "react-router-dom";
import { QueryParamProvider } from "use-query-params";

const HomeScreen = lazy(() => import("./screens/HomeScreen"));
const EditScreen = lazy(() => import("./screens/EditScreen"));

const Routes = () => (
  <Suspense fallback={<div>Loading...</div>}>
    <Router>
      <QueryParamProvider ReactRouterRoute={Route}>
        <Switch>
          <Route key="/" path={"/"} exact>
            <HomeScreen />
          </Route>
          <Route key="/edit/:id" path={"/edit/:id"}>
            <EditScreen />
          </Route>
        </Switch>
      </QueryParamProvider>
    </Router>
  </Suspense>
);

export default Routes;
```

## Apollo 3

```bash
npm install @apollo/client graphql
```

```tsx
import { ApolloClient, createHttpLink, InMemoryCache } from "@apollo/client";
import { ApolloProvider } from "@apollo/client/react";

const httpLink = createHttpLink({
  uri: `${process.env.APP_BACKEND_URI}/graphql`,
});

const apolloClient = new ApolloClient({
  link: httpLink,
  cache: new InMemoryCache(),
});

const Component = () => <ApolloProvider client={apolloClient}></ApolloProvider>;
```

## GraphQL codegen (React Apollo)

```bash
npm install --save-dev @graphql-codegen/cli @graphql-codegen/typescript @graphql-codegen/typescript-operations @graphql-codegen/typescript-react-apollo @graphql-codegen/typescript-resolvers
```

codegen.sh

```bash
#!/bin/bash

set -e

export NODE_ENV="production"
node_modules/.bin/graphql-codegen
```

codegen.yml

```yml
schema: ../backend/schema.graphql
overwrite: true
generates:
  .codegen/gql.codegen.tsx:
    plugins:
      - typescript
      - typescript-operations
      - typescript-resolvers
      - typescript-react-apollo
    documents:
      - src/**/*.{ts,tsx}
    config:
      withHooks: true
      withComponent: false
      withHOC: false
      maybeValue: T | undefined
      namingConvention:
        enumValues: keep
```
