# Template for Actions in Boundary Premium

---

##Description

This project includes an implementation of the VictorOps Action for Boundary
Premium, which can serve as a template for the development of other Actions.

Also included in this project is a test harness that executes the VictorsOps
Action and mock Boundary Premium internal alarm objects.

The test harness uses the [Nock](https://github.com/pgte/nock "Nock") library
to mock HTTP requests to the VictorOps REST Endpoint.

**app/victorops.js**

> The VictorOps Action implementation

**test/mockAlarm.js**

> Mock Boundary Premium internal alarm objects

**test/victorops-spec.js**

> Test harness that exectues the VictorOps Action

---

##Details

An Action implementation for Boundary Premium has the following characteristics:

1. Exposes a `run(action, alarm, alarmlog, cb)` function:

		module.exports = {
			run : postmessage
		};

	**Parameters:**

	*action* - has a configuration property which contains the Action configuration
	settings that are exposed in the Boundary Premium UI.

	The VictorOps Action configuration property, for example, contains the following
	settings:

	* apiToken
	* routingKey
	* messageType

	Other Action implementations will use configuration settings that are specific
	to accessing that Action's endpoint.

	*alarm* - the Boundary Premium internal alarm object. Please refer to `test/mockAlarm.js`
	to see an example of all the properties in an alarm object.

	*alarmlog* - N/A

	*cb* - the callback function, which should have two parameters: *error*, *statistics*

2. Sends a request to the Action's endpoint.

	The VictorOps Action, for example, makes an HTTP POST request to a REST Endpoint
	with the following request body:

		{
			"entity_id": "ALARM/40506/www-server-1/CPU usage is high",
			"entity_display_name": "CPU usage is high",
			"message_type": "CRITICAL",
			"monitoring_tool": "Boundary",
			"state_message": "Server www-server-1's avg cpu utilization is 82.5%\n\nview dashboard - https://premium.boundary.com/home/5733/standard?ival=60&marker=1422377400000!network\n\nNOTE: CPU Utilization has exceeded 80% over a 60s duration"
		}

3. Returns a statistics object as the the callback function's second parameter.

	The statistics object contains a count of the number of messages successfully
	sent to the Action's endpoint as well as a count of the number of the messages
	that resulted in an error when calling the Action's endoint.

	The VictorOps statistics object, for example, is initialized as follows:

		var stats = {
			victorops_messages: 0,
			victorops_errors: 0
		};
---

##Usage

Install the development dependencies:

	npm install

Run Grunt, which executes the test harness:

	grunt

Test harness output:

	Running "mochaTest:test" (mochaTest) task


      VictorOps
        Action
          ✓ should define a run() function.
          ✓ should return an error if there is no configuration. (52ms)
          ✓ should return an error if there is no API key. (51ms)
          ✓ should increment the error stats count when the status code returned from calling VictorOps is not a success code. (58ms)
          ✓ should have the correct statistics for an alarm with 4 servers, routing key is present, message type is CRITICAL. (52ms)
          ✓ should have the correct statistics for an alarm with 4 servers, routing key is not present, message type is CRITICAL. (53ms)
          ✓ should have the correct statistics for an alarm with 4 servers, routing key is present, message type is WARNING. (52ms)
          ✓ should have the correct statistics for an alarm with 4 servers, routing key is not present, message type is WARNING. (52ms)
          ✓ should have the correct statistics for an alarm with 4 servers, routing key is present, message type is INFO. (52ms)
          ✓ should have the correct statistics for an alarm with 4 servers, routing key is not present, message type is INFO. (52ms)


      10 passing (485ms)


    Done, without errors.

To view debug messages from the test, edit the following line in `test/victorops-spec.js` and set the value to `true`:

	// Set this to true to see debug messages in the console output
    var consoleOutput = true;

Execute the test harness again:

	grunt

Test harness output with debug messages:

	Running "mochaTest:test" (mochaTest) task


      VictorOps
        Action
          ✓ should define a run() function.
          ✓ should return an error if there is no configuration. (52ms)
          ✓ should return an error if there is no API key. (51ms)
          ✓ should increment the error stats count when the status code returned from calling VictorOps is not a success code. (58ms)

    url ==>https://alert.victorops.com/integrations/generic/20131114/alert/7b37e7c0-1d42-40b3-a2f3-760001fea15b/level3<==
    reqBody ==>{
      "entity_id": "ALARM/40506/www-server-1/CPU usage is high",
      "entity_display_name": "CPU usage is high",
      "message_type": "CRITICAL",
      "monitoring_tool": "Boundary",
      "state_message": "Server www-server-1's avg cpu utilization is 82.5%\n\nview dashboard - https://premium.boundary.com/home/5733/standard?ival=60&marker=1422377400000!network\n\nNOTE: CPU Utilization has exceeded 80% over a 60s duration"
    }<==

	...
	...
	...

---