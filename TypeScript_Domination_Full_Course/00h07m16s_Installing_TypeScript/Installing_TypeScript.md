# Installing TypeScript

## Installing the TypeScript Compiler

TypeScript is distributed as an npm package. You install the TypeScript compiler (`tsc`) using npm.

### Global Installation

The most common approach is to install TypeScript globally on your system:

```bash
npm install -g typescript
```

This makes the `tsc` command available in any terminal window.

### Verify Installation

After installation, verify that TypeScript is installed correctly:

```bash
tsc --version
```

This should display the version number, e.g., `Version 5.4.0`.

### Local Installation (per project)

For projects that need a specific TypeScript version, install it locally:

```bash
npm install --save-dev typescript
```

Then use `npx` to run it:

```bash
npx tsc --version
```

## Using the TypeScript Compiler (tsc)

The `tsc` command compiles `.ts` files into `.js` files.

**Basic usage:**

```bash
# Compile a single file
tsc app.ts

# Compile with watch mode (auto-recompile on changes)
tsc app.ts --watch

# Compile a project (uses tsconfig.json)
tsc

# Compile with specific config file
tsc --project tsconfig.json
```

### Example

Create a file `hello.ts`:

```typescript
function greet(name: string): string {
    return `Hello, ${name}!`;
}

console.log(greet("TypeScript"));
```

Compile it:

```bash
tsc hello.ts
```

This generates `hello.js` with the compiled JavaScript:

```javascript
function greet(name) {
    return "Hello, " + name + "!";
}

console.log(greet("TypeScript"));
```

## Analogy

Installing TypeScript globally is like buying a toolkit that you keep in your garage -- it is available for any project you work on. Installing it locally is like having a dedicated tool kit for each project -- you can use different versions for different projects. The `tsc` command is the wrench that transforms your TypeScript design into a JavaScript product.

## Tricky Things to Remember

- If you have TypeScript installed both globally and locally, the local version takes precedence when using `npx` or npm scripts.
- Global install may require administrator privileges (`sudo` on macOS/Linux, "Run as administrator" on Windows).
- The `tsc` command without any input file looks for a `tsconfig.json` file in the current directory.
- Always version-lock TypeScript in your project's `package.json` to avoid unexpected compiler behavior changes.
- After global installation, you may need to restart your terminal or reload VS Code for the `tsc` command to be recognized.

## Interview Questions

1. What command installs TypeScript globally using npm?
2. What is the difference between global and local installation of TypeScript?
3. How do you verify the installed TypeScript version?
4. What does the `tsc` command do?
5. How do you compile a TypeScript file in watch mode?
6. What is `npx tsc` and when would you use it?
7. What happens when you run `tsc` without specifying any file?
8. Why would you install TypeScript locally instead of globally?
9. What file does `tsc` look for to know how to compile a project?
10. How do you compile TypeScript using a custom configuration file?
