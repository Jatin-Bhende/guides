# How To Publish an NPM Package

This guide goes through every single step you need to take to publish a package to npm. This is not a minimal guide. We will be setting up a fully production-ready package from an empty directory. This will include:

- [Git](https://git-scm.com/) for version control
- [Typescript](https://www.typescriptlang.org/) for writing type-safe code
- [Prettier](https://prettier.io/) for formatting our code
- [@arethetypeswrong/cli](https://arethetypeswrong.github.io/) for checking our exports
- [tsup](https://tsup.egoist.dev/) for compiling our TypeScript code into CJS and ESM
- [Vitest](https://vitest.dev/) for running our tests
- [GitHub Actions](https://docs.github.com/en/actions) for running our CI process
- [Changesets](https://github.com/changesets/changesets) for versioning and publishing our package

## 0: Prerequisites

Before we begin, make sure you have the following:

- Node.js 16.x or higher
- npm 7.x or higher
- A [GitHub](https://github.com/) account
- An [NPM](https://www.npmjs.com/) account

## 1: Git

In this section, we'll create a new git repository, set up a .gitignore, create an initial commit, create a new repository on GitHub, and push our code to GitHub.

### 1.1: Initialize the Repository

Run the following command to initialize a new git repository:

```bash
mkdir <your-package-name>
cd <your-package-name>
git init
```

### 1.2: Create a .gitignore

Create a `.gitignore` file in the root of your project and add the following:

```
node_modules
```

### 1.3: Create an Initial Commit

Run the following command to create an initial commit:

```bash
git add .
git commit -m "Initial commit"
```

### 1.4: Create a new repository on GitHub

Using the GitHub CLI, run the following command to create a new repository. I've chosen the name `demo-package` for this example:

```bash
gh repo create demo-package --source=. --public
```

### 1.5: Push your code to GitHub

Run the following command to push your code to GitHub:

```bash
git push --set-upstream origin main
```

## 2: `package.json`

In this section, we'll create a `package.json` file, add a `license` field, create a `LICENSE` file, and add a `README.md` file.

### 2.1: Create a `package.json` file

Create a `package.json` file in the root of your project and add the following:

```json
{
	"name": "demo-package",
	"version": "1.0.0",
	"description": "A demo package",
	"keywords": ["demo", "typescript"],
	"homepage": "https://github.com/Jatin-Bhende/demo-package",
	"bugs": {
		"url": "https://github.com/Jatin-Bhende/demo-package/issues"
	},
	"author": "Jatin Bhende",
	"repository": {
		"type": "git",
		"url": "git+https://github.com/Jatin-Bhende/demo-package.git"
	},
	"files": ["dist"],
	"type": "module"
}
```

- `name` is the name by which people will install your package. It must be unique on npm. You can create [organization scopes](https://docs.npmjs.com/about-organization-scopes-and-packages) (such as `@jatinbhende/demo-package`) for free, these can help make it unique.
- `version` is the version of your package. It should follow [semantic versioning](https://semver.org/): the 0.0.1 format. Each time you publish a new version, you should increment this number.
- `description` and `keywords` are short descriptions of your package. They're listed in searches in the npm registry.
- `homepage` is the URL of your package's homepage. The GitHub repo is a good default, or a docs site if you have one.
- `bugs` is the URL where people can report issues with your package.
- `author` is you! You can add optionally add your email and website. If you have multiple contributors, you can specify them as an array of `contributors` with the same formatting.
- `repository` is the URL of your package's repository. This creates a link on the npm registry to your GitHub repo.
- `files` is an array of files that should be included when people install your package. In this case, we're including the `dist` folder. `README.md`, `package.json` and `LICENSE` are included by default.
- `type` is set to `module` to indicate that your package uses ECMAScript modules, not CommonJS modules.

### 2.2: Add the license field

Add a `license` field to your `package.json`. Choose a license [here](https://choosealicense.com/licenses/). I've chosen [MIT](https://choosealicense.com/licenses/mit/).

```json
{
	"license": "MIT"
}
```

### 2.3: Create a `LICENSE` file

Create a file called `LICENSE`(no extension) in the root of your project containing the text of the license.For MIT, the text is:

```
MIT License

Copyright (c) [year] [fullname]

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.
```

Change the [year] and [fullname] placeholders to the current year and your name.

### 2.4: Add a `README.md` file

Create a `README.md` file with a description of your package. Here's an example:

```
**demo-package-demo**

A demo package.
```

This will be shown on the npm registry when people view your package.

## 3: TypeScript

In this section, we'll install TypeScript, set up a `tsconfig.json`, create a source file, create an index file, set up a `build` script, run our build, add `dist` to `.gitignore`, set up a `ci` script, and configure our `tsconfig.json` for the DOM.

### 3.1: Install TypeScript

Run the following command to install TypeScript:

```bash
npm install --save-dev typescript
```

We add `--save-dev` to install TypeScript as a development dependency. This means it won't be included when people install your package.

### 3.2: Set up a `tsconfig.json`

Create a tsconfig.json with the following values:

```json
{
	"compilerOptions": {
		/* Base Options: */
		"esModuleInterop": true,
		"skipLibCheck": true,
		"target": "es2022",
		"allowJs": true,
		"resolveJsonModule": true,
		"moduleDetection": "force",
		"isolatedModules": true,
		"verbatimModuleSyntax": true,

		/* Strictness */
		"strict": true,
		"noUncheckedIndexedAccess": true,
		"noImplicitOverride": true,

		/* If transpiling with TypeScript: */
		"module": "NodeNext",
		"outDir": "dist",
		"rootDir": "src",
		"sourceMap": true,

		/* AND if you're building for a library: */
		"declaration": true,

		/* AND if you're building for a library in a monorepo: */
		"declarationMap": true
	}
}
```

These options are explained in detail in Matt Pocock's [TSConfig Cheat Sheet](https://www.totaltypescript.com/tsconfig-cheat-sheet).

### 3.3: Configure your `tsconfig.json` for the DOM

If your code runs in the DOM (i.e. requires access to `document`, `window`, or `localStorage` etc), skip this step.

If your code doesn't require access to DOM API's, add the following to your `tsconfig.json`:

```json
{
	"compilerOptions": {
		// ...other options
		"lib": ["es2022"]
	}
}
```

This prevents the DOM typings from being available in your code.

If you're not sure, skip this step.

### 3.4: Create A Source File

Create a `src/utils.ts` file with the following content:

```ts
export const add = (a: number, b: number) => a + b;
```

### 3.5: Create An Index File

Create a `src/index.ts` file with the following content:

```ts
export { add } from "./utils.js";
```

The `.js` extension will look odd. [This article](https://www.totaltypescript.com/relative-import-paths-need-explicit-file-extensions-in-ecmascript-imports) explains more.

### 3.6: Set up a `build` script

Add a `scripts` object to your `package.json` with the following content:

```json
{
	"scripts": {
		"build": "tsc"
	}
}
```

This will compile your TypeScript code to JavaScript.

### 3.7: Running Your Build

Run the following command to compile your TypeScript code:

```bash
npm run build
```

This will create a `dist` folder with your compiled JavaScript code.

### 3.8: Add dist to .gitignore

Add the `dist` folder to your `.gitignore` file:

```
dist
```

This will prevent your compiled code from being included in your git repository.

### 3.9: Set up a `ci` script

Add a `ci` script to your `package.json` with the following content:

```json
{
	"scripts": {
		"ci": "npm run build"
	}
}
```

This gives us a quick shortcut for running all required operations on CI.

## 4: Prettier

In this section, we'll install Prettier, set up a `.prettierrc`, set up a `format` script, run the `format` script, set up a `format:check` script, add the `format:check` script to our `ci` script, and run the `ci` script.

Prettier is a code formatter that automatically formats your code to a consistent style. This makes your code easier to read and maintain.

### 4.1: Install Prettier

Run the following command to install Prettier:

```bash
npm install --save-dev prettier
```

### 4.2: Set up a `.prettierrc`

Create a `.prettierrc` file with the following content:

```json
{
	"semi": true,
	"singleQuote": true,
	"trailingComma": "all",
	"printWidth": 80,
	"tabWidth": 2
}
```

You can add more options to this file to customize Prettier's behavior. You can find a full list of options [here](https://prettier.io/docs/en/options.html).

### 4.3: Set up a format script

Add a `format` script to your `package.json` with the following content:

```json
{
	"scripts": {
		"format": "prettier --write ."
	}
}
```

This will format all files in your project using Prettier.

### 4.4: Run the `format` script

Run the following command to format all files in your project:

```bash
npm run format
```

You might notice some files change. Commit them with:

```bash
git add .
git commit -m "Format code with Prettier"
```

### 4.5: Set up a format:check script

Add a `format:check` script to your `package.json` with the following content:

```json
{
	"scripts": {
		"format:check": "prettier --check ."
	}
}
```

This will check if all files in your project are formatted correctly.

### 4.6: Adding to our CI script

Add the `format:check` script to our `ci` script in `package.json` with the following content:

```json
{
	"scripts": {
		"ci": "npm run build && npm run format:check"
	}
}
```

This will run the `format:check` script as part of your CI process.

## 5: `exports`, `main`, and `@arethetypeswrong/cli`

In this section, we'll install `@arethetypeswrong/cli`, set up a `exports:check` script, run the `exports:check` script, set up a `main` field, run the `exports:check` script again, set up a `ci` script, and run the `ci` script.

`@arethetypeswrong/cli` is a tool that checks if your package exports are correct. This is important because these are easy to get wrong, and can cause issues for people using your package.

### 5.1: Install `@arethetypeswrong/cli`

Run the following command to install `@arethetypeswrong/cli`:

```bash
npm install --save-dev @arethetypeswrong/cli
```

### 5.2: Set up a `exports:check` script

Add a `exports:check` script to your `package.json` with the following content:

```json
{
	"scripts": {
		"exports:check": "attw --pack ."
	}
}
```

This will check if all exports from your package are correct.

### 5.3: Run the `exports:check` script

Run the following command to check if all exports from your package are correct:

```bash
npm run exports:check
```

You should notice various errors:

```
┌───────────────────┬──────────────────────┐
│                   │ "tt-package-demo"    │
├───────────────────┼──────────────────────┤
│ node10            │ 💀 Resolution failed │
├───────────────────┼──────────────────────┤
│ node16 (from CJS) │ 💀 Resolution failed │
├───────────────────┼──────────────────────┤
│ node16 (from ESM) │ 💀 Resolution failed │
├───────────────────┼──────────────────────┤
│ bundler           │ 💀 Resolution failed │
└───────────────────┴──────────────────────┘
```

This indicates that no version of Node, or any bundler, can use our package.

Let's fix this.

### 5.4: Setting `main`

Add a `main` field to your `package.json` with the following content:

```json
{
	"main": "dist/index.js"
}
```

This tells Node where to find the entry point of your package.

### 5.5: Try `exports:check` again

Run the following command to check if all exports from your package are correct:

```bash
npm run exports:check
```

You should notice only one warning:

```
┌───────────────────┬──────────────────────────────┐
│                   │ "tt-package-demo"            │
├───────────────────┼──────────────────────────────┤
│ node10            │ 🟢                           │
├───────────────────┼──────────────────────────────┤
│ node16 (from CJS) │ ⚠️ ESM (dynamic import only) │
├───────────────────┼──────────────────────────────┤
│ node16 (from ESM) │ 🟢 (ESM)                     │
├───────────────────┼──────────────────────────────┤
│ bundler           │ 🟢                           │
└───────────────────┴──────────────────────────────┘
```

This is telling us that our package is compatible with systems running ESM. People using CJS (often in legacy systems) will need to import it using a dynamic import.

### 5.6 Fix The CJS Warning

If you don't want to support CJS (which I recommend), change the check-exports script to:

```json
{
	"scripts": {
		"exports:check": "attw --pack . --ignore-rules=cjs-resolves-to-esm"
	}
}
```

Now, running check-exports will show everything as green:

```
┌───────────────────┬───────────────────┐
│                   │ "tt-package-demo" │
├───────────────────┼───────────────────┤
│ node10            │ 🟢                │
├───────────────────┼───────────────────┤
│ node16 (from CJS) │ 🟢 (ESM)          │
├───────────────────┼───────────────────┤
│ node16 (from ESM) │ 🟢 (ESM)          │
├───────────────────┼───────────────────┤
│ bundler           │ 🟢                │
└───────────────────┴───────────────────┘
```

If you prefer to dual publish CJS and ESM, skip this step.

### 5.7: Adding to our CI script

Add the `exports:check` script to your `ci` script in your `package.json`:

```json
{
	"scripts": {
		"ci": "npm run build && npm run check-format && npm run check-exports"
	}
}
```
