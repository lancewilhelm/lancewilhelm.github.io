---
layout: page
title: Retroboard
description: Open source retro-style LED matrix display for general use, inspired by the Tidbyt 
img: assets/img/retroboard/IMG_0982_crop.jpg
importance: 1
category: fun
---

## About

The Retroboard project is LED matrix software and a web application designed to control RGB LED matrices using a Raspberry Pi. The project is inspired by the Tidbyt Kickstarter.

The setup is intended for a Raspberry Pi, specifically a 3B+ model, and requires the use of an Adafruit RGB Matrix HAT, a 5V power supply, and a 64x32 RGB LED Matrix, P3. The project leverages the rpi-rgb-led-matrix library by hzeller.

The Magic Color Board system consists of two applications that have been dockerized for ease of testing and deployment. Both the app and API directories have their own Dockerfile, allowing them to be built and run separately if needed.

The web application is built on the Next.js framework using React, chosen for its state-of-the-art web app development capabilities. The API is built simply from Flask, providing a Python basis for the API which allows for easy integration into the LED driver library.

As of the time of writing, the project is in its early stages with minimal functionality. The ultimate goal is to create a fully configurable and customizable LED matrix application that can be used in a variety of cases and allows any user to develop their own application.

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/retroboard/IMG_0982_crop.jpg" title="Example Image" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Retroboard snapshot. Currently the Retroboard has a couple baked in applications such as a stock chart and standard clock.
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/retroboard/IMG_0977.JPG" title="Internals" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    Retroboard internals. The board is powered with a raspberry pi which hosts both the local LED matrix drivers as well as the web application. 
</div>

<div class="row">
    <div class="col-sm mt-3 mt-md-0">
        {% include figure.html path="assets/img/retroboard/sim.JPG" title="Simulator" class="img-fluid rounded z-depth-1" zoomable=true %}
    </div>
</div>
<div class="caption">
    There also exists an online simulator for testing applications and display. This is useful for quick development away from the Retroboard.
</div>

## Future Work

I would like to develop an application store which allows anyone to create and deploy custom applications for the Retroboard. More work needs to be done with asynchronous task handling between the app and drivers. Lastly, a bluetooth application for setup and control would prove useful and potentially eliminate the need for a local network connection for some applications.

## Github Repo

<div class="repositories d-flex flex-wrap flex-md-row flex-column justify-content-between align-items-center">
    {% include repository/repo.html repository='lancewilhelm/RetroBoard' %}
</div>

