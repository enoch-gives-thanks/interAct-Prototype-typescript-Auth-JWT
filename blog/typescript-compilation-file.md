# 1. Typescript compilation

- [1. Typescript compilation](#1-typescript-compilation)
- [2. "module": "NodeNext"](#2-module-nodenext)
- [3. "moduleResolution": "nodenext"](#3-moduleresolution-nodenext)
  - [3.1. **Solution**](#31-solution)
  - [3.2. Corrected `tsconfig.json`](#32-corrected-tsconfigjson)
  - [3.3. **Why This Happens**](#33-why-this-happens)
  - [3.4. **What Do These Options Mean?**](#34-what-do-these-options-mean)
  - [3.5. **Key Takeaway**](#35-key-takeaway)
- [4. "baseUrl": "src"](#4-baseurl-src)
- [5. "outDir": "dist"](#5-outdir-dist)
- [6. "sourceMap": true](#6-sourcemap-true)
- [7. "noImplicitAny": true](#7-noimplicitany-true)
- [8. "include": \["src/\*\*/\*"\]](#8-include-src)
- [9. The Full File, Explained](#9-the-full-file-explained)
- [10. What Happens During Compilation?](#10-what-happens-during-compilation)
- [11. Conclusion](#11-conclusion)

# 2. "module": "NodeNext"

  Specifies the module system to be used during compilation.
  NodeNext:
  Introduced in TypeScript 4.7, it aligns with Node.js's native ESM and CommonJS module support.
  It allows TypeScript to handle .js, .ts, .mjs, and .cjs files as modules according to the
  conditions in the package.json file (e.g., "type": "module" for ESM or "type": "commonjs" for
  CommonJS).

# 3. "moduleResolution": "nodenext"

  Specifies how TypeScript should resolve module imports (e.g., import or require).
  node:
  It uses Node.js's module resolution strategy:
  Looks for modules in node_modules.
  Resolves relative imports (./, ../) and absolute imports based on the baseUrl.
  Handles extensions like .js, .ts, .json, etc.
  This error occurs because when you set the **`module`** option to `"NodeNext"`, the
  **`moduleResolution`** option must also be either `"NodeNext"` (or left unspecified, which
  defaults to `"NodeNext"` when `module` is `"NodeNext"`).

  originally in your configuration, you likely have this:

  ```json
  {
    "compilerOptions": {
      "module": "NodeNext",
      "moduleResolution": "node", // This is incorrect when module is NodeNext
      ...
    }
  }
  ```

## 3.1. **Solution**

  To fix this error, change the `moduleResolution` option to `"NodeNext"`, or remove it entirely so
  TypeScript can infer it automatically.

## 3.2. Corrected `tsconfig.json`

  ```json
  {
    "compilerOptions": {
      "module": "NodeNext",
      "moduleResolution": "NodeNext", // Correct value
      "baseUrl": "src",
      "outDir": "dist",
      "sourceMap": true,
      "noImplicitAny": true
    },
    "include": ["src/**/*"]
  }
  ```

  Or, simply omit the `moduleResolution` property:

  ```json
  {
    "compilerOptions": {
      "module": "NodeNext",
      "baseUrl": "src",
      "outDir": "dist",
      "sourceMap": true,
      "noImplicitAny": true
    },
    "include": ["src/**/*"]
  }
  ```

  ---

## 3.3. **Why This Happens**

- The **`"NodeNext"`** module system uses Node.js's **ESM and CommonJS interoperability**.
- When `"module": "NodeNext"` is set, TypeScript requires **`moduleResolution`** to match the same
  logic, i.e., `"NodeNext"`.
- If you set `"moduleResolution": "node"`, it conflicts because `"node"` uses an older resolution
  strategy incompatible with the `"NodeNext"` module format.

  ---

## 3.4. **What Do These Options Mean?**

  1. **`module: "NodeNext"`**:
     - Indicates that TypeScript should treat files as **ES modules (ESM)** or **CommonJS** based on
     file extensions:
       - `.mjs` and `.mts`: Always treated as ESM.
       - `.cjs` and `.cts`: Always treated as CommonJS.
       - `.js` and `.ts`: Treated as ESM or CommonJS depending on the `"type"` field in
       `package.json`.

  2. **`moduleResolution: "NodeNext"`**:
     - Uses Node.js’s modern resolution strategy for ESM and CommonJS.
     - Resolves imports based on the file extensions and `package.json` settings (like `"exports"`
     and `"type"`).

  ---

## 3.5. **Key Takeaway**

  When using `"module": "NodeNext"`, always use `"moduleResolution": "NodeNext"`, or leave it
  unspecified so TypeScript can infer the correct resolution strategy.

# 4. "baseUrl": "src"

  Sets the base directory for resolving non-relative module imports.
  "src":
  This means any non-relative import (e.g., import myModule from "utils";) will be resolved relative
  to the src folder.
  Simplifies imports by avoiding long relative paths like ../../../utils.

# 5. "outDir": "dist"

  Specifies the output directory where the compiled JavaScript files will be placed.
  "dist":
  After compilation, the .js files and other outputs will be stored in the dist folder.
  For example:
  src/index.ts → dist/index.js.

# 6. "sourceMap": true

  Generates source maps for your TypeScript files.
  Source Maps:
  These files map the compiled JavaScript code back to the original TypeScript code.
  Useful for debugging, as they allow you to view and debug the original TypeScript code in tools
  like Chrome DevTools.

# 7. "noImplicitAny": true

  Ensures that all variables, parameters, and return types must have an explicit type, and
  TypeScript will not infer the any type implicitly.
  Example:

- With "noImplicitAny": true:

  ```ts
  function add(a, b) {
    return a + b; // Error: Parameter 'a' implicitly has an 'any' type.
  }
  ```

  Without "noImplicitAny": true:

  ```ts
  function add(a, b) {
    return a + b; // No error, but less strict.
  }
  ```

  include
  The include option specifies which files or directories should be included in the compilation.

# 8. "include": ["src/**/*"]

  "src/**/*":
  Tells TypeScript to include all files and folders inside the src directory (recursively).
  The**/*pattern means:
  **: Match all subfolders.
  *: Match all file names.
  Only files with supported extensions (e.g., .ts, .tsx) will be compiled.

# 9. The Full File, Explained

  ```json
  {
      "compilerOptions": {
          "module": "NodeNext",                // Uses Node.js's ESM/CommonJS module system.
          "moduleResolution": "node",         // Resolves modules using Node.js's strategy.
          "baseUrl": "src",                   // Non-relative imports are resolved relative to 'src'.
          "outDir": "dist",                   // Compiled JavaScript files are output to 'dist'.
          "sourceMap": true,                  // Generates source maps for debugging.
          "noImplicitAny": true               // Enforces explicit typing, disallowing implicit 'any'.
      },
      "include": ["src/**/*"]                 // Includes all files in 'src' for compilation.
  }
  ```

# 10. What Happens During Compilation?

  Input:
  TypeScript looks for all .ts files inside the src folder (due to include).
  Compilation:
  Compiles the TypeScript files according to the compilerOptions.
  Adheres to strict typing rules (e.g., noImplicitAny).
  Resolves modules with NodeNext rules and uses src as the base for imports.
  Output:
  Outputs the compiled .js files (and .map files if sourceMap is true) into the dist folder.
  Why Use These Settings?
  module: NodeNext and moduleResolution: node:
  Useful for projects targeting modern Node.js versions that support both ESM and CommonJS.
  Allows you to interoperate with Node.js's native module system and package.json settings.
  baseUrl: src:
  Simplifies imports by avoiding long relative paths.
  outDir: dist:
  Keeps compiled JavaScript files separate from source TypeScript files.
  sourceMap: true:
  Enables easier debugging in modern browsers or development tools.
  noImplicitAny: true:
  Ensures stricter typing, reducing bugs caused by implicit any types.
  include: ["src/**/*"]:
  Focuses the compiler on the src folder, ensuring that only the source files are compiled.
  What Else Can You Add?
  You can further customize the tsconfig.json by adding options like:

  target: Specify the target JavaScript version (e.g., ES2015, ES2020).
  strict: Enables a suite of strict flags, including noImplicitAny.
  paths: Define custom module paths for more flexible imports.
  lib: Specify which library features to include (e.g., ES2017, DOM).
  Example:

  ```json
  {
      "compilerOptions": {
          "target": "ES2020",
          "strict": true,
          "lib": ["ES2020", "DOM"]
      }
  }
  ```

# 11. Conclusion

  This configuration is tailored for a Node.js project using modern module systems (ESM and
  CommonJS). It enforces strict typing, organizes output files, and simplifies imports, making it a
  robust setup for a TypeScript project.
