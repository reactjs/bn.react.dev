---
title: এডিটর সেটাপ
---

<Intro>

সঠিকভাবে কনফিগার করা এডিটর আপনার কোড পড়ায় এবং লেখায় সাহায্য করতে পারে। এমনকি আপনাকে লিখার সাথে সাথে ভুলত্রুটি ধরায়ও সাহায্য করতে পারে! আপনি যদি প্রথমবারের মত এডিটর সেটাপ করেন অথবা আপনার বর্তমান এডিটরকে আরও সমৃদ্ধ করে তুলতে চান, তাহলে আমাদের কিছু রিকমেন্ডেশন রয়েছে।

</Intro>

<YouWillLearn>

* সবচেয়ে জনপ্রিয় এডিটরগুলো কি কি
* কিভাবে আপনার কোড স্বয়ংক্রিয়ভাবেই ফরম্যাট করবেন

</YouWillLearn>

## আপনার এডিটর {/*your-editor*/}

[VS Code](https://code.visualstudio.com/) বর্তমানে ব্যবহৃত সবচেয়ে জনপ্রিয় এডিটরগুলোর মধ্যে একটি। এর মধ্যে বিশাল এক্সটেনশন মার্কেটপ্লেস রয়েছে এবং এটি সহজেই GitHub এর মত জনপ্রিয় সার্ভিসগুলোর সাথে সংযুক্ত হতে পারে। নিম্নোক্ত অধিকাংশ জিনিসই VS Code এ এক্সটেনশন হিসেবে যুক্ত করা যায়, যা একে আরও বেশি কনফিগার করতে সহায়তা করে।

React কমিউনিটিতে ব্যবহৃত অন্য জনপ্রিয় এডিটরগুলোঃ

* [WebStorm](https://www.jetbrains.com/webstorm/) is an integrated development environment designed specifically for JavaScript.
* [Sublime Text](https://www.sublimetext.com/) has support for JSX and TypeScript, [syntax highlighting](https://stackoverflow.com/a/70960574/458193) and autocomplete built in.
* [Vim](https://www.vim.org/) is a highly configurable text editor built to make creating and changing any kind of text very efficient. It is included as "vi" with most UNIX systems and with Apple OS X.

## Recommended text editor features {/*recommended-text-editor-features*/}

Some editors come with these features built in, but others might require adding an extension. Check to see what support your editor of choice provides to be sure!

### Linting {/*linting*/}

Code linters find problems in your code as you write, helping you fix them early. [ESLint](https://eslint.org/) is a popular, open source linter for JavaScript. 

* [Install ESLint with the recommended configuration for React](https://www.npmjs.com/package/eslint-config-react-app) (be sure you have [Node installed!](https://nodejs.org/en/download/current/))
* [Integrate ESLint in VSCode with the official extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint)

**Make sure that you've enabled all the [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks) rules for your project.** They are essential and catch the most severe bugs early. The recommended [`eslint-config-react-app`](https://www.npmjs.com/package/eslint-config-react-app) preset already includes them.

### Formatting {/*formatting*/}

The last thing you want to do when sharing your code with another contributor is get into an discussion about [tabs vs spaces](https://www.google.com/search?q=tabs+vs+spaces)! Fortunately, [Prettier](https://prettier.io/) will clean up your code by reformatting it to conform to preset, configurable rules. Run Prettier, and all your tabs will be converted to spaces—and your indentation, quotes, etc will also all be changed to conform to the configuration. In the ideal setup, Prettier will run when you save your file, quickly making these edits for you.

You can install the [Prettier extension in VSCode](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) by following these steps:

1. Launch VS Code
2. Use Quick Open (press Ctrl/Cmd+P)
3. Paste in `ext install esbenp.prettier-vscode`
4. Press Enter

#### Formatting on save {/*formatting-on-save*/}

Ideally, you should format your code on every save. VS Code has settings for this!

1. In VS Code, press `CTRL/CMD + SHIFT + P`.
2. Type "settings"
3. Hit Enter
4. In the search bar, type "format on save"
5. Be sure the "format on save" option is ticked!

> If your ESLint preset has formatting rules, they may conflict with Prettier. We recommend disabling all formatting rules in your ESLint preset using [`eslint-config-prettier`](https://github.com/prettier/eslint-config-prettier) so that ESLint is *only* used for catching logical mistakes. If you want to enforce that files are formatted before a pull request is merged, use [`prettier --check`](https://prettier.io/docs/en/cli.html#--check) for your continuous integration.
