# Connect an Accelerometer to the Internet of Things

![](http://i.imgur.com/X5lK5WA.gif)

Today we're going to use Intel Galileo to connect an Accelerometer to the internet. Accelerometers are awesome little devices that can sense movement (acceleration). You can find accelerometers in almost every smartphone and they're even in fancier hard drives for fall protection.

Imagine a cardboard box with a basketball inside. Now imagine picking up the box and moving it left or right. Depending on how fast you pick up the box, the basketball may hit the roof. If you move it left or right, it'll hit the sides. 

![](http://i.imgur.com/TdpTOwq.png)

This is how an acceleromter works. The only difference is there is some compenent to measure the force of the ball hitting a wall. It also happens on a much smaller scale, about the size of your fingernail.

> By measuring the amount of static acceleration due to gravity, you can find out the angle the device is tilted at with respect to the earth. By sensing the amount of dynamic acceleration, you can analyze the way the device is moving. At first, measuring tilt and acceleration doesn't seem all that exciting. However, engineers have come up with many ways to make really useful products with them.

Check out [this article](http://www.dimensionengineering.com/info/accelerometers) for more on accelerometers.

## ADXL335 Triple Axis Accelerometer

![](http://i.imgur.com/WHPThTY.jpg)

For this demo we're going to be using the [SainSmart ADXL335 Triple Axis Accelerometer](https://www.sparkfun.com/products/9269).

> The sensor is a polysilicon surface-micromachined structure built on top of a silicon wafer. Polysilicon springs suspend the structure over the surface of the wafer and provide a resistance against forces due to applied acceleration.
As described in [the data sheet](http://www.sainsmart.com/arduino-adxl335-triple-axis-accelerometer-breakout-module.html).

Here are some specs:

* 3-axis sensing
* Small, low profile package
* 4 mm × 4 mm × 1.45 mm LFCSP
* Low power : 350 μA (typical)
* Single-supply operation: 1.8 V to 3.6 V
* 10,000 g shock survival
* Excellent temperature stability
* BW adjustment with a single capacitor per axis
* RoHS/WEEE lead-free compliant

# Overview

* [Intel Galileo Gen 2](http://www.intel.com/content/www/us/en/do-it-yourself/galileo-maker-quark-board.html) - The beautiful blue chip that publishes potentiometer resistance to the internet.
* [NodeJS](https://nodejs.org/) - Running on the Galileo linux environment 
* [PubNub](http://www.pubnub.com/) - Realtime Data Stream Network that connects the Galileo to Eon-chart
* [EON-chart](pubnub.com/developers/eon) - Realtime charting framework that connects to PubNub and renders the resistance value in HTML.

## Galileo

![](http://www.intorobotics.com/wp-content/uploads/2014/12/intel-galileo-gen-2.jpg)

We're going to use Intel Galileo; Intel's flagship IOT prototyping chip that runs NodeJS. Check out the last post where we connected a potentiomter to Intel Galileo.

## Solder the Accelerometer

First thing we need to do is solder headers to the accelerometer. Soldering headers is very simple. Just insert some headers into the provided points, touch sodler to the connection, and then touch the sodlering iron.

![](http://www.cdn.sciencebuddies.org/Files/2084/5/Elec_primer-solder2.jpg)

When you're done, you'll have an accelerometer you can plug into anything.

![](http://i.imgur.com/1nWovxc.jpg)

## Wiring the Accelerometer into the Galileo

Great, now let's wire the accelerometer. This particular device is really simple, there are 6 connections.

* ***ST*** - don't worry about this
* ***Z*** - z axis acceleration
* ***Y*** - y axis acceleration
* ***X*** - x axis acceleration
* ***-*** - ground
* ***+*** - 3.3v power 

Thanks to the header pins we just soldered, we can simply plug the acclerometer into our breadboard. Take 5 jumper cables, and wire them to each of the connections (excluding ST).

![](http://i.imgur.com/KUsXgxe.jpg)

I used green for x, y, and z, red for power, and black for ground. Now plug the other ends of the jumpers into the Galileo. Note that you need to use ***3.3v*** power and ***not 5v***. 5v will dammage your accelerometer.

Connection | Wire Color | Galileo Pin
--------|-----------------|----------
Z | Green | A0
X | Green | A1
Z | Green | A2
- | Black | GND
+ | Red | 3.3v

Here's what mine looked like when I was done connecting everything:

![](http://i.imgur.com/KNCYsMa.jpg)

## NodeJS Code

Now we're going to read the accelerometer data using NodeJS and publish it to PubNub network so we can display it in a graph. Load the Intel XDK and start with a blank project.

If you need help setting up your Galileo, check out last weeks post on connecting a Potentiometer to the Galileo.

![](https://software.intel.com/sites/default/files/managed/8e/2f/intelxdkiot_develop.png)

### Package.json

In the package.json add PubNub as a dependency:

```js
{
  "name": "pubnub-galileo",
  "description": "",
  "version": "0.0.0",
  "main": "main.js",
  "engines": {
    "node": ">=0.10.0"
  },
  "dependencies": {
      "pubnub": "3.7.10"
  }
}
```

Then click Install/Build. This essentially runs ```npm install``` on your Galileo and adds the PubNub library to your build. PubNub is the backend for this demo; it allows us to publish the accelerometer data to the internet and read it somewhere else.

We can read the accelerometer data using analog read. We'll assign the values to x, y, and z within our javascript file.

```js
var mraa = require('mraa');

console.log('MRAA Version: ' + mraa.getVersion()); //write the mraa version to the console

var x = new mraa.Aio(0);
var y = new mraa.Aio(1);
var z = new mraa.Aio(2);

var xo = x.read();
var yo = y.read();
var zo = z.read();
```

So all we're gonna do is create a loop to conintually read the values of the X, Y, and Z accelerations. In the following case, we store the initial values as a "zero" value and then calculate the difference. This is rough, but it will work for the sake of this example.

```js
setInterval(function(){

    var xv = x.read() - xo;
    var yv = y.read() - yo;
    var zv = z.read() - zo;
    
    console.log(xv, yv, zv);

}, 1000);
```

### PubNub

Now let's integrate PubNub. PubNub is the service that will allow us to publish the data to the internet and display it on a graph in our webpage.

```js
var mraa = require('mraa');
var PUBNUB = require('pubnub');

console.log('MRAA Version: ' + mraa.getVersion()); //write the mraa version to the console

var x = new mraa.Aio(0);
var y = new mraa.Aio(1);
var z = new mraa.Aio(2);

var xo = x.read();
var yo = y.read();
var zo = z.read();

var pubnub = PUBNUB.init({
  publish_key: 'demo',
  subscribe_key: 'demo'
});

setInterval(function(){

    var xv = x.read() - xo;
    var yv = y.read() - yo;
    var zv = z.read() - zo;
    
    console.log(xv, yv, zv);
    
    pubnub.publish({
        channel: 'pubnub-intel-gal-demo-xyz',
        message: {
          columns: [
            ['x', xv],
            ['y', yv],
            ['z', zv]
          ]
        }
    });

}, 1000);
```

![](http://i.imgur.com/qp6fpol.gif)
