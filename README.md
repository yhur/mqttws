# mqttws : MQTT WebSocket Client for React Web
This is a temporary npm package to fix the message handler callback registration and may be removed when the fix is taken into the original library.

## Background

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
* The orignal code adds the callbacks to an array of callbacks when registered multiple times. ie. `client.on('message', callback)`.
But for the message handling call back, we don't and can't have mulitple callbacks, so this code just replaces the callback when called again, meaning only the latest callback will be in effect.

<img width="767" alt="Screenshot 2023-04-22 at 8 56 33 PM" src="https://user-images.githubusercontent.com/13171662/233783299-e2a282a7-72c5-4eee-81a8-cdcc71784b2f.png">

## Sample code
```javascript
import React, { useEffect, useState } from 'react';
import Box from '@mui/material/Box';
import mqtt from 'mqttws/dist/mqtt';

let options = {
    username: "mqttid",
    password: "mqttpassword",
    clean: true,
    rejectUnauthorized: false
};

let mqttClient = mqtt.connect('wss://localhost:9001', options);

const Event = (props) => {
    const { devId } = props.chosenDevice;
    const [devEvent, setDevEvent] = useState();
    
    useEffect(() => {
        mqttClient.subscribe('iot3/+/evt/#', {qos:0});
        mqttClient.on('message', (topic, message) => {
            console.log(topic + ' : ' + message.toString());
            setDevEvent(message.toString());
        });

        return () => {
            mqttClient.unsubscribe('iot3/+/evt/#');
        };
    }, [])

    return (
        <Box>
            <h1>Device Id : {devId}</h1>
            <h3>{devEvent}</h3>
        </Box>
    )
}

export default Event;
```
