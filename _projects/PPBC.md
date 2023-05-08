---
layout: page
title: Ping Pong Ball Clock (PPBC)
description: Novel clock design driven by LED strips and a Raspberry Pi controlled via a web app
img: assets/img/ppbc.jpg
importance: 1
category: fun
---

## Inspiration

A reddit post originally tipped me off to the project. This [thingiverse](https://www.thingiverse.com/thing:4091854) project detailed some 3D printed parts for a ping pong ball clock. Upon further investigation I found an [instructable](https://www.instructables.com/id/Ping-Pong-Ball-LED-Clock/) project that laid out designs that did not involve 3D printing and optimized the LED placement. I used this instructable project as the basis for the hardware design. However, I knew from the start that I was going to control the clock using a raspberry pi (zero) and that I would build upon a previous project that controlled LEDs using a local web app.

## Design Concept

This firmware is centered in Python because of how easy it is to use and a couple easy to use and essential libraries:

- Neopixel libraries (LED drivers)
- Flask (web server)

The layout of the LED strips called for a font libraries and character writing functions to be written from scratch. The only code reutilized from previous projects were those that drove single LED illumination.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/layout visualizer-01.jpg" title="PPBC Layout Design" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    PPBC wiring design concept. This aided driver programming as it was essential to map the correct LEDs in sequence with their coordinates on the LED array. The code utilized a 2-dimensional array to store the LED indices, so "999" was used for spots in the array where there existed no ball.
</div>

Novel font libraries were designed to give the user something more to look at than the single font from the original reference project. It also expanded the font from only digits to characters as well, which later becomes essential for weather and other messages.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/layout visualizer-02.jpg" title="PPBC Digit Font 1" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/layout visualizer-03.jpg" title="PPBC Digit Font 2" class="img-fluid rounded z-depth-1" %}
    </div>
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/layout visualizer-04.jpg" title="PPBC Character Font" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    PPBC font designs.
</div>

## Implementation

The clock is coded with a backend that consists of a continuous loop, checking for updates and updating the clock, and a front end which is a threaded flask server which acts as both the web app and the API for updating the clock. Updates passed through the API take effect on the next iteration of the loop. The API directly changes features stored in an object. This object is an instance of a custom class called `PingPongBoard` and stores essential variables and functions used for driving the LEDs. Custom color animations as well as content features (i.e. time, date, weather, message) are implemented in this object.

Both front and back end are hosted simultaneously on the Raspberry Pi which drives the LEDs. This requires that the Pi be accessible on the local network so that local devices can access the web app.

## Video

<center>
    <iframe width="800" height="500" src="https://www.youtube.com/embed/jIoWmhVCPb4" title="YouTube video player" frameborder="0" allow="accelerometer; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
</center>

At the time of this post the video had 17K views! Thank you all to for the support and I hope you have been able to make PPBCs of your own.

## Github Repo

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
    {% include repository/repo.html repository='lancewilhelm/pingPongBallClock' %}
</div>

## Future Work

#### Web App

The web application can be brought into the modern age with libraries like React or even Jekyll sites like this one. The trick to make it available for hosting via the flask python server. Other solutions are possible, but the simple setup of flask servers make them ideal for this use case.

#### Drivers

The python drivers should move away from a continual loop whereby updates only occur on new loop instances. Severe slowing of framerate and thus polling rate can be seen when full board LED animations are implemented. One way to address this may be through the `threading` package built into Python. This has the potential to allow for asynchronous tasks which could effectively separate API polling from LED driving. 