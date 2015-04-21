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

## Galileo

![](http://www.intorobotics.com/wp-content/uploads/2014/12/intel-galileo-gen-2.jpg)

We're going to use Intel Galileo; Intel's flagship IOT prototyping chip that runs NodeJS. Check out the last post where we connected a potentiomter to Intel Galileo.

# Overview

* [Intel Galileo Gen 2](http://www.intel.com/content/www/us/en/do-it-yourself/galileo-maker-quark-board.html) - The beautiful blue chip that publishes potentiometer resistance to the internet.
* [NodeJS](https://nodejs.org/) - Running on the Galileo linux environment 
* [PubNub](http://www.pubnub.com/) - Realtime Data Stream Network that connects the Galileo to Eon-chart
* [EON-chart](pubnub.com/developers/eon) - Realtime charting framework that connects to PubNub and renders the resistance value in HTML.

![](http://i.imgur.com/1nWovxc.jpg)
![](http://i.imgur.com/KUsXgxe.jpg)
![](http://i.imgur.com/KNCYsMa.jpg)
![](http://i.imgur.com/qp6fpol.gif)
