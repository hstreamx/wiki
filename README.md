# ACE Web SDK API extended methods

The term `extended methods` are used for those new methods added to Web SDK API that are documented in markdown format (instead of 
Microsoft Word).

## Terms and definitions

* `Telia ACE` is the name of a contact center solution from Telia. 
* `ACE Server` is the set of backend servers making up an ACE system.
* `ACE Web SDK` is a set of small JavaScript libraries to integrate with an ACE system.
* `ACE Web API` is a REST API for communication directly with an ACE System.
* `Web SDK API` is a JavaScript API for communication with an ACE System via ACE Web SDK.
* `Visitor` is the website visitor using the client application on a webpage on a customer website.
* `Agent` is the website representative at the ACE contact center.
* `Customer` In this context, customer is always the organization using a ACE contact center. That is, the customer to ACE. This 
should not be confused with the person browsing the customer website; this person is called the (website) visitor.
* `Client application` The application that uses Web SDK API to communicate with an ACE system.
* `Queue` An ACE queue (or waiting list). Queues (and waiting lists) are used to process contacts at an ACE 
contact center.

## References

- Configuration Instructions ACE Web SDK
- Interface Specification Pulse Interface
- Interface Specification Callback Interface - Web Service

## Change log

* 2019-02-12: Version update: CallGuide.api.createCallback version 3 (exempel hur det kan se ut)
* 2019-02-09: Version update: CallGuide.api.openingHours version 2
* 2019-02-04: Version update: CallGuide.api.createCallback version 2
* 2019-02-01: First version of document.

## Error handling

All API methods return its result in the same format.

```javascript
{
  resultCode: number          // Operation result code
  resultMessage: string       // Operation result message, for logging purposes
  resultData: {
    data: Object              // Method specific result data
    errorCode: number         // ACE internal error code (only for errors)
    errorMessage: string      // ACE internal error message (only for errors)
  } 
  methodName: string          // Method name called in API
  methodVersion: string       // Method protocol version used
}
```

A successful method call has a `resultCode` of zero (0) and `resultData.data` as defined in API Reference section. 

For errors, `resultCode` differ from zero (0). There is also a `resultMessage` that can be used for logging. Furthermore, the `resultData
.errorCode` and `resultData.errorMessage` is added to the result, with internal error information from ACE. These can be useful for 
troubleshooting a problem.

General result codes for all methods in API is defined in table below. Each API method normally has some additional result codes, 
described in the API Reference.

Result code | Description | How to solve?
:---|:---|:---
0 | Operation completed with no error | -
1 | ACE Server error | Ask ACE administrator to investigate.
2 | ACE Web API error  | Ask ACE administrator to investigate.
3 | Too many requests from client application | Client application should slow down requests to Web SDK API.
10 | Bad request | ACE Web API is getting a bad request from either Web SDK or Client application. Ask Web SDK administrator to investigate.
11 | Invalid version | Protocol version for a method does not exist. Verify that client application is using correct version. In last case, ask Web SDK administrator for help.
20 | Server not found | URL to ACE Web API is wrong. Ask Web SDK administrator to investigate.
99 | Unknown error  | Ask Web SDK administrator to investigate.

## API Reference

### CallGuide.api.createCallback

This method will create a callback in an ACE system, resulting in an ACE agent calling up a phone number either as soon as possible or at
 a given time and date.

#### Specification

 * Protocol version: 1

#### Prototype

 * createCallback (RequestInput) -> ResponseOutput

#### Request input

Name | Type | Description
:---|:---|:---
phoneNumber | string | (**Required**) Phone number to call.
priority | number | (**Required**) Priority, used to sort a queue of callbacks. From 1 (highest) to 9 (lowest).
errand | string | (**Required**) ACE task type. Must match existing object in ACE. Used for routing.
entrance | string | (**Required**) ACE entrance. Must match existing object in ACE. Used for routing.
menuChoice | string | (**Required**) ACE menuchoice. Must match existing object in ACE. Used for routing.
queueName | string | (**Required**) ACE queue name. Should be short queue name, not the longer GUI name. Must match existing object in ACE. Used for routing.
agentUsername | string | (**Required**) ACE agent username. Must match existing object in ACE. Used for routing.
startTime | string | Preferred time when callback should be made.
stopTime | string | Time when callback should be finished. If a successful callback has not been made before this time, it will be discarded.
cid | string | Customer identity. Can be used for routing.
taskNumber | string | Task number. Can be used for connection to task handling system.
customerName | string | Customer name. Ca be used for routing.
comment | string | Free text comment. Will be seen by agent issuing the callback. Max 512 characters.
ani | string | Callers phone number. Can be used for routing. Normally empty when creating callbacks with API.
dnis | string | Dialled phone number. Can be used for routing. Normally empty when creating callbacks with API.
customerSpecific1 | string | Customer unique data. Can be used for routing.
customerSpecific2 | string | Customer unique data. Can be used for routing.
customerSpecific3 | string | Customer unique data. Can be used for routing.
customerSpecific4 | string | Customer unique data. Can be used for routing.
customerSpecific5 | string | Customer unique data. Can be used for routing.

<sup>*</sup> **Note**:  All required (and other) fields can be omitted in method call if they are pre-defined in Web SDK configuration 
file. 

<sup>*</sup> **Note**: Not all parameters used for routing are required. One (and only one) of these parameter groups should be supplied to create a callback. If more than one are present, the priority is as shown here below.

* Normal routing: `entrance` and `menuChoice`
* Routed to a specific queue, regardless of routing algorithm: `queueName`
* Routed to a specific agent, regardless of routing algorithm: `agentUsername`

<sup>*</sup> **Note**: Phone number rules:

* Allowed characters: `0-9`, `Space`, `Tab`, `-`, `+` 
* These characters are automatically trimmed: `Space`, `Tab`, `-`
  * Example 1: `018-123456` is trimmed to `018123456`
  * Example 2: `+46 070 456 78 90` is trimmed to `+460704567890`
* Resulting phone number must contain five digits or more.
* Format should be one of these: 
  * `\<0\> \<areanumber\> \<localnumber\>`
  * `\+ \<country\> \<areanumber\> \<localnumber\>`

<sup>*</sup> **Note**: If start time is omitted, current time will be used. If stop time is omitted, one week from now eill be used.

<sup>*</sup> **Note**: Date format is `YYYYMMDD HH:MM:SS`. Example: `20190101 17:00:00`
  
#### Response output

A successful callback creation returns the record ID of that callback. Right now there is now real usage for that ID, but future updates 
to the API may add that.

```javascript
{
  resultCode: number          // Operation result code
  resultMessage: string       // Operation result message, for logging purposes
  resultData: {
    recordId: string          // Callback record ID
    errorCode: number         // ACE internal error code (only for errors)
    errorMessage: string      // ACE internal error message (only for errors)
  } 
  methodName: string          // Method name called in API
  methodVersion: string       // Method protocol version used
}
```

#### Result codes

Method specific result codes are shown below. For general result codes (< 100), see Error handling section.

Result code | Description | How to solve?
:---|:---|:---
0 | Operation completed with no error | -
100 | Invalid errand  | Correct input error. Input is coming from either Client application or Web SDK configuration file. If invalid ACE object names (entrance, queue name, etc) have been used, talk to ACE administrator to get correct names.
101 | Invalid priority  | (see above)
102 | Invalid queue name  | (see above)
103 | Invalid agent name  | (see above)
104 | Invalid entrance  | (see above)
105 | Invalid menu choice  | (see above)
106 | Invalid combination of queue name, agent name, or entrance and menu choice | (see above)
107 | Entrance and queue or agent belong to different organisation areas | (see above)
108 | Invalid start date  | (see above)
109 | Invalid stop date  | (see above)
110 | Start date is after stop date  | (see above)
111 | Invalid phone number  | (see above)
112 | Duplicate phone number  | Client application should inform visitor that a callback with this this phone number already exists.
113 | No more callbacks allowed  | Talk to ACE administrator about callback limits in ACE Server.


#### Examples

TODO

#### Protocol version change log

 * Version 3
   * Remove THIS and THAT (exempel hur det kan se ut)
   * Do X and Z.
 * Version 2
   * Add FOO and BAR
 * Version 1
   * First version  
