# Enhanced create-T3-app

More tools to enhance your experience while develop your app.

<br />
## Lint & Format

Detect error by enforcing some rule & beautifully format it.

_Confuse about plugin and config?_

- Read [this](https://stackoverflow.com/questions/44690308/whats-the-difference-between-prettier-eslint-eslint-plugin-prettier-and-eslint) and
- Read [this](https://stackoverflow.com/questions/53189200/whats-the-difference-between-plugins-and-extends-in-eslint)

### 1. Install prettier with config for eslint & plugin for tailwind

`npm install --save-dev prettier eslint-config-prettier eslint-plugin-prettier prettier-plugin-tailwindcss`

### 2. Add prettier config file

`prettier.config.cjs`

```js
module.exports = {
	trailingComma: 'es5',
	useTabs: true,
	tabWidth: 2,
	semi: false,
	singleQuote: true,
	bracketSpacing: false,
	jsxSingleQuote: true,
	plugins: [require('prettier-plugin-tailwindcss')],
	tailwindConfig: './tailwind.config.cjs',
}
```

### 3. Extend prettier on eslint config file `.eslint.json`

```diff
{
- "plugins": ["@typescript-eslint"],
+ "plugins": ["@typescript-eslint", "prettier"],
	"extends": [
		"next/core-web-vitals",
		"plugin:@typescript-eslint/recommended",
+		"prettier"
	],
+	"rules": {
+		"prettier/prettier": "warn"
+	}
}
```

### 4. Add more plugin when you needed

I personally love [unicorn](https://github.com/sindresorhus/eslint-plugin-unicorn)

### 5. Lint & format all of your file

`npx prettier --write .`
`npx eslint .`

<br />

## Git hooks

Adding pre-commit check (lint, format), commit message check, and emoji 😄

### 1. Pre-commit check

Add husky to the project
`npx husky-init && npm install`

Install lint-staged
`npm install --save-dev lint-staged`

Add config file `.lintstagedrc`

```json
{
	"*.{js,jsx,cjs,ts,tsx}": "eslint --fix",
	"*.{md,json}": "prettier --write"
}
```

Edit pre-commit husky hook to run lint-staged

```diff
#!/usr/bin/env sh
. "$(dirname -- "$0")/_/husky.sh"

- npm test
+ npx lint-staged
```

If the log message doesn't show correctly, see this [issue](https://github.com/typicode/husky/issues/968#issuecomment-1176848345)

### 2. Commit message

Install commitlint
`npm install -D @commitlint/cli @commitlint/config-conventional`

Add config file `commitlint.config.cjs`

```json
module.exports = {
	extends: ['@commitlint/config-conventional'],
}
```

Add to commit-message hook
`npx husky add .husky/commit-msg "npx commitlint --edit \"\$1\""`

Test by making a commit
`git commit -m "foo: bar"`

#### Adding Emoji 🤯🚀

Install [gitmoji](https://github.com/carloscuesta/gitmoji)
`npm i -g gitmoji-cli`

Install gitmoji config for commitlint
`npm i -D commitlint-config-gitmoji`

Update commitlint config file

```diff
module.exports = {
-	extends: ['@commitlint/config-conventional'],
+	extends: ['gitmoji'],
+	rules: {
+		'header-max-length': [0, 'always', 100],
+		'scope-case': [0, 'always', 'pascal-case']
+ 	}
}
```

Commit using gitmoji
`gitmoji -c`

<br />

## Bundle Analyzer 📈

> Make sure your app perform fast.

Install bundle analyzer
`npm -i -D @next/bundle-analyzer`

Edit next.config.cjs

```diff
+ import bundleAnalyzer from '@next/bundle-analyzer'

+ const withBundleAnalyzer = bundleAnalyzer({
+ 	enabled: process.env.ANALYZE === 'true',
+ })

function defineNextConfig(config) {
-	return config
+	return withBundleAnalyzer(config)
}
```

Add bundle analyzer build script

```diff
+	"build-stats": "ANALYZE=true npm run build"
```

Run build with bundle analyzer
`npm run build-stats`
