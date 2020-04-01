---
title: "Testing TypeScript Apps in Node.js"
date: 2020-03-31T11:22:30-03:00
draft: false
---

### Unit testing is hard

In this post, I will not dive into why is unit testing important or if you should or not care about testing (hint: you should, always), I assuming that you already know how important is testing.

So let's go straight to business...

### But setting up the tooling can be harder

Setting up the unit testing environment could be tricky some times and we are not talking about typescript but any language really but, if you tried to do it for a typescript development you know it is more complicated than what is seen. it took to my several try/error till I got a setup that works for me, that does not mean that it will be the best setup for everything nor that it will be what are you looking for but it definitely can be helpful as a starting point.

The first thing we should care is linting and this a very personal decision that every developer and/or team has to do by their own before start writting the first line of code. Once that decisicion had been made then we need to setup the tools, there are few options out there but I ussually go for two: [`eslint`](https://eslint.org) and [`tslint`](https://palantir.github.io/tslint/).

In the case of tslint we just need to install the package:

```bash
npm i -D tslint
```

And, create the `tslint.json` file, where we can tweak the rules to accommodate them to the guidelines we decided before, this file looks something like this:

```ts
// the complete file can be found in
// https://github.com/rhynl/setup-for-testing-typescript/tslint.json
{
  "defaultSeverity": "warning",
  "rules": {
    "align": [
      false,
      "parameters",
      "arguments",
      "statements"
    ],
    // ...
    "max-line-length": [
      true,
      140
    ],
    // ...
    "whitespace": [
      true,
      "check-branch",
      "check-decl",
      "check-operator",
      "check-separator",
      "check-type"
    ]
  }
}
```

That was easy right? That is because tslint as the name implies is ment to be use with typescript, the situation is quite different with eslint, as you will see, same as before we need to install the packages:

```sh
npm i -D eslint @typescript-eslint/eslint-plugin @typescript-eslint/parser
```

And, create the `.eslintrc` file, where we can tweak the rules in the same way we did for `tslint`, especial attention to how to use `@typescript-eslint/eslint-plugin` and `@typescript-eslint/parser`, basically extending the recommended configuration of `eslint-plugin` and using the `parser` to process the TypeScript before linting, and this is required because, by default, eslint does not support TypeScript.

```ts
// the complete file can be found in
// https://github.com/rhynl/setup-for-testing-typescript/.eslintrc
{
  "extends": [
    "plugin:@typescript-eslint/recommended"
  ],
  "env": {
    // ...
  },
  "parser": "@typescript-eslint/parser",
  "parserOptions": {
      // ...
    }
  },
  "plugins": [
    "@typescript-eslint/eslint-plugin"
  ],
  "rules": {
    // ...
  }
}
```

We include linting as part of the unit testing process because follow a coding guideline and enforce it through linting is a way to test our code against known anti-pattern that can affect the quality of the code, it could helpfull to add a script inside `package.json` to run the linting every time we want.

```ts
// the complete file can be found in
// https://github.com/rhynl/setup-for-testing-typescript/package.json
{
  // ...
  "scripts": {
    "lint": "eslint src/**/* --color --fix && tslint --fix --project ."
  },
  // ...
}
```

Now we can start with test tools, we are going to use, [`mocha`](https://mochajs.org) as a test runner, and [`chai`](https://www.chaijs.com) as an assertion library. Also, [`ts-node`](https://github.com/TypeStrong/ts-node) needs to be installed as well as the typings for [`mocha`](https://mochajs.org) and [`chai`](https://www.chaijs.com).

```bash
npm i -D mocha chai @types/mocha @types/chai ts-node
```

And, create a `.mocharc.json` file, there are different ways of create a [`.mocharc`](https://github.com/mochajs/mocha/tree/master/example/config) file, you can use whenever is best suitable for you project. The content looks somethign similar to the follows.

```ts
// the complete file can be found in
// https://github.com/rhynl/setup-for-testing-typescript/.mocharc.json
{
  "require": "ts-node/register",
  "colors": true,
  "recursive": true,
  "full-trace": true,
  "fail": true,
  "exit": true,
  "spec": "src/**/*.spec.ts"
}
```

Notice the relevant parts, the `require` atribute is where we ask to use `ts-node/register` and `spec` wehre we said where to look out for test files. Now that everythin is in place, we can add a script to `package.json` with something like this.

```ts
// the complete file can be found in
// https://github.com/rhynl/setup-for-testing-typescript/package.json
{
  // ...
  "scripts": {
    "lint": "eslint src/**/* --color --fix && tslint --fix --project .",
    "pretest": "npm run lint --silent",
    "test": "mocha"
  },
  // ...
}
```

That way we can run linting and test as part of the same script. There is a final step to do, and that is setup code coverage. For that let's use [`Istanbul`](https://istanbul.js.org).

```bash
npm i -D nyc
```

And, create a `.nycrc` file with the desired configuration, something similar to the one below.

```ts
// the complete file can be found in
// https://github.com/rhynl/setup-for-testing-typescript/.nycrc
{
  // ...
  "branches": 80,
  "lines": 80,
  "function": 80,
  "statements": 80,
  "reporter": [
    "text",
    "html",
    "lcov"
  ],
  // ...
  "exclude": [
    "**/**/*.spec.ts"
  ]
}
```

Now, we can add a new script to the `package.json` file, to be able to check the code coverage from time to time.

```ts
// the complete file can be found in
// https://github.com/rhynl/setup-for-testing-typescript/package.json
{
  // ...
  "scripts": {
    "coverage": "nyc npm run test --silent",
    "lint": "eslint src/**/* --color --fix && tslint --fix --project .",
    "pretest": "npm run lint --silent",
    "test": "mocha",
    "tdd": "mocha --extensions ts --watch --watch-files src || exit 0"
  },
  // ...
}
```

I also add a script to helps me to do [`Test Driven Development`](https://en.wikipedia.org/wiki/Test-driven_development), could you tell what it does.

That it is. Now you are ready to create great apps testing without issues.

You can find the example code [here](https://github.com/rhynl/setup-for-testing-typescript).
