# Setting Up the Environment for TypeScript

## Prerequisite Installations

Before you can start writing TypeScript code, you need three core tools installed on your machine.

### 1. Web Browser

A modern web browser (Chrome, Firefox, Edge, etc.) is needed to run and test the compiled JavaScript output. The browser's developer tools console is useful for debugging.

### 2. Visual Studio Code (VS Code)

VS Code is the recommended editor for TypeScript development because of its first-class TypeScript support. It provides:

- Built-in TypeScript IntelliSense (autocompletion, hover info, error checking).
- Integrated terminal for running commands.
- Extensions marketplace for additional tools.
- Debugger support for TypeScript source maps.

### 3. Node.js

Node.js is a JavaScript runtime built on Chrome's V8 engine. It is required because:

- The TypeScript compiler (`tsc`) is itself written in TypeScript/JavaScript and runs on Node.js.
- `npm` (Node Package Manager) comes bundled with Node.js and is used to install TypeScript and other packages.
- Many TypeScript projects use Node.js for build tooling and server-side development.

**Installation Steps:**

1. Download Node.js from the official website (nodejs.org) -- choose the LTS (Long Term Support) version for stability.
2. Run the installer and follow the setup wizard.
3. Verify the installation by opening a terminal and running:
   ```bash
   node --version
   npm --version
   ```

## Analogy

Setting up the environment for TypeScript is like preparing a kitchen before cooking. The browser is the dining table where the food will be served (executed). VS Code is your chef's knife and cutting board (the tools you work with). Node.js is the stove and oven (the infrastructure that makes cooking possible). Without any one of these, the process becomes difficult or impossible.

## Tricky Things to Remember

- Node.js and npm come together -- if you have Node.js, you already have npm.
- If you are behind a corporate proxy, npm installs may fail. Configure npm proxy settings if needed.
- On macOS/Linux, you may need to use `sudo` for global npm installs, or configure npm to use a local prefix.
- On Windows, ensure the terminal you use (PowerShell, CMD, Git Bash) has proper permissions.
- VS Code's integrated terminal inherits your system PATH, so make sure Node.js is in your PATH.

## Interview Questions

1. What are the three core prerequisites for setting up a TypeScript development environment?
2. Why is Node.js required for TypeScript development?
3. Why is VS Code particularly well-suited for TypeScript development?
4. How do you verify that Node.js and npm are correctly installed?
5. What is the role of a web browser in TypeScript development?
6. How does VS Code's TypeScript IntelliSense work without installing any extensions?
7. What command would you use to check the version of npm installed?
8. What is the difference between LTS and Current versions of Node.js?
9. How do you troubleshoot npm install failures on Windows?
10. Can you use editors other than VS Code for TypeScript development? If so, which ones?
