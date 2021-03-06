# Thingweb-Playground Assertions

This package provides the assertion testing functionality for the Web of Things Playground.
You can find more information about the Thingweb-Playground [here](https://github.com/thingweb/thingweb-playground).

Validation tool for W3C WoT Thing Descriptions. Your Thing Descriptions should be written according to the W3C standard found [here](https://w3c.github.io/wot-thing-description/#).

Limitations:  

* There is limited nested validation. This is due to the JSON Schema specification which doesn't allow infinite references with $ref keywords. Because of this, an enum key in a e.g. #/actions/input/properties/enum will not be necessarily checked. More information can be found [here](http://json-schema.org/draft/2019-09/json-schema-core.html#rfc.section.8.2.4.3).

## Usage

You can use this package to integrate TD assertion testing in your own Browser/NPM application.

* Install this package via NPM (`npm install playground-assertions`) (or clone repo and install the package with `npm install`)
* Node.js or Browser import:
  * Node.js: Require the package and use the assertion Testing function

  ```javascript
  const tdAssertions = require("playground-assertions")
  ```

  * Browser: Import the `tdAssertions` function as a global by adding a script tag to your html.

  ```html
  <script src="./node_modules/playground-assertions/dist/web-bundle.min.js"></script>
  ```

* Now you can call the assertion testing function and handle the result.

```javascript
tdAssertions([simpleTD], fileLoad, /*OPT: log Function*/, /*OPT: manual report*/)
.then( result => {
  console.log("OKAY")
  console.log(result)
}, err => {
  console.log("ERROR")
  console.error(err)
})
```

## Structure

* [index.js](./index.js) contains the package import/export structure, application flow (assertion testing, merging, print stats), and handles loading of the assertion schemas
* [assertionTests.js](./assertionTests.js) contains the actual assertion testing functionalities
* [checkCoverage.js](./checkCoverage.js) can output stats about a given assertion testing report
* [list.json](./list.json) a list of all filenames in the [assertions](./assertions) folder. Required, for browser usage of the package (since no access to ddirectory content exists there).
* [manual.csv](./manual.csv) example of a manual report part. Used if no other file is specified as manual assertion report.
* [mergeResults.js](./mergeResults.js) can merge several assertion testing reports to one single report, representing the assertions results of an whole implementation instead of single TDs.
* [update-list.js](./update-list.js) Has to be executed after applying changes of files in the [assertions](./assertions) directory. Updates the [list.json](./list.json) with the current filenames.

## License

Dual-licensed under both

* [Eclipse Public License v. 2.0](http://www.eclipse.org/legal/epl-2.0)
* [W3C Software Notice and Document License (2015-05-13)](https://www.w3.org/Consortium/Legal/2015/copyright-software-and-document)

Pick one of these two licenses that fits your needs.
Please also see the additional [notices](NOTICE.md) and [how to contribute](CONTRIBUTING.md).

## Script based Thing Description Validation

This is a Node.js based tool.

* You can use `playground-core` package as an API to validate TDs in your own packages.
* You can use the `playground-cli` package to test one/multiple TDs via the command line.
* You can use this package to integrate assertion testing via an API in your own packages.
* You can use the `playground-web` package to host/adapt your own browser version of the WoT playground. Remember you need to deliver its files by a web server (also locally possible via localhost), simply opening the `index.html` with a browser won't do the job.

## Script based Assertion Tester

297 out of 349 assertions of the TD specification can be tested with this tool.

This tool checks which assertions are satisfied by a given Thing Description(s). The assertions are modeled as JSON Schema or as scripts. 'AssertionTester/Assertions' has the JSON Schema assertions.

To use the assertion testing via the command line please use the `playground-cli` package.

**WARNING**: If you see an error like `ajv.errors[0].params.allowedValue` this very probably means that your TD is not valid at a specific point. Scroll up to see the precise error message

### Contributing

You can contribute by providing new JSON Schemas for assertions or by correcting them. There are two types of assertions:

* Not-complex: This is generally used to check assertions that are in the Thing instance or mandatory assertions. You simply put the JSON key to be checked in the `required` validation keyword.
* Complex: This type uses the `if-then-else` structure of JSON Schema Draft 7. You should put an `if` for the keyword you want to check and the following object in the `then`:

  ```json
    "then": {
        "const": "td-data-schema_description=pass"
    }
  ```

  This way, the validation will surely fail at the const keyword and display that the JSON data has to be `"td-data-schema_description=pass"` string. This will be then detected by the assertion testing tool which will look for the `=` sign to find the result. If the schema doesn't fail, it implies that this if was false, which in turn implies that the assertion you wanted to test was not implemented in the given TD.

## Known Bugs

* td-json-open assertion exists multiple times, [see issue 124](https://github.com/thingweb/thingweb-playground/issues/124)
