This is a temporary npm package to fix the message handler callback registration. 

The origianl code was taken from https://www.npmjs.com/package/mqtt and modified.

* The original can be installed by npm i mqtt. 
* This origianl has the capability to add the callbacks in to array so all the registered callbacks are called for the event.

The problem encountered.
* To use it with the React, it may be called in the useEffect with no dependant state variables so it's executed just once along with subsribe.
* And that useEffect should return a function to unsubscribe so the message stops to come on leaving the page.
* Whenever entering and leaving the subject page, subscribe/callback registration/unsubscribe will be executed.
* The subscribe and unsubscribe can be executed multiple times as needed without issue
* But calling `mqtt_client.on` to register the callback will add the same callback to the existing callback array which then be called multiple times.

## The intention and design of the fix.
* In the `mqtt_client.on` function chain, if the callback type is 'message', then instead of adding the new callback, just replace the existing one with the new one, so there is only one message handling callback to execute.
