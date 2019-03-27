# ESLint config validation reproduction

This repository reproduces an issue with validating rules from plugins that are
included implicitly from config extension sources.

## Explicitly included plugins

The `explicit-plugin` directory demonstates config validation behavior for
plugin rules when the plugin is included explicitly in the config (via the
`plugins` config key).

```sh
$ npm run lint

> @jarrodldavis/repro-eslint-validate-issue-explicit-plugin@1.0.0 lint /Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin
> eslint .

Error: /Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/.eslintrc.json:
	Configuration for rule "node/exports-style" is invalid:
	Value "invalid-option" should be equal to one of the allowed values.

    at validateRuleOptions (/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/node_modules/eslint/lib/config/config-validator.js:133:19)
    at Object.keys.forEach.id (/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/node_modules/eslint/lib/config/config-validator.js:176:9)
    at Array.forEach (<anonymous>)
    at validateRules (/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/node_modules/eslint/lib/config/config-validator.js:175:30)
    at Object.validate (/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/node_modules/eslint/lib/config/config-validator.js:262:5)
    at loadFromDisk (/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/node_modules/eslint/lib/config/config-file.js:544:19)
    at Object.load (/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/node_modules/eslint/lib/config/config-file.js:587:20)
    at Config.getLocalConfigHierarchy (/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/node_modules/eslint/lib/config.js:240:44)
    at Config.getConfigHierarchy (/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/node_modules/eslint/lib/config.js:192:43)
    at Config.getConfigVector (/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/explicit-plugin/node_modules/eslint/lib/config.js:299:21)
```

## Implicitly included plugins

The `implicit-plugin` directory demonstates config validation behavior for
plugin rules when the plugin is included implictly in the config from a config
extension source (via the `extends` config key).

```sh
$ npm run lint

> @jarrodldavis/repro-eslint-validate-issue-implicit-plugin@1.0.0 lint /Users/jarrodldavis/source/repos/repro-eslint-validate-issue/implicit-plugin
> eslint .


/Users/jarrodldavis/source/repos/repro-eslint-validate-issue/implicit-plugin/index.js
  1:7   error  'none' is assigned a value but never used  no-unused-vars
  1:22  error  "./doesnt-exist" is not found              node/no-missing-require

✖ 2 problems (2 errors, 0 warnings)
```

## Direcotory diff

Here is a commandline diff of the two directories

```sh
$ diff -ur -I "_where" explicit-plugin/ implicit-plugin/
diff -ur -I _where explicit-plugin/.eslintrc.json implicit-plugin/.eslintrc.json
--- explicit-plugin/.eslintrc.json	2019-03-27 17:28:56.000000000 -0600
+++ implicit-plugin/.eslintrc.json	2019-03-27 17:29:04.000000000 -0600
@@ -5,7 +5,6 @@
         "es6": true,
         "node": true
     },
-    "plugins": ["node"],
     "extends": ["eslint:recommended", "plugin:node/recommended"],
     "globals": {
         "Atomics": "readonly",
diff -ur -I _where explicit-plugin/package-lock.json implicit-plugin/package-lock.json
--- explicit-plugin/package-lock.json	2019-03-27 17:17:28.000000000 -0600
+++ implicit-plugin/package-lock.json	2019-03-27 17:19:13.000000000 -0600
@@ -1,5 +1,5 @@
 {
-  "name": "@jarrodldavis/repro-eslint-validate-issue-explicit-plugin",
+  "name": "@jarrodldavis/repro-eslint-validate-issue-implicit-plugin",
   "version": "1.0.0",
   "lockfileVersion": 1,
   "requires": true,
diff -ur -I _where explicit-plugin/package.json implicit-plugin/package.json
--- explicit-plugin/package.json	2019-03-27 17:21:08.000000000 -0600
+++ implicit-plugin/package.json	2019-03-27 17:21:13.000000000 -0600
@@ -1,7 +1,7 @@
 {
-  "name": "@jarrodldavis/repro-eslint-validate-issue-explicit-plugin",
+  "name": "@jarrodldavis/repro-eslint-validate-issue-implicit-plugin",
   "version": "1.0.0",
-  "description": "Explicit plugin specified in ESLint config",
+  "description": "Implicit plugin specified from config extend source",
   "repository": "jarrodldavis/repro-eslint-validate-issue",
   "main": "index.js",
   "scripts": {
```

```diff
diff -ur -I _where explicit-plugin/.eslintrc.json implicit-plugin/.eslintrc.json
--- explicit-plugin/.eslintrc.json	2019-03-27 17:28:56.000000000 -0600
+++ implicit-plugin/.eslintrc.json	2019-03-27 17:29:04.000000000 -0600
@@ -5,7 +5,6 @@
         "es6": true,
         "node": true
     },
-    "plugins": ["node"],
     "extends": ["eslint:recommended", "plugin:node/recommended"],
     "globals": {
         "Atomics": "readonly",
```

```diff
diff -ur -I _where explicit-plugin/package-lock.json implicit-plugin/package-lock.json
--- explicit-plugin/package-lock.json	2019-03-27 17:17:28.000000000 -0600
+++ implicit-plugin/package-lock.json	2019-03-27 17:19:13.000000000 -0600
@@ -1,5 +1,5 @@
 {
-  "name": "@jarrodldavis/repro-eslint-validate-issue-explicit-plugin",
+  "name": "@jarrodldavis/repro-eslint-validate-issue-implicit-plugin",
   "version": "1.0.0",
   "lockfileVersion": 1,
   "requires": true,
```

```diff
diff -ur -I _where explicit-plugin/package.json implicit-plugin/package.json
--- explicit-plugin/package.json	2019-03-27 17:21:08.000000000 -0600
+++ implicit-plugin/package.json	2019-03-27 17:21:13.000000000 -0600
@@ -1,7 +1,7 @@
 {
-  "name": "@jarrodldavis/repro-eslint-validate-issue-explicit-plugin",
+  "name": "@jarrodldavis/repro-eslint-validate-issue-implicit-plugin",
   "version": "1.0.0",
-  "description": "Explicit plugin specified in ESLint config",
+  "description": "Implicit plugin specified from config extend source",
   "repository": "jarrodldavis/repro-eslint-validate-issue",
   "main": "index.js",
   "scripts": {
```
