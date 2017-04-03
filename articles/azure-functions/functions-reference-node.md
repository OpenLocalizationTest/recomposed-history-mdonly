---
title: JavaScript developer reference for Azure Functions | Microsoft Docs
description: Understand how to develop Azure Functions using JavaScript.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: ''
tags: ''
keywords: azure functions, functions, event processing, webhooks, dynamic compute, serverless architecture

ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.service: functions
ms.devlang: nodejs
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/06/2017
ms.author: chrande, glenga

---
# Azure Functions JavaScript developer guide
> [!div class="op_single_selector"]
> * [C# script](functions-reference-csharp.md)
> * [F# script](functions-reference-fsharp.md)
> * [JavaScript](functions-reference-node.md)
> 
> 

The JavaScript experience for Azure Functions makes it easy to export a function which is passed a `context` object for communicating with the runtime, and for receiving and sending data via bindings.

This article assumes that you've already read the [Azure Functions developer reference](functions-reference.md).

## Exporting a function
All JavaScript functions must export a single `function` via `module.exports` for the runtime to find the function and run it. This function must always include a `context` object.

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // Additional inputs can be accessed by the arguments property
    if(arguments.length === 4) {
        context.log('This function has 4 inputs');
    }
};
// or you can include additional inputs in your arguments
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
};
```

Bindings of `direction === "in"` are passed along as function arguments, meaning you can use [`arguments`](https://msdn.microsoft.com/library/87dw3w1k.aspx) to dynamically handle new inputs (for example, by using `arguments.length` to iterate over all your inputs). This functionality is very convenient if you only have a trigger with no additional inputs, as you can predictably access your trigger data without referencing your `context` object.

The arguments are always passed along to the function in the order they occur in *function.json*, even if you don't specify them in your exports statement. For example, if you have `function(context, a, b)` and change it to `function(context, a)`, you can still get the value of `b` in function code by referring to `arguments[3]`.

All bindings, regardless of direction, are also passed along on the `context` object (see below). 

## context object
The runtime uses a `context` object to pass data to and from your function and to let you communicate with the runtime.

The context object is always the first parameter to a function and must be included because it has methods such as `context.done` and `context.log` which are required to correctly use the runtime. You can name the object whatever you like (i.e. `ctx` or `c`).

```javascript
// You must include a context, but other arguments are optional
module.exports = function(context) {
    // function logic goes here :)
};
```

### context.bindings property

```
context.bindings
```
Returns a named object that contains all your input and output data. For instance, the following binding definition in your *function.json* lets you access the contents of the queue from the `context.bindings.myInput` object. 

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
}
```

```javascript
// myInput contains the input data which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

### context.done method
```
context.done([err],[propertyBag])
```

Informs the runtime that your code has finished. You must call `context.done` or the runtime never knows that your function is complete and the execution will time out. 

The `context.done` method allows you to pass back a user-defined error to the runtime, as well as a property bag of properties which will overwrite the properties on the `context.bindings` object.

```javascript
// Even though we set myOutput to have:
//  -> text: hello world, number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method will overwrite the myOutput binding to be: 
//  -> text: hello there, world, noNumber: true
```

### context.log method  

```
context.log(message)
```
Allows you to generate a correlated set of output log statements. When you use `console.log`, your messages will only show for process level logging, which isn't as useful.

```javascript
/* You can use context.log to log output specific to this 
function. You can access your bindings via context.bindings */
context.log({hello: 'world'}); // logs: { 'hello': 'world' } 
```

The `context.log` method supports the same parameter format that the Node [util.format method](https://nodejs.org/api/util.html#util_util_format_format) supports. So, for example, code like this:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

can be written like this:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

## HTTP triggers and bindings

HTTP and webhook triggers and HTTP output bindings use request and response objects to represent the HTTP messaging.  

### Request object

The `request` object has the following properties:

| Property      | Description                                                    |
| ------------- | -------------------------------------------------------------- |
| _body_        | An object that contains the body of the request.               |
| _headers_     | An object that contains the request headers.                   |
| _method_      | The HTTP method of the request.                                |
| _originalUrl_ | The URL of the request.                                        |
| _params_      | An object that contains the routing parameters of the request. |
| _query_       | An object that contains the query parameters.                  |
| _rawBody_     | The body of the message as a string.                           |


### Response object

The `response` object has the following properties:

| Property  | Description                                               |
| --------- | --------------------------------------------------------- |
| _body_    | An object that contains the body of the response.         |
| _headers_ | An object that contains the response headers.             |
| _isRaw_   | Indicates that formatting is skipped for the response.    |
| _status_  | The HTTP status code of the response.                     |

### Accessing the request and response 

When working with HTTP triggers, there are three ways that you can access the HTTP request and response objects:

+ From the named input and output bindings. In this way, the HTTP trigger and bindings work the same as any other binding. The following example sets the response object using a named `response` binding. 

    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```

+ From `req` and `res` properties on the `context` object. In this way, you can use the conventional pattern to access HTTP data from the context object, instead of having to use the full `context.bindings.name` pattern. The following example shows how to access the `req` and `res` objects on the `context`:

    ```javascript
    // You can access your http request off of the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your http response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ Calling `context.done()`. There is a special kind of HTTP binding that returns the response that is passed to the `context.done()` method. The following HTTP output binding defines a `$return` output parameter:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    This output binding expects you to supply the response when you call `done()` as follows:

    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## Node Version & Package Management
The node version is currently locked at `6.5.0`. We're investigating adding support for more versions and making it configurable.

You can include packages in your function by 
uploading a *package.json* file to your function's folder in the function app's file system. For file upload instructions, see the **How to update function app files** section of the [Azure Functions developer reference topic](functions-reference.md#fileupdate). 

You can also use `npm install` in the function app's SCM (Kudu) command line interface:

1. Navigate to: `https://<function_app_name>.scm.azurewebsites.net`.
2. Click **Debug Console > CMD**.
3. Navigate to `D:\home\site\wwwroot\<function_name>`.
4. Run `npm install`.

Once the packages you need are installed, you import them to your function in the usual ways (i.e. via `require('packagename')`)

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

Node should have a `package.json` at the root of the Function App so Functions can shared cached packages. If there are version conflicts, you can add a `package.json` at a Function level. However, doing so should be avoided for performance reasons. 

## Environment variables
To get an environment variable or an app setting value, use `process.env`, as shown in the following code example:

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    context.log('Node.js timer trigger function ran!', timeStamp);   
    context.log(GetEnvironmentVariable("AzureWebJobsStorage"));
    context.log(GetEnvironmentVariable("WEBSITE_SITE_NAME"));

    context.done();
};

function GetEnvironmentVariable(name)
{
    return name + ": " + process.env[name];
}
```
## Considerations for JavaScript Functions

You should be aware of the following items when working with JavaScript functions.

### Use small VMs when using App Service Plan

When creating a function app that uses App Service Plan for your JavaScript functions, we recommend that you use multiple small VMs rather than a medium or large VM. At this point, Functions runs JavaScript functions more efficiently on small VMs; using larger VMs will not produce the expected results.    

### TypeScript/CoffeeScript support
There isn't, yet, any direct support for auto-compiling TypeScript/CoffeeScript via the runtime, so that would all need to be handled outside the runtime, at deployment time. 

## Next steps
For more information, see the following resources:

* [Best Practices for Azure Functions](functions-best-practices.md)
* [Azure Functions developer reference](functions-reference.md)
* [Azure Functions C# developer reference](functions-reference-csharp.md)
* [Azure Functions F# developer reference](functions-reference-fsharp.md)
* [Azure Functions triggers and bindings](functions-triggers-bindings.md)

