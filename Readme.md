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
## Starting project for web testing - Option 1 -> Puppeteer

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
- For begin your project execute: `./cc init`;

- Answer the first ask with: `./tests/*.js`;

- Confirm the suggested answer, for directory where should save log, reports and screenshots (`./output`);

- Answer `Y` for extend the object **I** in custom steps;

- Confirm the suggested answer to set the location of custom steps (`./steps_file.js`);

- Choose localization for tests;

- Inform the URL for the web tests https://google.com, for example;

- Answer `Y` to display browser show during run tests;

- In the `codecept.conf.js` file override the `Puppeteer` helper with to the code below:
  ```
  helpers: {
    Puppeteer: {
      url: 'https://google.com',
      browser: 'chrome',
      windowSize: '1366x768x24',
      show: true, // false for headless mode
      chrome: {
        args: ['--window-size=1366,768', '--no-sandbox'],
      },
    },
  },
  ```

## Starting project for web testing - Option 2 -> WebDriver

- Execute `npm i @wdio/selenium-standalone-service --save-dev` for add the **WebDriverIo** dependecy in your **package.json** file. This dependency is necessary for simulate users actions in the browser;
  ```
  {
    "name": "Codeceptjs",
    "dependencies": {
      "codeceptjs": "^2.3.0"
    },
    "devDependencies": {
      "@wdio/selenium-standalone-service": "^5.12.1"
    }
  }
  ```
- For begin your project execute: `./cc init`;

- Answer the first ask with: `./tests/*.js`;

- Confirm the suggested answer, for directory where should save log, reports and screenshots (`./output`);

- Answer `Y` for extend the object **I** in custom steps;

- Confirm the suggested answer to set the location of custom steps (`./steps_file.js`);

- Choose localization for tests;

- Inform the URL for the web tests https://google.com, for example;

- Confirm the suggested answer (chrome) to set browser for web tests;

- In the `codecept.conf.js` file override the `WebDriver` helper with to the code below:
  ```
  helpers: {
    WebDriver: {
      url: 'https://google.com',
      browser: 'chrome',
      host: '127.0.0.1',
      port: 4444,
      restart: false,
      windowSize: '1920x1680',
      desiredCapabilities: {
        // descomment --headless argument for execution without browser display
        chromeOptions: {
          args: [ /* "--headless", */ "--disable-gpu", "--window-size=1200,1000", "--no-sandbox" ]
        }
      }
    }
  },
  // Enable it in config inside plugins section
  plugins: {
    wdio: {
      enabled: true,
      services: ['selenium-standalone']
    }
  },
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
    I.click('Quickstart')
    I.wait(3)
  });
  ```
- Run test -> `./cc run`;

- For step by step execution: `./cc run --steps`;

- For more information visit [Codeceptjs](https://codecept.io) official website. o/