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

- For more details execution: `./cc run --verbose`.

## Adding Page Object pattern in project

### Adding initial Google page

- In the terminal execute: `./cc gpo`;

- Answer `google_initial_page` to map the google initial page;

- Confirm the suggested path `./pages/google_initial_page.js`;

- In `codecept.conf.js` file add from `include` to the code below:
  ```
  google_initial_page: './pages/google_initial_page.js'
  ```
- In `google_initial_page.js` add from `module.exports` to the code below:
  ```
  fields: {
    search_google: 'input[name=q]'
  },

  buttons: {
    search_google: 'input[name=btnK]'
  },

  search_google(values) {
    I.fillField(this.fields.search_google, values)
    I.click(this.buttons.search_google)
  }
  ```
### Adding search result Google page

- In the terminal execute: `./cc gpo`;

- Answer `search_result_google_page` to map the google initial page;

- Confirm the suggested path `./pages/search_result_google_page.js`;

- In `codecept.conf.js` file add from `include` to the code below:
  ```
  search_result_google_page: './pages/search_result_google_page.js'
  ```
- In `search_result_google_page.js` add from `module.exports` to the code below:
  ```
  elements: {
    result_latency: '#resultStats'
  },

  click_link_result(link) {
    I.waitForVisible(this.elements.result_latency, 10) // wait up to ten seconds
    I.click(link)
  }
  ```

### Adding CodeceptJS QuickStart page

- In the terminal execute: `./cc gpo`;

- Answer `codeceptjs_quickstart_page` to map the google initial page;

- Confirm the suggested path `./pages/codeceptjs_quickstart_page.js`;

- In `codecept.conf.js` file add from `include` to the code below:
  ```
  codeceptjs_quickstart_page: './pages/codeceptjs_quickstart_page.js'
  ```
- In `codeceptjs_quickstart_page` add from `module.exports` to the code below:
  ```
  labels: {
    title_page: { xpath: '(//h1)[1]' }
  },

  check_title_name(title_name) {
    I.waitForVisible(this.labels.title_page, 10)
    I.seeTextEquals(title_name, this.labels.title_page)
  }
  ```

### Refactoring tests/first_example_test.js

  - Override `tests/first_example_test.js` file with to the code below:
    ```
    Feature('Google Search CodeceptJS QuickStart');

    let url = 'https://google.com'

    Scenario('test something', (I, google_initial_page, search_result_google_page, codeceptjs_quickstart_page) => {
      I.amOnPage(url)
      google_initial_page.search_google('codeceptjs')
      search_result_google_page.click_link_result('Quickstart')
      codeceptjs_quickstart_page.check_title_name('Quickstart')
      I.wait(3)
    });
    ```
- Run test -> `./cc run`;

- For step by step execution: `./cc run --steps`;

- For more details execution: `./cc run --verbose`.

## Debuging tests

- Test execution can be paused in any place of a test with `pause()` call.

- Add command after opening the page:
    ```
    Feature('Google Search CodeceptJS QuickStart');

    let url = 'https://google.com'

    Scenario('test something', (I, google_initial_page, search_result_google_page, codeceptjs_quickstart_page) => {
      I.amOnPage(url)
      pause()
      google_initial_page.search_google('codeceptjs')
      search_result_google_page.click_link_result('Quickstart')
      codeceptjs_quickstart_page.check_title_name('Quickstart')
      I.wait(3)
    });
    ```
- In the terminal will display the following options:
    ```
    Interactive shell started
    Use JavaScript syntax to try steps in action
    - Press ENTER to run the next step
    - Press TAB twice to see all available commands
    - Type exit + Enter to exit the interactive shell
    ```
- If a test is failing you can prevent browser from closing by putting `pause()` command into `After()` hook.
    ```
    After(pause)
    ```
## Adding Comments

- There is a simple way to add additional comments to your test scenario. Use say command to print information to screen.
  ```
  Feature('Google Search CodeceptJS QuickStart');

  let url = 'https://google.com'

  Scenario('test something', (I, google_initial_page, search_result_google_page, codeceptjs_quickstart_page) => {
    I.amOnPage(url)
    // pause()
    google_initial_page.search_google('codeceptjs')
    search_result_google_page.click_link_result('Quickstart')
    I.say('Search \'QuickStart\' Found', 'magenta') // without second argument, will print the default color (cyan)
    codeceptjs_quickstart_page.check_title_name('Quickstart')
    I.wait(3)
  });
  ``` 
- Run with `--steps` (`./cc run --steps`).

### For more information visit [Codeceptjs](https://codecept.io) official website. o/