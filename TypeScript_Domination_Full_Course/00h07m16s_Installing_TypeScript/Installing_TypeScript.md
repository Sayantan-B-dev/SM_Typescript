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

## Expanded Examples

### Example 1: Complete Installation and Compilation Cycle

```bash
# Step 1: Install TypeScript globally
npm install -g typescript

# Step 2: Verify installation
tsc --version
# Output: Version 5.4.2

# Step 3: Create a TypeScript file
echo 'let message: string = "Hello TypeScript!"; console.log(message);' > hello.ts

# Step 4: Compile it
tsc hello.ts

# Step 5: Run the output
node hello.js
# Output: Hello TypeScript!
```

### Example 2: Different Installation Methods Compared

```bash
# Method A: Global install (one version for all projects)
npm install -g typescript
tsc --version  # Available everywhere

# Method B: Local install (version per project)
cd my-project
npm install --save-dev typescript
npx tsc --version  # Must use npx or npm scripts

# Method C: No install (use VS Code's built-in)
# VS Code has TypeScript built in for language features
# But you still need tsc installed to compile from terminal
```

### Example 3: Using npm Scripts for Local TypeScript

Add to `package.json`:

```json
{
    "scripts": {
        "build": "tsc",
        "watch": "tsc --watch",
        "start": "node dist/index.js"
    }
}
```

Then run:

```bash
npm run build
npm run watch
npm start
```

### Example 4: Multiple TypeScript Versions

```bash
# Check global version
tsc --version

# Check local project version
cd project-a
npx tsc --version  # Might be 4.9.5

cd ../project-b
npx tsc --version  # Might be 5.4.2

# The local version in node_modules takes precedence
```

### Example 5: Installing Specific Version

```bash
# Install a specific version globally
npm install -g typescript@4.9.5

# Install a specific version locally
npm install --save-dev typescript@5.3.3

# Check available versions
npm view typescript versions --json
```

### Example 6: Compiling with Error Output

Create `error.ts`:

```typescript
let age: number = "twenty-five"; // Type error
```

Compile it:

```bash
tsc error.ts
# Output:
# error.ts:1:5 - error TS2322: Type 'string' is not assignable to type 'number'.
# 1 let age: number = "twenty-five";
#       ~~~
# Found 1 error. Watching for file changes.
```

### Example 7: Using --watch Mode

```bash
# Terminal 1: Start watch mode
tsc app.ts --watch
# Output: [12:00:00] Starting compilation in watch mode...
#         [12:00:00] Found 0 errors. Watching for file changes.

# In another terminal or editor: edit app.ts
# Watch mode automatically recompiles:
# [12:01:00] File change detected. Starting incremental compilation...
# [12:01:00] Found 0 errors. Watching for file changes...
```

## Analogy

Installing TypeScript globally is like buying a toolkit that you keep in your garage -- it is available for any project you work on. Installing it locally is like having a dedicated tool kit for each project -- you can use different versions for different projects. The `tsc` command is the wrench that transforms your TypeScript design into a JavaScript product.

## Tricky Things to Remember

- If you have TypeScript installed both globally and locally, the local version takes precedence when using `npx` or npm scripts.
- Global install may require administrator privileges (`sudo` on macOS/Linux, "Run as administrator" on Windows).
- The `tsc` command without any input file looks for a `tsconfig.json` file in the current directory.
- Always version-lock TypeScript in your project's `package.json` to avoid unexpected compiler behavior changes.
- After global installation, you may need to restart your terminal or reload VS Code for the `tsc` command to be recognized.

## More Tricky Scenarios

### Tricky 1: Global vs Local Precedence

```bash
# If both are installed, which one runs?
# Answer: When using npx or npm scripts, the local version wins.
# When running tsc directly from terminal, the global version runs.

# To force using local version:
npx tsc

# To force using global version:
tsc  # Only if no local version in node_modules/.bin
```

### Tricky 2: TypeScript Version Mismatch

If VS Code uses a different TypeScript version than your project:

1. Open any `.ts` file in VS Code
2. Click the TypeScript version number in the status bar
3. Select "Use Workspace Version" to match your project
4. Or run the command "TypeScript: Select TypeScript Version"

### Tricky 3: npm install -g Fails on Mac/Linux

```bash
# Error: EACCES: permission denied
# Solution 1: Use sudo (not recommended)
sudo npm install -g typescript

# Solution 2: Change npm's default directory (recommended)
npm config set prefix ~/.npm-global
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc
npm install -g typescript
```

## Interview Questions with Answers

### 1. What command installs TypeScript globally using npm?

`npm install -g typescript` installs TypeScript globally, making the `tsc` command available in any terminal window. The `-g` flag stands for "global." After installation, you can run `tsc --version` to verify it works.

### 2. What is the difference between global and local installation of TypeScript?

Global installation (`npm install -g typescript`) makes `tsc` available system-wide from any terminal. It is convenient but uses the same version for all projects. Local installation (`npm install --save-dev typescript`) installs TypeScript in a specific project's `node_modules`, allowing different projects to use different TypeScript versions. Local installations require `npx tsc` or npm scripts to run.

### 3. How do you verify the installed TypeScript version?

Run `tsc --version` in the terminal for the global version, or `npx tsc --version` inside a project directory for the local version. The output shows the version string, e.g., `Version 5.4.2`. You can also check the version in `package.json`: `"devDependencies": { "typescript": "^5.4.2" }`.

### 4. What does the `tsc` command do?

`tsc` (TypeScript Compiler) compiles `.ts` files into `.js` files. It performs type checking, reports any type errors found, and then emits JavaScript code by stripping all TypeScript-specific syntax. It respects compiler options from `tsconfig.json` if present, or uses defaults when compiling individual files.

### 5. How do you compile a TypeScript file in watch mode?

Use the `--watch` flag: `tsc app.ts --watch`. This keeps the compiler running and automatically recompiles the file whenever changes are detected. It is useful during development to get immediate feedback. The watch mode shows "File change detected. Starting incremental compilation..." each time you save.

### 6. What is `npx tsc` and when would you use it?

`npx tsc` runs the TypeScript compiler from the local `node_modules` directory rather than a globally installed version. Use it when you have TypeScript installed locally per project (`npm install --save-dev typescript`) and want to ensure the project-specific version is used. It avoids version conflicts between projects.

### 7. What happens when you run `tsc` without specifying any file?

When run without arguments, `tsc` looks for a `tsconfig.json` file in the current directory (and parent directories). If found, it compiles the project according to the configuration. If no `tsconfig.json` is found, it reports an error: "error TS18003: No inputs were found in config file." You must either specify a file name or create a `tsconfig.json`.

### 8. Why would you install TypeScript locally instead of globally?

Local installation allows different projects to use different TypeScript versions without conflict. This is important when working on multiple projects with different TypeScript version requirements, or when a project needs to pin a specific version for consistency across a team. Local installation also ensures that CI/CD pipelines use the exact version specified in `package.json`.

### 9. What file does `tsc` look for to know how to compile a project?

`tsc` looks for `tsconfig.json` in the current directory. This JSON file specifies compiler options (target, module, outDir, rootDir, strict mode, etc.), which files to include and exclude, and other project settings. You generate it with `tsc --init`. If `tsc` cannot find `tsconfig.json`, it requires a specific file name to compile.

### 10. How do you compile TypeScript using a custom configuration file?

Use the `--project` flag (or `-p`): `tsc --project tsconfig.prod.json`. This tells the compiler to use a specific configuration file instead of the default `tsconfig.json`. This is useful for maintaining different configurations for development, production, testing, or different build targets.
