# Configure Prettier and ESLint in Visual Studio Code

## What will we be accomplishing?

[Christopher Hamilton](https://www.39digits.com/author/christopher/)6 August 2017

We will cover how to install and configure both [Prettier](https://github.com/prettier/prettier) and [ESLint](http://eslint.org/) within a Node project.

This post will also cover how to integrate these tools into [Visual Studio Code](https://code.visualstudio.com/).

> Updated on 19 September 2017 to include Prettier 1.6's new support for configuration files.

### What is Prettier?

> **Prettier** is an opinionated code formatter

A tool that will format code against some opinionated and standard default formatting rules to ensure all final code maintains a consistent style. It is particularly advantageous when working on projects in teams.

Prettier currently supports JavaScript (including ES2017), JSX, Flow, TypeScript, CSS, LESS, SCSS, JSON and GraphQL.

### What is ESLint?

> **ESLint** - the pluggable linting utility for JavaScript and JSX

Code linting is a form of static analysis that will find both code errors as well as highlight syntax not adhering to formatting styles. This means ESLint can help us find problematic code before we commit to version control and (more importantly) before the code finds its way onto Production.

### Why not just use ESLint?

Good question.

It's true that ESLint could very well handle both the code quality errors and syntax format violations. But we will rely on ESLint purely to help alert us to problematic code and defer the format handling to Prettier.

Prettier's sole specialisation is to handle formatting of code and it does this job extremely well. By focusing primarily on the formatting rules, Prettier can do more sophisticated things under the hood, but due to being opinionated doesn't require time fine-tuning these style rules.

In short, ESLint is the de facto for static analysis and Prettier has quickly become the go-to tool for formatting code.

## Setup the test project

Begin by creating a very simple Node project in a blank directory.

```
npm init -y && mkdir src && touch src/index.js

```

Edit the `src/index.js` file and paste the below code - be sure to include the weird tabbing.

```
var logToConsole = (msg) => {
console.log(msg)
}

    let message = "my message to log"
          logToConsole(message)

```

## Configure Prettier

### Install the dependencies

Begin by installing Prettier as a dev dependency in our project.

```
yarn add --dev prettier

```

We install all the dependencies local to the project and [avoid installing Node packages globally on our system](https://www.smashingmagazine.com/2016/01/issue-with-global-node-npm-packages/).

> Rule of thumb: If your project depends on a package, it should be listed in your package.json file as a dependency and installed locally in your project, rather than globally.

### Test Prettier on our code

At its simplest Prettier can be run as a command-line tool. We'll be doing that now to test everything is correctly installed.

Out of the box Prettier already has excellent defaults, including:

- indenting using spaces and not tabs
- indents are 2 spaces
- semicolons inserted at the end of every line
- setting all quotes to double quotes
- formatting the code against a print width of 80

I personally prefer using single-quotes and a print width of 100. Thankfully, Prettier allows us to override any defaults using various command-line options passed at run-time. Learn about the defaults and decide on your own overrides by reading the [Options available in the Prettier documentation](https://github.com/prettier/prettier)

Run a test of Prettier against our code from the command-line.

```
./node_modules/.bin/prettier --single-quote --print-width=100 "**/*.{js,jsx}"

```

![Prettier output](https://www.39digits.com/content/images/2017/08/prettier_output.png)

Running Prettier this way on the command-line outputs to the screen the format changes that would have been made to our code based on the rules defined. It's important to note that the format changes have not been written to the `src/index.js` file - you can verify this by opening the project in your code editor and see it hasn't changed. If you want Prettier to write the formatting changes you need to add `--write` to the options list.

We've installed Prettier as a dependency in our local project which requires us to include the full `./node_modules/.bin/` path when running it from the command-line.

The last part of the command is where we define which files to run Prettier against. This could be a single file or a group of files. If you look closely we've specified the path as `**/*` and not `src/**/*` where our `index.js` file resides. Despite running against all js and jsx files from the project root, the output was only for our `src/index.js` file. I wanted to show that Prettier smartly ignores the `node_modules` directory and concentrates only on our code.

### Create a Prettier config file

We could continue to just run Prettier from the command-line but supplying our preferred list of overrides would quickly get boring.

Thankfully, as of Prettier 1.6+ we can create a `.prettierrc` file to store our preferred settings and overrides.

Adding a Prettier config file explicitly documents our formatting preferences within the project.

Create a `.prettierrc` file in the project root.

```
touch .prettierrc

```

The configuration uses the [Prettier API Overrides](https://prettier.io/docs/en/options.html#content) to set values (visit the link to find your own preferences).

If following along, add the following JSON to our `.prettierrc` config.

```
{
  "printWidth": 100,
  "singleQuote": true
}

```

Read up more on the [Prettier configuration file](https://github.com/prettier/prettier#configuration-file).

### Add Prettier to our npm scripts

At this point we could rerun the Prettier CLI command to test out our new config file.

Instead, we'll add a line to the scripts section of our `package.json` file to allow us to easily run the formatter manually but also hook into other scripts as pre-requisites.

Open the `package.json` file and add a line to the `"scripts"` section

```
"scripts": {
  "format": "prettier --write '**/*.{js,jsx}'"
}

```

We no longer specify the print width and single quotes as this is covered by our `.prettierrc` file. The `--write` flag is also used to save the changes to our file when run.

Now all you need to do is run `yarn format` to have Prettier format our code.

Note that since we're running Prettier [via a script](https://docs.npmjs.com/misc/scripts#path) we no longer need to provide the full `./node_modules/.bin/` path.

Now that we have the command as an entry in our npm scripts, we can use Prettier as a pre-commit hook using the excellent [Husky](https://github.com/typicode/husky) package. I'll leave that as extra reading for you to keep this tutorial on target. [Update 2018/01/08 - I have just published an article to [automatically format your JavaScript commits using Prettier and Husky](https://www.39digits.com/automatically-format-your-javascript-commits-using-prettier-and-husky/)]

The creation of the npm script still doesn't solve having to manually run Prettier each time we want to format our code. Lets take the opportunity to automate applying any format changes upon saving a supported file type in Visual Studio Code.

## Integrate Prettier with Visual Studio Code

### Install the Prettier extension in Visual Studio Code

Open the extension sidebar (Cmd-Shift-X) inside Visual Studio Code and search for `Prettier - JavaScript formatter`. There are a few extensions with that name so be sure to install the one by [Esben Petersen](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode).

You will need to reload the Visual Studio Code window after installing the extension. You should now see a Prettier notification in the status bar when opening up a file type supported by Prettier.

### Visual Studio Code settings

The only change required to Visual Studio Code's `settings.json` file is to ensure the Format On Save functionality is triggered. This ensures the Prettier extension applies the format rules on each save of a supported file type.

To do this open settings and add Visual Studio Code's `formatOnSave`setting.

```
  "editor.formatOnSave": true,

```

Prior to version 1.6 of Prettier we were required to duplicate our override preferences in the Visual Studio Code settings. Thankfully this duplication is no longer required thanks to our `.prettierrc` config file (documenting and setting these preferences in a single location is a big victory).

> If you are using an earlier version read the [Prettier extension's documentation for available settings](https://github.com/prettier/prettier-vscode) to add to your Visual Studio Code settings.

Now when you save a supported file it will automatically apply your formatting preferences. In fact, do this now to `/src/index.js` to see it in action.

## Configure ESLint

### Install the dependencies

Start by installing ESLint and the the airbnb eslint-config-airbnb

```
npm install --save-dev eslint eslint-config-airbnb

```

Next install the airbnb shareable eslint config (as described at <https://www.npmjs.com/package/eslint-config-airbnb>). We'll discuss shareable configs shortly.

```
(
  export PKG=eslint-config-airbnb;
  npm info "$PKG@latest" peerDependencies --json | command sed 's/[\{\},]//g ; s/: /@/g' | xargs npm install --save-dev "$PKG@latest"
)

```

### Create the ESLint config file

Create a `.eslintrc.json` file in the project root.

```bash
touch .eslintrc.json
```



It is inside this file that you apply project-wide and ESLint configuration and linting rules. Read more on [configuring ESLint](http://eslint.org/docs/user-guide/configuring).

Add the following configuration to your newly created `.eslintrc.json` file.

```
{
  "extends": ["airbnb"]
}

```

### A little more about ESLint Rules

ESLint makes use of rules to determine whether certain code should be allowed or disallowed. No rules are enabled by default so it's up to us to define these rules based on our own requirements.

You can see a [full list of ESLint rules](http://eslint.org/docs/rules/) in the online docs.

At the most simple, we could define the rules in the `.eslintrc.json`under a `"rule"` node. Thankfully, there is the ability to create shareable configurations of rules. These shareable configurations take the naming convention of `eslint-config-<ConfigName>` and are simply npm packages that can be easily distributed. Once installed these configurations are added to the `"extended"` node and the order of inclusion is important so make sure you understand what could be overridden when adding new configs to the extends array.

Read more on [extending configuration files](http://eslint.org/docs/user-guide/configuring.html#extending-configuration-files) in the online docs.

ESLint does come with a set of recommended rules for your convenience so you could easily just install eslint and add `"eslint:recommended"`to the extends list. These recommended rules are great, but I prefer using the excellent (and quickly becoming ubiquitous) airbnb ESLint shareable configuration. Read up on the sensible rules they have set in the [Airbnb JavaScript GitHub repo](https://github.com/airbnb/javascript).

If required, you can override any of these by adding explicit entries in the `"rules"` node or by including further shareable configurations in the `"extends"` array. In fact, we will do the latter further down this tutorial.

### Test ESLint on our code

Run a test of ESLint against our code from the command-line. The configuration defined in `.eslintrc.json` will be automatically used by ESLint.

```
./node_modules/.bin/eslint "**/*.{js,jsx}"

```

As with Prettier, ESLint will ignore the contents of the `node_modules`directory by default.

> You might see warnings such as
> `can't resolve reference #/definitions/basicConfig from id #`

This relates to a bug in `eslint-config-react` and can be safely ignored. A pull request is in the process of being merged as defined at <https://github.com/yannickcr/eslint-plugin-react/pull/1292>

![ESLint command line output](https://www.39digits.com/content/images/2017/08/eslint_output.png)

I have intentionally included 3 errors and 1 warning in the code to see ESLint in action.

We could fix those errors and re-run the command manually from the terminal but where's the fun in that? We'll soon integrate ESLint into Visual Studio and use these to identify and fix the errors.

### Add ESLint to our npm scripts

Before we move onto actually integrating ESLint into our editor, add a line to the scripts section of `package.json`.

```
"scripts": {
  "lint": "eslint '**/*.{js,jsx}' --quiet",
  "format": "prettier --write '**/*.{js,jsx}'"
}

```

The `--quiet` option suppresses warnings and reports only errors. This makes the command especially handy as a pre-commit hook to avoid committing code with errors.

Test this on the command line by running `npm run lint`. You should see the same output as before but without the warning.

### Setting ESLint to ignore files or folders

ESLint conveniently ignores the contents of the `node_modules`directory by default but you can also set other files and folders that should be ignored.

To use this feature you need simply create a `.eslintignore` file in your project root and fill it with the files/directories to ignore. The patterns used are the same as within a `.gitignore` file.

A good use case is a build directory if transpiling code using something like Babel or a bundler like Webpack. We aren't doing any of that in this example so we won't require a `.eslintignore` file.

You can ready more on [ESLint's ability to ignore files and folders](http://eslint.org/docs/user-guide/configuring#ignoring-files-and-directories) in the official documentation.

## Integrate ESLint with Visual Studio Code

### Install the ESLint extension in Visual Studio Code

Once again open the extension sidebar (Cmd-Shift-X) inside Visual Studio Code and search for `ESLint`. There should be only 1 result but in case that has changed be sure to install the extension by [Dirk Baeumer](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint).

You will need to reload the Code window after installing the extension.

### Verify ESLint is working in Visual Studio Code

Open `src/index.js` inside Visual Studio Code and you should now see ESLint in the status bar.

There will also be indicators within the code identifying the errors breaking our defined rules. Under my editor theme the red lines indicate errors and green lines indicate warnings.

![ESLint errors shown in Visual Studio Code](https://www.39digits.com/content/images/2017/08/vscode_eslint_errors.png)

These are just some of the types of errors ESLint may identify and we'll now see how we can resolve it.

## Fixing keyword errors

Hover over the first `var` keyword and you should see a text popup.

```
[eslint] Unexpected var, use let or const instead. (no-var)

```

The initial text provides details on the issue detected and where possible suggestions for a fix. The text in parens tells us which specific ESLint rule is currently being broken (no-var).

Change the `var` keyword to `const` and the error indicator should immediately disappear.

Hovering over `message` should display similar text in the popup suggesting the use of `const` but look carefully at the rule inside the parens - (prefer-const) in this instance.

This time, instead of just changing the `let` keyword click on the `message` variable and a little lightbulb will appear in the gutter next to the row number. Click on the lightbulb and you'll be presented with some fix options - select `Fix this prefer-const option` and the error will be automatically corrected by replacing `let` with the `const`keyword.

## Ignoring expected rule violations

Sometimes we might write code that breaks a rule but we want ESLint to ignore this error - such as with the `console.log` in our example code.

Normally you wouldn't want to use `console.log` throughout your application and the warning is justified. But for the purpose of this example let's assume we want to allow the use without ESLint alerting.

Hover over `console.log` and you'll see an ESLint warning in a familiar format.

```
[eslint] Unexpected console statement. (no-console)

```

The rule name (no-console) is what is most important here. To disable linting for code we can add a comment before it using the `eslint-disable-next-line` trigger.

Add the comment to `src/index.js` above the console command.

```
// eslint-disable-next-line no-console
console.log(msg);

```

You can read more on [disabling ESLint rules](http://eslint.org/docs/user-guide/configuring.html#disabling-rules-with-inline-comments) in the online documentation.

## When format rules collide...

Now all that remains is to fix the error on `msg`.

```
[eslint] Expected parentheses around arrow function argument having a body with curly braces. (arrow-parens)

```

ESLint - based on the formatting rules defined in Airbnb config - requires a set of parens to be on either side of the variable in our ES6 function definition. Add the parens on either side and you'll see the ESLint error hint disappears. Now save and Prettier will take over and remove the parens, once more triggering the ESLint rule.

Our tools are in conflict!

It's time to configure Prettier and ESLint to work together. We want our tools to really focus on their specialisations. To do this we need to disable all formatting rules inside ESLint and let Prettier take over managing the code style.

### Install dependencies

Install the [Prettier config for ESLint](https://github.com/prettier/eslint-config-prettier).

```
npm install --save-dev eslint-config-prettier

```

We rely on ESLint being a code quality linter and defer all formatting to Prettier.

### Enable the ESLint Prettier config

The `eslint-config-prettier` shareable configuration disables all of the formatting rules within ESLint previously set by the `airbnb`ruleset. We definitely still want to keep all of the excellent code quality rules provided by that configuration though.

Edit `.eslintrc.json` and add `"prettier"` after the `"airbnb"`entry.

```
"extends": ["airbnb", "prettier"],

```

The order of configs are important to ensure the new shareable config rules override and disable all formatting rules already set within ESLint.

Prettier will now automatically format our code on each file save and these changes won't conflict with ESLint.

### (Optional) Enable the ESLint Prettier plugin

There is a [Prettier plugin for ESLint](https://github.com/prettier/eslint-plugin-prettier).

This plugin leverages ESLint to show formatting errors as you type and can allow ESLint to auto-fix these. We've set Prettier to run on each file save so we would only benefit from the added visual cues in our editor. If implementing the plugin you will benefit from ESLint reporting Prettier violations when run from the command-line.

If this is something you'd be interested in implementing, install the plugin as a dependency.

```
npm install --save-dev eslint-plugin-prettier

```

Next, add a new `"plugins"` node to your `.eslintrc.json` to enable the Prettier ESLint plugin. We also need to define the prettier rule to show errors.

This is what your `.eslintrc.json` file should now contain.

```
{
  "extends": ["airbnb", "prettier"],
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": ["error"]
  },
}

```

> In Prettier versions prior to the configuration file support, the rule needed to duplicate our Prettier preferences once more `"prettier/prettier": ["error", { "singleQuote": true, "printWidth": 100 }]`. Thankfully this is no longer required.

### Alternative option - prettier-eslint

There is a separate JavaScript module called [prettier-eslint](https://github.com/prettier/prettier-eslint) that will run your code through Prettier and then ESLint --fix.

> This formats your code via prettier, and then passes the result of that to eslint --fix. This way you can get the benefits of prettier's superior formatting capabilities, but also benefit from the configuration capabilities of eslint.

The Visual Studio Code Prettier extension has a `"prettier.eslintIntegration"` setting that, if set to `true`, runs this module instead of directly through Prettier.

I personally prefer to defer the formatting functionality to Prettier alone and that's why I choose the `eslint-config-prettier` route of disabling all formatting rules in ESLint. But know that this does exist as an option.

## Bonus Tip: Explicitly enabling ES6 syntax support

The Airbnb configuration enables ES6 syntax support (see <https://github.com/airbnb/javascript/blob/master/packages/eslint-config-airbnb-base/rules/es6.js>).

If you temporarily remove everything from your `.eslintrc.json` file, save and load `src/index.js` in Visual Studio Code you will see the ES6 function definition syntax now throws a linting error. This is because ES6 syntax support is no longer enabled inside ESLint - it currently lints against ES5 syntax by default.

You are able to explicitly enable ES6 support inside `.eslintrc.json`.

```
  "parserOptions": {
    "ecmaVersion": 6,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  "env": {
    "es6": true,
    "browser": true,
    "node": true
  }

```

Read more on [specifying parser options](http://eslint.org/docs/user-guide/configuring#specifying-parser-options) in the ESLint documentation.

## Bonus Tip: Fix all auto-fixable problems

ESLint has a useful `--fix` option that can be supplied to the command when run in the terminal. Similar to the `--write` option in Prettier, this will attempt to fix the issues ESLint discovers and save these against the original file.

This functionality is also available inside Visual Studio Code. Open the command palette (Cmd-Shift-P) and type `fix` and you should be given the option `ESLint: Fix all auto-fixable problems`.

The important takeaway is that ESLint cannot fix all of the issues it discovers and using `--fix` or the extension's command will only fix those which ESLint is capable of automatically fixing.

## Conclusion

We have just integrated both Prettier and ESLint into Visual Studio Code. More importantly we have ensured that Prettier controls all format linting and ESLint concentrates on detecting code problems.

Now go write beautiful code!