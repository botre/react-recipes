# React recipes

## Parcel 1 + TypeScript

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
npm install --save react react-dom
```

```bash
npm install --save-dev @types/react @types/react-dom bundlesize http-server-spa parcel-bundler parcel-plugin-static-files-copy typescript
```

/static/robots.txt

```text
User-agent: *
Disallow: /
```

build.sh

```bash
#!/bin/bash

set -e

BUILD_DIRECTORY="dist"

rm -rf $BUILD_DIRECTORY
NODE_ENV="production" node_modules/.bin/parcel build src/index.html --detailed-report
node_modules/.bin/bundlesize
```

dev.sh

```bash
#!/bin/bash

set -e

NODE_ENV="development" node_modules/.bin/parcel src/index.html --no-autoinstall
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
  "version": "1.0.0",
  "scripts": {
    "build": "sh scripts/build.sh",
    "dev": "sh scripts/dev.sh",
    "serve": "sh scripts/serve.sh",
    "typecheck": "sh scripts/typecheck.sh"
  },
  "bundlesize": [
    {
      "path": "./dist/main.*.js",
      "maxSize": "300kB"
    }
  ],
  "browserslist": ["defaults"]
}
```

src/index.html

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
  </head>
  <body>
    <div id="root"></div>
    <script src="main.tsx"></script>
  </body>
</html>
```

src/main.tsx

```tsx
import React from "react";
import ReactDOM from "react-dom";
ReactDOM.render(
  <React.StrictMode>
    <div>Hello, World!</div>{" "}
  </React.StrictMode>,
  document.getElementById("root")
);
```
