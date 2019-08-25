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
## Starting project for web testing with **WebDriver**

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

- Inform the URL for the web tests https://precodahora.pb.gov.br;

- Confirm the suggested answer (chrome) to set browser for web tests;

- In the `codecept.conf.js` file override the `WebDriver` helper with to the code below:
  ```
  helpers: {
    WebDriver: {
      url: 'https://precodahora.pb.gov.br',
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

- Enter the test name: `search_prices`;

- Enter the feature name: `Search Prices`;

- Override the created `tests/search_prices_test.js` file, with to the code below:
  ```
  const { MAX_WAIT } = require('../consts.js')
  const { I } = inject()

  Feature('Search Prices')

  Scenario('Etanol - 810101001', async () => {
    product_name = 'Etanol'
    product_id = '810101001'
    I.amOnPage('/')
    I.click('#fake-sbar')
    I.fillField('#top-sbar', product_name)
    I.waitForElement({ xpath: '//li[text()="Você quis dizer:"]/../li[2]' }, MAX_WAIT)
    I.click({ xpath: '//li[text()="Você quis dizer:"]/../li[2]' })
    I.waitForElement({ xpath: '//div[@class="list-info mt-2 mb-2"]/h6[2]' }, MAX_WAIT)
    I.seeTextEquals('Você buscou produtos com código de barras ' + product_id + '. Exibindo 25 resultados.', { xpath: '//div[@class="list-info mt-2 mb-2"]/h6[2]' })
    I.waitForElement({ xpath: '//div[@class="media-flex item-list"][1]//img[@src="https://apiprecos.tce.pb.gov.br/images/810101001"]' }, MAX_WAIT)
    I.see(product_name.toUpperCase(), { xpath: '//div[@class="media-flex item-list"][1]/div[2]/div[1]/strong' })
  })
  ```
- Run test -> `./cc run`;

- For step by step execution: `./cc run --steps`;

- For more details execution: `./cc run --verbose`.

## Adding Page Object pattern in project

### Adding **initial page**

- At the terminal execute: `./cc gpo`;

- Answer `initial_page` to map the initial page;

- Confirm the suggested path `./pages/initial_page.js`;

- In `codecept.conf.js` file add from `include` to the code below:
  ```
  initial_page: './pages/initial_page.js'
  ```
- Override `initial_page.js` file with to the code below:
  ```
  const { I } = inject();
  const { MAX_WAIT } = require('../consts.js')

  module.exports = {

    fields: {
      enable_search_product: '#fake-sbar',
      search_product: '#top-sbar'
    },

    links: {
      first_search_result: { xpath: '//li[text()="Você quis dizer:"]/../li[2]' }
    },

    search_product(product) {
      I.click(this.fields.enable_search_product)
      I.fillField(this.fields.search_product, product)
      I.waitForElement(this.links.first_search_result, MAX_WAIT)
      I.click(this.links.first_search_result)
    }
  }
  ```
### Adding **products page**

- At the terminal execute: `./cc gpo`;

- Answer `products_page` to map the page of found products;

- Confirm the suggested path `./pages/products_page.js`;

- In `codecept.conf.js` file add from `include` to the code below:
  ```
  products_page: './pages/products_page.js'
  ```
- Override `products_page.js` file with to the code below:
  ```
  const { I } = inject();
  const { MAX_WAIT } = require('../consts.js')

  module.exports = {

    labels: {
      amount_results: { xpath: '//div[@class="list-info mt-2 mb-2"]/h6[2]' },
      title_first_record: { xpath: '//div[@class="media-flex item-list"][1]/div[2]/div[1]/strong' }
    },

    images: {
      etanol: { xpath: '//div[@class="media-flex item-list"][1]//img[@src="https://apiprecos.tce.pb.gov.br/images/810101001"]' }
    },

    check_search_result(id, name) {
      I.waitForElement(this.labels.amount_results, MAX_WAIT)
      I.seeTextEquals('Você buscou produtos com código de barras ' + id + '. Exibindo 25 resultados.', this.labels.amount_results)
      I.waitForElement(this.images.etanol, MAX_WAIT)
      I.see(name.toUpperCase(), this.labels.title_first_record)
    }
  }
  ```

### Refactoring **tests/search_prices_test.js**

  - Override `tests/search_prices_test.js` file with to the code below:
  ```
  const { I, initial_page, products_page } = inject()

  Feature('Search Prices')

  Scenario('Etanol - 810101001', async () => {
    product_name = 'Etanol'
    product_id = '810101001'
    I.amOnPage('/')
    initial_page.search_product(product_name)
    products_page.check_search_result(product_id, product_name)
  })
  ```
- Run test -> `./cc run`;

- For step by step execution: `./cc run --steps`;

- For more details execution: `./cc run --verbose`.

## Adding mobile test with **Appium**

### Prerequisite: [JDK and JAVA_HOME configurated](https://thishosting.rocks/install-java-ubuntu/), [SDK](https://developer.android.com/studio) and [ANDROID_HOME](https://itrendbuzz.com/set-path-environment-variable-for-android-sdk-in-ubuntu/) configurated, and [APPIUM](http://appium.io/) installed

- Start a virtual device in Android Studio, or connect your device via USB. In this example, let's use Android 9;

- Start appium in terminal `appium`;

- Output in terminal:
  ```
  [Appium] Welcome to Appium v1.14.1
  [Appium] Appium REST http interface listener started on 0.0.0.0:4723
  ```

- Add the code below in `codecept.conf.js` file and **comment helper Webdriver**:
  ```
  Appium: {
      app: '<path_project>/apks/preco_da_hora.apk',
      platform: 'Android',
      device: '0052204890', // list your device code running "adb devices" in terminal
      desiredCapabilities: {
        'platformVersion': '9', // check your android version
        'platformName': 'Android',
        'deviceName': 'Anyname',
        'appPackage': 'br.gov.pb.precodahora',
        'appActivity': 'br.gov.pb.precodahora.MainActivity',
        'autoGrantPermissions': true
      }
    }
  ```
## Refactoring pages for mobile platform execution

### Refactoring **initial page** for mobile test

- Override `initial_page.js` file with to the code below:
  ```
  const { I } = inject();
  const { MAX_WAIT } = require('../consts.js')

  module.exports = {

    // locators web
    fields: {
      enable_search_product: '#fake-sbar',
      search_product: '#top-sbar'
    },

    links: {
      first_search_result: { xpath: '//li[text()="Você quis dizer:"]/../li[2]' }
    },
    
    // locators android
    android_fields: {
      enable_field_search: 'Pesquise aqui...',
      search_product: 'Palavra-chave, código de barras ou CNPJ',
      first_search_result: { xpath: '//android.widget.ImageView[@index="4"]' }
    },

    android_links: {
      first_search_result: 'ETANOL HIDRATADO COMUM'
    },

    search_product(product) {
      I.runInWeb(() => {
        I.click(this.fields.enable_search_product)
        I.fillField(this.fields.search_product, product)
        I.waitForElement(this.links.first_search_result, MAX_WAIT)
        I.click(this.links.first_search_result)
      })

      I.runOnAndroid(() => {
        I.waitForElement(this.android_fields.enable_field_search)
        I.tap(this.android_fields.enable_field_search)
        I.fillField(this.android_fields.search_product, product)
        I.waitForElement(this.android_links.first_search_result, MAX_WAIT)
        I.tap(this.android_links.first_search_result)
      })
    }
  }

  ```
### Refactoring **products page** for mobile test

- Override `apps_details_page.js` file with to the code below:
  ```
  const { I } = inject();
  const { MAX_WAIT } = require('../consts.js')

  module.exports = {

    // locators web
    labels: {
      amount_results: { xpath: '//div[@class="list-info mt-2 mb-2"]/h6[2]' },
      title_first_record: { xpath: '//div[@class="media-flex item-list"][1]/div[2]/div[1]/strong' }
    },

    images: {
      etanol: { xpath: '//div[@class="media-flex item-list"][1]//img[@src="https://apiprecos.tce.pb.gov.br/images/810101001"]' }
    },

    // locators android
    android_labels:{
      title_first_record: { xpath: '//android.widget.ImageView[@instance="4"]' }
    },

    check_search_result(id, name) {
      I.runInWeb(() => {
        I.waitForElement(this.labels.amount_results, MAX_WAIT)
        I.seeTextEquals('Você buscou produtos com código de barras ' + id + '. Exibindo 25 resultados.', this.labels.amount_results)
        I.waitForElement(this.images.etanol, MAX_WAIT)
        I.see(name.toUpperCase(), this.labels.title_first_record)
      })

      I.runOnAndroid(() => {
        I.waitForElement(this.android_labels.title_first_record, MAX_WAIT)
        I.see(name.toUpperCase(), this.android_labels.title_first_record)
      })
    }
  }
  ```
### Refactoring **tests/search_prices_test.js** for mobile test

- Override `tests/search_prices_test.js` file with to the code below:
  ```
  const { I, initial_page, products_page, modal_important } = inject()

  Feature('Search Prices')

  Before( async () => {
    I.runInWeb(() => {
      I.amOnPage('/')
    })
  })

  Scenario('Etanol - 810101001', async () => {
    product_name = 'Etanol'
    product_id = '810101001'
    modal_important.close_modal_important()
    initial_page.search_product(product_name)
    products_page.check_search_result(product_id, product_name)
  })
  ```

## Running on multiple platforms (with npm)

- In the `package.json` file add to the code below:
  ```
  "scripts": {
    "web": "codeceptjs run -c codecept.conf.js --override '{\"helpers\": { \"WebDriver\": { \"url\": \"https://precodahora.pb.gov.br\", \"browser\": \"chrome\", \"host\": \"127.0.0.1\", \"port\": 4444, \"restart\": \"false\", \"windowSize\": \"1920x1680\", \"desiredCapabilities\": { \"chromeOptions\": { \"args\": [ \"--disable-gpu\", \"--window-size=1200,1000\", \"--no-sandbox\" ]}}}}} ' --steps",
    "android": "codeceptjs run -c codecept.conf.js --override '{\"helpers\": { \"Appium\": { \"app\": \"<path_your_project>/apks/preco_da_hora.apk\", \"platform\": \"Android\", \"device\": \"<list_your_device_code_running_"adb devices"_in_terminal>\", \"desiredCapabilities\": { \"platformVersion\": \"check_your_android_version\", \"platformName\": \"Android\", \"deviceName\": \"Anyname\", \"appPackage\": \"br.gov.pb.precodahora\", \"appActivity\": \"br.gov.pb.precodahora.MainActivity\", \"autoGrantPermissions\": true }}}}' --steps"
  }
  ```
- At the terminal, run `npm run android & npm run web` for web and android execution;

- Run `npm run android` only Android running or `npm run web` for web running.

### For more information visit [Codeceptjs](https://codecept.io) official website. o/
