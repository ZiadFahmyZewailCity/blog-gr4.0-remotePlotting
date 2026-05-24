---
layout: post
title: "Introduction to OOT and me"
author: "Ziad Fahmi"
---

Hello everyone, my name is ziad and i am very happy to be working on an OOT module for GR 4.0 this summer as part of google summer of code.

This post is going to be broken up to two parts, discussing the background of the OOT module and what work has been done up until now.

## A bit of background on this OOT

Previously for GR 3.x, an OOT module named [gr-bokehgui](https://github.com/gnuradio/gr-bokehgui) made by Kartik Patel during GSOC 2017. This OOTs goal was to provide A Web based GUI for GNU Radio applications

Now that GR 4.0 is being developed, development of a successor OOT to gr-bokehgui for GR 4.0 is needed and ill be the one to develop it ! 

Here is a link to my GSoC proposal if you would like to take a look 
[Link To gsoc proposal](https://docs.google.com/document/d/18-Tik6fIwv6KZVvqpIYjX_cKrxYV9c4MihXhG65SPKg/edit?usp=sharing)

I would like to go over the objectives of the OOT listed in the proposal as they are to me whats most important for potential user of this OOT (_Such as yourself_)

At a minimum this OOT should offer the same functionality as the previosu gr-bokehgui with the objective list being the following

**Objectives:**
* Plotting in the browser of data coming from a GR4 flow graph 
* Allowing for interaction with the plots in the browser through widgets 
* Allowing for interactivity with GR4 flowgraph through widgets  
* Allowing for customization of location of plots/widgets in browser

## Work done up until now

### Deciding which library will be used for the browser based plotting

Although many options currently exist i have played around with two bokehGUI a python based library, the same library used for gr-bokehgui and imGUI a c++ based library traditionally used in gameDev. 

#### BokehGUI
I have played around with bokehGUI during the proposal writing phase. I succefully plotted a sinWave from a GR 4.0 flowgraph using custom ZMQ block for IPC between the flowgraph and the bokehGUI python script ([Video of test](https://drive.google.com/file/d/1-5VSE6BerbMY9ruzjn5oQxtQFzTD34lS/view?usp=sharing)).

Bokeh operates by running a lightweight Python HTTP server that serves the web UI to your browser. It then opens a WebSocket connection to continuously push live data updates and sync UI interactions between the browser and the Python backend

I found bokehGUI to be simple to use but not very flexible, its plotting abilites are also a bit limited when compared to our other option imGUI. There is also currently no plugin for python for GR 4.0

#### imGUI
Although not specifically made for plotting in the browser when coupled with emscripten a tool for compiling C++ code into WASM ,it can run in the browser just fine.

imGUI is capable of some pretty nice plots especially when paired with implotty a library which adds better plotting capability to imGUI, some examples:
* [Example 1](https://fsunuc.physics.fsu.edu/git/gwm17/implot/src/commit/e0450d00af0cad3ba73f973a6efb069bddd39da6/README.md)
* [Example 2](https://github.com/epezent/implot_demos/blob/master/screenshots/spectrogram.png)

The current idea for the implementation of the OOT using imGUI is the following
When the flowgraph starts, an HTTP server serves the pre-compiled WASM file to the browser. 

Next, the flowgraph sends over a configuration file that the browser reads to dynamically generate the required dashboard layout (The plots & widget the creator of the flow graph want)

From that point on, the flowgraph’s only job is pushing raw data to the client and occasionally receiving control updates when a user interacts with a widget. 

I have been building a small proof of concept for this approach for the past week and half, although i have yet to get data from the flowgraph plotted yet. I have managed to get the following working

1. **Configurable Dashboard**
   The dashboard layout is defined at runtime by a JSON config file pushed over the WebSocket connection. The config specifies:
   * The panels to display
   * The widgets and plots within each panel (time-series plots, sliders, and text labels are currently supported)
   * The data source key for each widget/plot, which the dashboard uses to route incoming telemetry to the correct element

   for context almost every GUI element other than drawings is contained within a panel in imGUI [Picture of panel with widgets is attached for clarity](https://drive.google.com/file/d/1joxmmHyfAzYCsdj3O6hg9RnatKXc_0ip/view?usp=sharing)

2. **Sending config & Simulated data stream** A python script which on connection to the dashboard sends the config file to the dashboard, then begins streaming telemetry packets at a fixed rate over TCP. 

3. **Bi-Directional Communication**
   A slider in the dashboard was used to control the frequency of a sine wave. 
   Adjusting the slider sent a control message back to the Python script, which varies the frequency of a sine wave being generated and streamed back to the dashboard.

Here is a video showing it off [Intially made for my mentors but i see no reason why i cant also share it with you guys](https://drive.google.com/file/d/1joxmmHyfAzYCsdj3O6hg9RnatKXc_0ip/view?usp=sharing)

I hope to get the current version of the POC interfaced with a flowgraph soon so we can truly see the feasibilty of the current architecture

Overall imGUI looks very promising, although a final decision hasnt been made on what library is to be used, if the POC is working without issue i will be probably sticking to imGUI 

The code created uptil now can be found in the repo for the OOT module (Hoping to get it organized soon but its a bit of a mess currently)

## Thank you for taking the time to read this !
Very happy to be part of this community and to be working on this OOT for this summer, hope you all have a wonderfull day

---

## Feedback is always welcome
If you feel you have any feedback, suggestions or questions, please feel free to email me at s-ziad.fahmy@zewailcity.edu.eg (Prefered) or maybe @ me on the GNU Radio community matrix chat.