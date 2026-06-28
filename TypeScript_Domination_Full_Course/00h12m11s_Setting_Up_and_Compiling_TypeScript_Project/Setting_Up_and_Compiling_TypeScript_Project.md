# Setting Up and Compiling a TypeScript Project

## Initializing a TypeScript Project

To set up a TypeScript project, you need a `tsconfig.json` file. This file configures how the TypeScript compiler behaves.

### Generate tsconfig.json

Run the following command in your project root:

```bash
tsc --init
```

or with npx:

```bash
npx tsc --init
```

This creates a `tsconfig.json` file with default settings and many commented options.

## Understanding tsconfig.json

The `tsconfig.json` file is the heart of a TypeScript project. Key options include:

```json
{
    "compilerOptions": {
        "target": "ES2016",           // ECMAScript target version
        "module": "commonjs",         // Module system
        "outDir": "./dist",           // Output directory for compiled JS
        "rootDir": "./src",           // Root directory of input TS files
        "strict": true,               // Enable all strict type-checking options
        "esModuleInterop": true,      // Better compatibility with CommonJS modules
        "skipLibCheck": true,         // Skip type checking of declaration files
        "forceConsistentCasingInFileNames": true
    },
    "include": ["src/**/*"],          // Files to include
    "exclude": ["node_modules"]       // Files to exclude
}
```

## Compiling the Project

Once `tsconfig.json` is configured, compile using:

```bash
# Compile once
tsc

# Compile in watch mode (recompile on every change)
tsc --watch
```

### Project Structure Example

```
my-ts-project/
  src/
    index.ts
    utils/
      helper.ts
  dist/
    index.js
    utils/
      helper.js
  tsconfig.json
  package.json
```

## Expanded Examples

### Example 1: Full Project Setup Script

```bash
# Create project structure
mkdir my-typescript-project
cd my-typescript-project

# Initialize npm
npm init -y

# Create source directory
mkdir src

# Generate tsconfig.json
npx tsc --init

# Install TypeScript locally
npm install --save-dev typescript

# Create a simple TypeScript file
cat > src/index.ts << 'EOF'
function greet(name: string): string {
    return `Hello, ${name}!`;
}

const message = greet("TypeScript Developer");
console.log(message);
EOF

# Compile
npx tsc

# Run the compiled JavaScript
node dist/index.js
# Output: Hello, TypeScript Developer!
```

### Example 2: Detailed tsconfig.json Configuration

```json
{
    "compilerOptions": {
        "target": "ES2020",
        "module": "ESNext",
        "moduleResolution": "node",
        "lib": ["ES2020", "DOM", "DOM.Iterable"],
        "outDir": "./dist",
        "rootDir": "./src",
        "strict": true,
        "esModuleInterop": true,
        "skipLibCheck": true,
        "forceConsistentCasingInFileNames": true,
        "resolveJsonModule": true,
        "declaration": true,
        "declarationMap": true,
        "sourceMap": true,
        "removeComments": false,
        "noUnusedLocals": true,
        "noUnusedParameters": true,
        "noImplicitReturns": true,
        "noFallthroughCasesInSwitch": true
    },
    "include": ["src"],
    "exclude": ["node_modules", "dist", "**/*.test.ts"]
}
```

### Example 3: Multiple Output Targets

```json
// tsconfig.cjs.json -- CommonJS output
{
    "extends": "./tsconfig.json",
    "compilerOptions": {
        "module": "CommonJS",
        "outDir": "./dist/cjs"
    }
}

// tsconfig.esm.json -- ES Module output
{
    "extends": "./tsconfig.json",
    "compilerOptions": {
        "module": "ESNext",
        "outDir": "./dist/esm"
    }
}
```

Build both:

```bash
tsc --project tsconfig.cjs.json
tsc --project tsconfig.esm.json
```

### Example 4: Understanding outDir and rootDir

```typescript
// src/index.ts
console.log("Hello from index");

// src/utils/helper.ts
export const helper = "I am a helper";
```

With `rootDir: "./src"` and `outDir: "./dist"`:

```
dist/
  index.js
  utils/
    helper.js
```

The directory structure under `src/` is mirrored in `dist/`.

### Example 5: Strict Mode Enablement

`strict: true` enables these individual checks:

```json
{
    "compilerOptions": {
        "strictNullChecks": true,
        "strictFunctionTypes": true,
        "strictBindCallApply": true,
        "strictPropertyInitialization": true,
        "noImplicitAny": true,
        "noImplicitThis": true,
        "alwaysStrict": true
    }
}
```

### Example 6: Watch Mode in Action

```bash
# Start watch mode
npx tsc --watch
# [12:00:00] Starting compilation in watch mode...
# [12:00:00] Found 0 errors. Watching for file changes.

# In another terminal, edit a file:
echo 'console.log("Updated!");' >> src/index.ts

# Watch mode detects the change:
# [12:01:15] File change detected. Starting incremental compilation...
# [12:01:15] Found 0 errors. Watching for file changes...

# dist/index.js is automatically updated
```

### Example 7: Excluding Test Files from Compilation

```json
{
    "compilerOptions": {
        "outDir": "./dist"
    },
    "include": ["src"],
    "exclude": [
        "node_modules",
        "dist",
        "**/*.test.ts",
        "**/*.spec.ts",
        "src/__tests__"
    ]
}
```

### Example 8: Composite Project Setup (Project References)

```json
// tsconfig.json (root)
{
    "references": [
        { "path": "./packages/core" },
        { "path": "./packages/utils" }
    ]
}

// packages/core/tsconfig.json
{
    "compilerOptions": {
        "composite": true,
        "outDir": "./dist",
        "rootDir": "./src"
    },
    "include": ["src"]
}

// packages/utils/tsconfig.json
{
    "compilerOptions": {
        "composite": true,
        "outDir": "./dist",
        "rootDir": "./src"
    },
    "include": ["src"]
}
```

Build all:

```bash
tsc --build
```

## Compilation Commands Summary

| Command              | Description                                        |
|----------------------|----------------------------------------------------|
| `tsc`                | Compile the project using tsconfig.json            |
| `tsc --watch`        | Compile and recompile on file changes              |
| `tsc --project tsconfig.json` | Compile with a specific config file      |
| `tsc --init`         | Generate a new tsconfig.json                       |
| `tsc app.ts`         | Compile a specific file (ignores tsconfig.json)    |
| `tsc --build`        | Build a composite project                          |
| `tsc --noEmit`       | Type-check without emitting output                 |
| `tsc --generateTrace` | Generate performance trace for debugging          |

## Analogy

The `tsconfig.json` is like a recipe for a dish. It specifies what ingredients (files) to use, how to prepare them (compiler options), and where to serve the final dish (output directory). Without the recipe, the chef (`tsc`) does not know what to make. With it, the process is consistent and repeatable.

## Tricky Things to Remember

- When you run `tsc` without any arguments, it searches for `tsconfig.json` in the current directory and parent directories.
- If you specify a file name (e.g., `tsc app.ts`), `tsconfig.json` is ignored and default compiler options are used.
- The `--watch` flag keeps the process running -- you must save the `.ts` file for the `.js` output to update.
- Files in `node_modules` are excluded by default, even without the `exclude` option.
- The `rootDir` setting determines the structure of the output directory -- TypeScript preserves the folder structure relative to `rootDir`.

## More Tricky Scenarios

### Tricky 1: tsc with Specific File Ignores tsconfig.json

```bash
# If tsconfig.json has 'strict: true' and you run:
tsc app.ts
# -- strict mode is NOT applied! Default compiler options are used.
# Always use 'tsc' without arguments to respect tsconfig.json
```

### Tricky 2: rootDir Inference

If your source files are in `src/` and also in the root folder, TypeScript infers the `rootDir` as the common ancestor of all files. This can cause unexpected output structure.

### Tricky 3: sourceMap for Debugging

```json
{
    "compilerOptions": {
        "sourceMap": true
    }
}
```

With source maps, you can debug the original TypeScript code in browser DevTools or VS Code debugger, rather than the compiled JavaScript. The `.js.map` files map each line of JS back to the corresponding TS line.

### Tricky 4: declaration Files for Library Authors

```json
{
    "compilerOptions": {
        "declaration": true,
        "declarationMap": true
    }
}
```

This generates `.d.ts` files alongside your compiled JS. These declaration files allow other TypeScript projects to consume your library with full type information.

## Interview Questions with Answers

### 1. What command generates a `tsconfig.json` file?

`tsc --init` (or `npx tsc --init`) generates a `tsconfig.json` file with default compiler options. It creates a comprehensive configuration file with many commented-out options that you can enable as needed. The generated file includes sensible defaults like `target: "ES2016"`, `module: "commonjs"`, and `strict: true`.

### 2. What is the purpose of the `outDir` option in tsconfig.json?

`outDir` specifies the output directory where compiled JavaScript files are placed. For example, if `outDir: "./dist"`, `src/app.ts` compiles to `dist/app.js`. It keeps compiled output separate from source files, making it cleaner to manage deployments, git repositories (you can add `dist/` to `.gitignore`), and project structure.

### 3. What does `strict: true` enable in TypeScript?

`strict: true` enables all strict type-checking options as a group: `strictNullChecks` (null and undefined are not assignable to other types), `strictFunctionTypes` (stricter function type checking), `strictBindCallApply` (stricter checks for bind/call/apply), `strictPropertyInitialization` (class properties must be initialized in constructor), `noImplicitAny` (error on implicit any), `noImplicitThis` (error on this with implicit any), and `alwaysStrict` (emit "use strict"). Turning on `strict: true` is recommended for all new projects.

### 4. What is the difference between `tsc` and `tsc --watch`?

`tsc` compiles the project once and exits. `tsc --watch` keeps the compiler running in the background, monitoring files for changes. When a file is modified, it automatically recompiles only the changed and affected files (incremental compilation). Watch mode is ideal for development as it provides immediate feedback.

### 5. What happens if you run `tsc app.ts` and there is a tsconfig.json in the same folder?

When you specify a file name (e.g., `tsc app.ts`), the compiler ignores `tsconfig.json` entirely and uses default compiler options. This means you lose all custom configuration (strict mode, outDir, target, etc.). To use tsconfig.json, always run `tsc` without file arguments.

### 6. What are the `include` and `exclude` options used for?

`include` specifies which files or glob patterns the compiler should process (e.g., `["src/**/*"]` includes all files under src/). `exclude` specifies which files or patterns to skip (e.g., `["node_modules", "dist"]`). Without `include`, TypeScript processes all `.ts` files in the project root and its subdirectories.

### 7. How does the `rootDir` option affect the output structure?

`rootDir` specifies the root directory of input TypeScript files. The compiler preserves the directory structure relative to `rootDir` when writing to `outDir`. For example, with `rootDir: "./src"` and `outDir: "./dist"`, `src/utils/helper.ts` becomes `dist/utils/helper.js`. If you do not set `rootDir`, TypeScript infers it as the common ancestor of all input files.

### 8. What does the `target` option control in compilation?

`target` specifies the ECMAScript target version for the compiled JavaScript output. Options include `ES3`, `ES5`, `ES2015` (ES6), `ES2016`, `ES2017`, ..., `ES2022`, or `ESNext`. TypeScript compiles down features like arrow functions, classes, async/await, and optional chaining to the target version. A lower target (e.g., `ES5`) produces more compatible but sometimes more verbose code.

### 9. Why would you use `esModuleInterop` in tsconfig.json?

`esModuleInterop` enables better compatibility between ES modules and CommonJS modules. It allows default imports from CommonJS modules that do not have a default export (e.g., `import express from "express"` instead of `import * as express from "express"`). It also enables importing named exports from modules that use `module.exports`. This option is recommended for most projects.

### 10. What is the difference between `tsc --build` and `tsc`?

`tsc --build` is used for composite TypeScript projects with project references. It builds all referenced projects in the correct dependency order, caches build outputs, and performs incremental builds. `tsc` (without `--build`) compiles a single project defined by `tsconfig.json`. `--build` is more efficient for monorepos or projects split across multiple packages.
