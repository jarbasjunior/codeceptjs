# Initial config the project for web tests with Codeceptjs

## Prerequisite: Installed [NPM Package](https://nodejs.org/en/download/package-manager/)

### Example to install on Ubuntu below:

- **Node.js v12.x:**
  ```
  # Using Ubuntu
  curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -
  sudo apt-get install -y nodejs

  # Using Debian, as root
  curl -sL https://deb.nodesource.com/setup_12.x | bash -
  apt-get install -y nodejs
  ```
- To check the installed version run in terminal: `node -v`;

## Initial config

- Execute the command -> `npm install codeceptjs --save`;

- Execute `npm install puppeteer --save` for add the **Puppeteer** dependecy in your **package.json** file. This dependency is necessary for simulate users actions in the browser;
  ```
  {
    "name": "your_project",
    "dependencies": {
      "codeceptjs": "^2.2.1",
      "puppeteer": "^1.19.0"
    }
  }
  ```

- In terminal execute: `chmod +x cc`;

- In terminal execute: `./cc` for list all codeceptjs actions:
  ```
  CodeceptJS v2.2.1
  Usage:  [options] [command]

  Options:
    -h, --help                                      output usage information

  Commands:
    init [path]                                     Creates dummy config in current dir or [path]
    migrate [path]                                  Migrate json config to js config in current dir or [path]
    shell|sh [options] [path]                       Interactive shell
    list|l [path]                                   List all actions for I.
    def [options] [path]                            Generates TypeScript definitions for all I actions.
    gherkin:init|bdd:init [options] [path]          Prepare CodeceptJS to run feature files.
    gherkin:steps|bdd:steps [options] [path]        Prints all defined gherkin steps.
    gherkin:snippets|bdd:snippets [options] [path]  Generate step definitions from steps.
    generate:test|gt [path]                         Generates an empty test
    generate:pageobject|gpo [path]                  Generates an empty page object
    generate:object|go [options] [path]             Generates an empty support object (page/step/fragment)
    generate:helper|gh [path]                       Generates a new helper
    run [options] [test]                            Executes tests
    run-multiple [options] [suites...]              Executes tests multiple
  ```
## Starting project for web testing with Puppeteer

- For begin your project execute: `./cc init`;

- Answer the first ask with: `./tests/*.js`;

- In this example we will use **Puppeteer**, choose this option;

- Confirm the suggested answer, for directory where should save log, reports and screenshots (`./output`);

- Answer `Y` for extend the object **I** in custom steps;

- Confirm the suggested answer to set the location of custom steps (`./steps_file.js`);

- Choose localization for tests;

- Inform the URL for the web tests https://google.com, for example;

- Answer `Y` to display browser show during run tests;

- In the terminal type it `l` and `enter`. And check if the output conform to the code below:
  ```
  total 148K
  drwxrwxr-x   5 jarbas jarbas 4,0K ago  5 00:36 .
  drwxrwxr-x  20 jarbas jarbas 4,0K ago  5 00:22 ..
  -rwxrwxr-x   1 jarbas jarbas   44 ago  5 00:28 cc
  -rw-rw-r--   1 jarbas jarbas  272 ago  5 00:36 codecept.conf.js
  -rw-rw-r--   1 jarbas jarbas   49 ago  5 00:36 jsconfig.json
  drwxrwxr-x 231 jarbas jarbas  12K ago  5 00:25 node_modules
  drwxrwxr-x   2 jarbas jarbas 4,0K ago  5 00:36 output
  -rw-rw-r--   1 jarbas jarbas  109 ago  5 00:25 package.json
  -rw-rw-r--   1 jarbas jarbas  83K ago  5 00:25 package-lock.json
  -rw-rw-r--   1 jarbas jarbas  14K ago  5 00:36 steps.d.ts
  -rw-rw-r--   1 jarbas jarbas  267 ago  5 00:36 steps_file.js
  drwxrwxr-x   2 jarbas jarbas 4,0K ago  5 00:31 tests 
  ```

## Create test example

- Run on the terminal the command for generate test: `./cc gt`;

- Enter the test name: `first_example`;

- Enter the feature name: `Search Google`;

- Override the created `tests/first_example_test.js` file, with to the code below:
  ```
  Feature('Search Google');

  let url = 'https://google.com'

  Scenario('test something', (I) => {
    I.amOnPage(url)
    I.fillField('input[name=q]', 'codeceptjs')
    I.click('input[name=btnK]')
    I.waitForVisible('#resultStats', 10) // wait up to ten seconds
    I.clickLink('Quickstart')
    I.wait(3)
  });
  ```
- Run test -> `./cc run`;

- For step by step execution: `./cc run --steps`;

- For more information visit [Codeceptjs](https://codecept.io) official website. o/