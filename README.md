This is a temporary npm package to fix the message handler callback registration.

The original can be installed by npm i mqtt. This origianl has the capability to add the callbacks in to array so all the registered callbacks are called for the event.

But 'message' callback basically needs to be just one since there is no topic based registration or similar.

So this temporary package just replace the existing callback with the latest one if registered multiple times.
