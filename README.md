# agent-js-jasmine
[![Build Status](https://travis-ci.org/reportportal/agent-js-jasmine.svg?branch=master)](https://travis-ci.org/reportportal/agent-js-jasmine)[![Code Coverage](https://codecov.io/gh/reportportal/agent-js-jasmine/branch/master/graph/badge.svg)](https://codecov.io/gh/reportportal/agent-js-jasmine)[![npm version](https://badge.fury.io/js/agent-js-jasmine.svg)](https://badge.fury.io/js/agent-js-jasmine)

Agent for integration Jasmine with ReportPortal.     
[ReportPortal](http://reportportal.io/)<br>
[ReportPortal on GitHub](https://github.com/reportportal)

### How to use
1. Install the agent in your project:
```cmd
npm i agent-js-jasmine --save-dev
```
2. Create an agent instance:
```javascript
var ReportportalAgent = require('agent-js-jasmine');

var agent = new ReportportalAgent({
    // client settings
    token: "00000000-0000-0000-0000-000000000000",
    endpoint: "http://your.reportportal.server/api/v1",
    launch: "LAUNCH_NAME",
    project: "PROJECT_NAME",
    // agent settings
    attachPicturesToLogs: true,
    attributes: [
        {
            "key": "YourKey",
            "value": "YourValue"
        },
        {
            "value": "YourValue"
        },
    ]
});
```
3. Add a reporter to Jasmine:
```javascript
jasmine.addReporter(agent.getJasmineReporter());
```
4. After Jasmine has completed its work, wait until the end of the agent's work:
```javascript
agent.getExitPromise().then(() => {
    console.log('finish work');
})
```

### Settings
Agent settings consist of two parts:
* Client settings can be viewed [here](https://github.com/reportportal/client-javascript#settings)
* Agent settings are described below

Parameter | Description
--------- | -----------
attachPicturesToLogs | It is 'true' or 'false', if set 'true' then attempts will be made to attach screenshots to the logs. Default: 'true'.
reportHooks           | *Default: false.* Determines report before and after hooks or not. 

To report [rerun](https://github.com/reportportal/documentation/blob/master/src/md/src/DevGuides/rerun.md) to the report portal you need to specify the following options:

Parameter | Description
--------- | -----------
rerun | to enable rerun
rerunOf | UUID of launch you want to rerun. If not specified, report portal will update the latest launch with the same name

Example:

```json
  "rerun": true,
  "rerunOf": "f68f39f9-279c-4e8d-ac38-1216dffcc59c"
```

### Additional reporting functionality

The agent provides an API to extend the functionality of Jasmine.

Add PublicReportingAPI into your test file to use additional reporting features.

```javascript
const PublicReportingAPI = require('agent-js-jasmine/lib/publicReportingAPI');
```

#### Report static attributes, description
Inside of your suite or spec call PublicReportingAPI.setDescription(), PublicReportingAPI.addAttributes()

**setDescription** method inside of your **suite**

Parameter | Required | Description | Examples
--------- | ----------- | ----------- | -----------
description | true | "string" - text description for your suite | "Your description"
suite | true | "string" - description of your suite (all suite descriptions must be unique) | "Suite"

**addAttributes** method inside of your **suite**

Parameter | Required | Description | Examples
--------- | ----------- | ----------- | -----------
attributes | true | attributes, pairs of key and value | [{ "key": "YourKey", "value": "YourValue" }]
suite | true | "string" - description of your suite (all suite descriptions must be unique) | "Suite"

**setDescription** method inside of your **spec**

Parameter | Required | Description | Examples
--------- | ----------- | ----------- | -----------
description | true | "string" - text description for your suite | "Your description"

**addAttributes** method inside of your **spec**

Parameter | Required | Description | Examples
--------- | ----------- | ----------- | -----------
attributes | true | attributes, pairs of key and value | [{ "key": "YourKey", "value": "YourValue" }]

#### Report logs and attachments
PublicReportingAPI provides the following methods for reporting logs into the current suite/spec.

* log(*level*, *message* , *file*, *suite*). Reports *message* and optional *file* with specified log *level* as a log of the current suite/spec.<br/>
*level* should be equal to one the following values: *TRACE*, *DEBUG*, *INFO*, *WARN*, *ERROR*, *FATAL*.<br/>
*suite* it's description of your suite (all suite descriptions must be unique) ***REQUIRED INSIDE OF YOUR SUITE, OPTIONAL FOR SPEC*** <br/>
*file* should be an object (***REQUIRED INSIDE OF YOUR SUITE,*** if there is no file object, set ***NULL***) : <br/>
```javascript
{
  name: "filename",
  type: "image/png",  // media type
  content: data,  // file content represented as 64base string
}
```
* trace (*message* , *file*, *suite*). Reports *message* and optional *file* as a log of the current suite/spec with trace log level.
* debug (*message* , *file*, *suite*). Reports *message* and optional *file* as a log of the current suite/spec with debug log level.
* info (*message* , *file*, *suite*). Reports *message* and optional *file* as log of the current suite/spec with info log level.
* warn (*message* , *file*, *suite*). Reports *message* and optional *file* as a log of the current suite/spec with warning log level.
* error (*message* , *file*, *suite*). Reports *message* and optional *file* as a log of the current suite/spec with error log level.
* fatal (*message* , *file*, *suite*). Reports *message* and optional *file* as a log of the current suite/spec with fatal log level.

PublicReportingAPI provides the corresponding methods for reporting logs into the launch.
* launchLog (*level*, *message* , *file*). Reports *message* and optional *file* with the specified log *level* as a log of the current launch.
* launchTrace (*message* , *file*). Reports *message* and optional *file* as a log of the launch with trace log level.
* launchDebug (*message* , *file*). Reports *message* and optional *file* as a log of the launch with debug log level.
* launchInfo (*message* , *file*). Reports *message* and optional *file* as log of the launch with info log level.
* launchWarn (*message* , *file*). Reports *message* and optional *file* as a log of the launch with warning log level.
* launchError (*message* , *file*). Reports *message* and optional *file* as a log of the launch with error log level.
* launchFatal (*message* , *file*). Reports *message* and optional *file* as a log of the launch with fatal log level.

**Example:**
```javascript
const PublicReportingAPI = require('agent-js-jasmine/lib/publicReportingAPI');

describe('A suite', function() {
    const suiteAttachment = {
      name: 'attachment.png',
      type: 'image/png',
      content: data.toString('base64'),
    }
    PublicReportingAPI.addAttributes([{
        key: 'suiteKey',
        value: 'suiteValue',
    }], 'A suite');
    PublicReportingAPI.setDescription('Suite description', 'A suite');
    PublicReportingAPI.debug('Debug log message for suite "suite"', null, 'A suite');
    PublicReportingAPI.info('Info log message for suite "suite"', suiteAttachment, 'A suite');
    PublicReportingAPI.warn('Warning for suite "suite"', null, 'A suite');
    PublicReportingAPI.error('Error log message for suite "suite"', null, 'A suite');
    PublicReportingAPI.fatal('Fatal log message for suite "suite"', suiteAttachment, 'A suite');

    it('spec', function() {
        const specAttachment = {
          name: 'attachment.png',
          type: 'image/png',
          content: data.toString('base64'),
        }
        PublicReportingAPI.addAttributes([{
            key: 'specKey',
            value: 'specValue'
        }]);
        PublicReportingAPI.setDescription('Spec description');
        PublicReportingAPI.log('INFO', 'Info log message for spec "spec" with attachment', specAttachment);
        PublicReportingAPI.launchLog('ERROR', 'Error log message for current launch with attachment', specAttachment);
        PublicReportingAPI.trace('Trace log message for spec "spec"', specAttachment);
        PublicReportingAPI.debug('Debug log message for spec "spec"');
        PublicReportingAPI.info('Info log message for spec "spec" with attachment');
        PublicReportingAPI.warn('Warning for spec "spec"');
        PublicReportingAPI.error('Error log message for spec "spec"');
        PublicReportingAPI.fatal('Fatal log message for spec "spec"');
        
        expect(true).toBe(true);
    });
});
```

## Integrations
### Protractor integration
#### Launch agent in single thread mode.

If you launch protractor in single tread mode , just add agent initialization to the onPrepare function.
Add agent.getJasmineReporter to the  jasmine.getEnv().addReporter() as an argument. You can see this in the example bellow.
Update your configuration file as follows:
```javascript
const ReportportalAgent = require('agent-js-jasmine');

...
const agent = new ReportportalAgent({
        token: "00000000-0000-0000-0000-000000000000",
        endpoint: "http://your.reportportal.server/api/v1",
        launch: "LAUNCH_NAME",
        project: "PROJECT_NAME",
        attachPicturesToLogs: false,
        attributes: [
            {
                "key": "YourKey",
                "value": "YourValue"
            },
            {
                "value": "YourValue"
            },
        ]
    });
exports.config = {
    ...
    onPrepare: ()=> {
        ...
        jasmine.getEnv().addReporter(agent.getJasmineReporter());
    },
    afterLaunch:() => {
        return agent.getExitPromise();
    }
};
```

#### Launch agents in multi thread mode.

 For launching agents in multi thread mode firstly parent launch must be created and it ID
 must be sent to the child launches , so they would send data to the right place, and wouldn't create new
 launch instances at the Report Portal.
 
 The main problem is that node.js is a single threaded platform. And for providing multi treading launch with browsers protractor generate
 new processes  of node, which can't interact with each other, so Singelton objects or functions can't be created for synchronizing
 it work. Only primitive types could be sent as args to the new processes before launch. The way of resolving this problem is
 to create launch file that would generate a Parent Launch and send launch's ID to protractor as argument. Then protractor would
 launch jasmine-agents with parent ID.
 Look through example of the Launch File with protractor-flake module at the 'Settings fot the multi threaded launch' section or at the examples folder.
 Any node runner could be used!
 
1. Install 'protractor-flake':
```bash
npm install protractor-flake --save-dev
```
 
2. Create a config file as in example below:

reportportalConf.js
```javascript
module.exports = {
    token: "00000000-0000-0000-0000-000000000000",
    endpoint: "http://your.reportportal.server/api/v1",
    launch: "LAUNCH_NAME",
    project: "PROJECT_NAME",
    attachPicturesToLogs: false,
    attributes: [
        {
            "key": "YourKey",
            "value": "YourValue"
        },
        {
            "value": "YourValue"
        },
    ]
}
```

3. Create a main launch file as in example below:

protractorLaunchFile.js
```javascript
const protractorFlake = require('protractor-flake');
const AgentJasmine = require('agent-js-jasmine');
const reportportalConfig = require('./reportportalConf');
const agent = new AgentJasmine(reportportalConfig);

agent.getLaunchStartPromise().then((launchData) =>{
    protractorFlake({
        maxAttempts: 1,
        protractorArgs: [
            './multiThreadConf.js',
            '--params.id',
            launchData.id
        ]
    }, (status) => {
        agent.getExitPromise().then(() =>{
            process.exit(status);
        });
    });
});
```

4. Then create protractor's spec file as in example below:

multiThreadConf.js file

```javascript
 const ReportportalAgent = require('agent-js-jasmine');
 const reportportalConfig = require('./reportportalConf');
 
 exports.config = {
     multiCapabilities: [
         {
             name: 'normal',
             browserName: 'chrome',
             maxInstances: 2,
             shardTestFiles: true,
             chromeOptions: {
                 args: ['--window-size=1024,768', '--disable-infobars']
             }
         }
     ],
     specs: ['testAngularPage.js', 'testGithubPage.js'],
     onPrepare() {
         const config = Object.assign({
             id: browser.params.id
         }, reportportalConfig);
         const agent = new ReportportalAgent(config);
         /*Its a hack. There is an issue since 2015. That Jasmine doesn't wait for report's async functions.
          links to the issues https://github.com/jasmine/jasmine/issues/842
          https://github.com/angular/protractor/issues/1938
          So it needed to wait until requests would be sent to the Report Portal.
          */
         afterAll((done) => agent.getPromiseFinishAllItems(agent.tempLaunchId).then(()=> done()));
         jasmine.getEnv().addReporter(agent.getJasmineReporter());
     }
 
 };
```

5. Update script section for your package.json:
```javascript
"scripts": {
    "protractor-multi": "node protractorLaunchFile.js"
 }
```

6. Run your protractor:
```bash
npm run protractor-multi
```


Link to the jasmine issue , that it doesn't work well with async functions
[jasmine issue](https://github.com/jasmine/jasmine/issues/842), 
[protractor's community](https://github.com/angular/protractor/issues/1938)

# Copyright Notice
Licensed under the [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0.html)
license (see the LICENSE.txt file).

		
