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

## Analogy

The `tsconfig.json` is like a recipe for a dish. It specifies what ingredients (files) to use, how to prepare them (compiler options), and where to serve the final dish (output directory). Without the recipe, the chef (`tsc`) does not know what to make. With it, the process is consistent and repeatable.

## Compilation Commands Summary

| Command              | Description                                        |
|----------------------|----------------------------------------------------|
| `tsc`                | Compile the project using tsconfig.json            |
| `tsc --watch`        | Compile and recompile on file changes              |
| `tsc --project tsconfig.json` | Compile with a specific config file      |
| `tsc --init`         | Generate a new tsconfig.json                       |
| `tsc app.ts`         | Compile a specific file (ignores tsconfig.json)    |
| `tsc --build`        | Build a composite project                          |

## Tricky Things to Remember

- When you run `tsc` without any arguments, it searches for `tsconfig.json` in the current directory and parent directories.
- If you specify a file name (e.g., `tsc app.ts`), `tsconfig.json` is ignored and default compiler options are used.
- The `--watch` flag keeps the process running -- you must save the `.ts` file for the `.js` output to update.
- Files in `node_modules` are excluded by default, even without the `exclude` option.
- The `rootDir` setting determines the structure of the output directory -- TypeScript preserves the folder structure relative to `rootDir`.

## Interview Questions

1. What command generates a `tsconfig.json` file?
2. What is the purpose of the `outDir` option in tsconfig.json?
3. What does `strict: true` enable in TypeScript?
4. What is the difference between `tsc` and `tsc --watch`?
5. What happens if you run `tsc app.ts` and there is a tsconfig.json in the same folder?
6. What are the `include` and `exclude` options used for?
7. How does the `rootDir` option affect the output structure?
8. What does the `target` option control in compilation?
9. Why would you use `esModuleInterop` in tsconfig.json?
10. What is the difference between `tsc --build` and `tsc`?
