---
layout: post
title: "GSoC 2026 Final Work Product: gr4-remotePlotting OOT for GR4"
author: "Ziad Haithem Fahmi"
---

If you want to get straight to the code, [here is the link to the repository](https://github.com/ZiadFahmyZewailCity/gr4.0-remotePlotting/tree/master) you will find everything you need to get this Out Of Tree (OOT) module running with [GNU Radio 4.0](https://github.com/gnuradio/gnuradio4) (GR4).

# Overview

This GSoC project has developed an OOT module for GR4 that is a successor to the OOT module [gr-bokehGUI](https://github.com/gnuradio/gr-bokehgui) for GR3.x. It's meant to allow for remote plotting and interactivity with a GR4 flowgraph by creating an imGUI dashboard containing sinks and widgets that runs in the browser. This is especially useful for monitoring and interacting with flowgraphs on headless devices such as embedded systems.

Here is a quick taste of what the project is capable of
<iframe src="https://drive.google.com/file/d/1zT-sR0pMoCp0At8cjLXZytd9rgN2H_Io/preview" width="640" height="480" frameborder="0" allow="autoplay" allowfullscreen></iframe>

<iframe src="https://drive.google.com/file/d/14XrTIua9mLDJLZpQb6sa212k3RjK8ggW/preview" width="640" height="480" frameborder="0" allow="autoplay" allowfullscreen></iframe>

# Current State

The project successfully implemented all it's deliverables being the following sinks & widgets, although some features are yet to be supported to achieve full feature parity with gr-bokehGUI. 

| Deliverable (Proposal) | Implementation Status | Feature Parity with gr-bokehGUI |
|---|---|---|
| Time Series Sink (imGUI_timeSeriesSink) | ✅ Implemented | Missing: trigger modes (Free/Auto/Normal/Tag + slope/level/delay/channel/tag-key), tag display |
| Frequency Sink (imGUI_frequencySink) | ✅ Implemented | Missing: Max Hold, Averaging (None/Low/Med/High), trigger support, center-freq/bandwidth calibration, message-port input |
| Waterfall Sink (imGUI_waterFallSink) | ✅ Implemented | Missing: center-freq/bandwidth calibration, block-level intensity min/max |
| Constellation Sink (imGUI_constellationSink)| ✅ Implemented | Missing: trigger support, tag display, block-level axis min/max |
| Vector Sink (imGUI_vectorSink)| ✅ Implemented | Missing: custom x-axis values vector, Max Hold, Averaging |
| Button Widget (imGUI_button) | ✅ Implemented | At parity |
| CheckBox Widget (imGUI_checkBox) | ✅ Implemented | At parity |
| Slider Widget (imGUI_slider) | ✅ Implemented | Missing: Step size (limitation of imGUI) |
| Dropdown Menu Widget (imGUI_dropDownMenu) | ✅ Implemented | At parity |
| Text Box Widget (imGUI_textBox) | ✅ Implemented | At parity |
| Text Label Widget (imGUI_textLabel) | ✅ Implemented | At parity |

# What's Left to Do (All opened as issues in the repository)

1) Closing the remaining feature-parity gaps with gr-bokehGUI
- **Trigger support** (Free/Auto/Normal/Tag modes, with slope/level/delay/channel/tag-key) across the Time Series, Frequency, and Constellation sinks
- **Max Hold and Averaging** (None/Low/Medium/High) on the Frequency and Vector sinks
- **Tag display** on the Time Series and Constellation sinks
- **Center-frequency / bandwidth calibration** on the Frequency and Waterfall sinks
- **Message-port (async) input** on the Frequency sink, so it can accept message streams in addition to sample streams
- **Block-level intensity range** on the Waterfall sink, and **block-level axis min/max** on the Constellation sink
- **Custom x-axis values vector** on the Vector sink

2) Pesky Bug: A fault sometimes occurs when closing a flowgraph containing the OOT's blocks, its completly random, not too big of an issue since it only occurs on closing. 

# A quick discussion of the architecture

The architecture of this OOT module was designed to be highly decoupled, made up of three primary components.

### 1. Within the Flowgraph Process (Dashboard Blocks & Management Singleton)
The GR4 flowgraph process consists of the `imGUI_dashboard_blocks` and a management singleton. The `imGUI_dashboard_blocks` are what you add to your actual flowgraph, these are the sinks and widgets, and they handle all the data processing and communication with the server via ZMQ IPC. 

Every time one of these dashboard blocks is instantiated, it registers itself with a central singleton class called the `imGUI_DashboardRegistry`. This registry is meant to track all the imGUI_dashBoard blocks in the flowgraph to create a configuration file which is needed to configure the dashBoard in the browser. Once all the blocks are registered, a JSON configuration file is generated and placed at (`/tmp/gr4_dashboard_config.json`). 

The `imGUI_DashboardRegistry` also handles the lifecycle of the dashBoard server process. It guarantees that the dashboard daemon is only booted once the flowgraph is ready and the config file is generated. It also tracks the total count of active imGUI blocks, automatically killing the daemon process and clearing the registry when the flowgraph stops and the last block is destroyed.

### 2. The Dashboard Server Process
Once triggered by the registry, the `dashboard_daemon` spins up as a completely independent process. This separation helps with **fault isolation**. By entirely decoupling the telemetry routing from the core DSP flowgraph, any crashes, network drops, or UI freezes on the web side will not interrupt your GNU Radio graph. If the server fails, the flowgraph keeps running without issue.

The dashboard server is built using [websocketpp](https://docs.websocketpp.org/). Internally, it operates using two primary threads to maintain high throughput:
*   **The Ingress Thread:** Dedicated to subscribing to the ZMQ streams coming from the sinks & widgets from the flowgraph.
*   **The Egress/Control Thread:** Handles packaging and pushing that data out to the dashBoard instances, while listening for incoming updates from the widgets in the dashBoard to send back to the flowgraph. This thread also handles initial HTTP GET requests from the browser, passing the generated JSON configuration file to the dashboard so it knows what UI elements to render.

### 3. The Web Dashboard (Emscripten)
This is the graphical frontend built using [imGUI](https://github.com/ocornut/imgui) & [imPlot](https://github.com/epezent/implot) where the user actually views and controls the flowgraph. The dashboard is compiled using [Emscripten](https://emscripten.org/), which translates the C++ ImGui framework into WebAssembly. 

Upon loading, the web dashboard makes an HTTP GET request to the dashboard server to retrieve the JSON configuration. It reads this file to determine exactly which panels, sinks, and widgets exist, using this data to dynamically build the UI layout. Once configured, it establishes a persistent WebSocket connection to the server to render real-time plot data and send control signals back to the flowgraph widgets.

# Go try it out ! 
If you don't have GR4 installed, head over to [gnuradio4](https://github.com/gnuradio/gnuradio4) and follow the build guide

Once you done or if you already have GR4 installed, head over to the [gr4-remotePlotting](https://github.com/ZiadFahmyZewailCity/gr4.0-remotePlotting/tree/master) and follow the build guide and tutorials to get using the OOT module.

Be sure to contact me if you run into any issues or have any questions
Email: s-ziad.fahmy@zewailcity.edu.eg 

# Challenges and Things I Learned
This project pushed me a bit outside my comfort zone.

*   **Modern C++ "Culture Shock":** GNU Radio 4.0 heavily leverages modern C++. Having never ventured this deep into the modern standards of the language, it was quite a shock at first. I eventually got familiar with programing concepts such as lambda expressions and the Curiously Recurring Template Pattern (CRTP).
*   **New Libraries & Network Programming:** This project also exposued me a lot more to networking and Inter-Process Communication (IPC) libraries, specifically ZeroMQ (ZMQ) and WebSocket++, which were really important for the server architecture.

*   **Linux Environment:** setting up the development environment was a hurdle that i had thankfully gotten ready early on. While I had dabbled with Linux in the past, I had never used it for full-scale development. I ultimately decided to use Windows Subsystem for Linux (WSL). Once I spent some time wrestling with the initial networking configurations, it turned out to be a nice environment to develop in and understanding how to tackle build tools and make files was a hassle.

*   **A Performance-Oriented Mindset:** Developing an OOT module for GR4 which is designed for Software Defined Radios (SDRs) made me mindful of keeping the OOT lightweight and high-performance.

# Acknowledgments
I would like to thank my mentors Josh M & Cyrille Morin for always being helpful and understanding. They gave me the space to explore and learn during the whole 12 weeks.


# My contact
Name: Ziad Haithem Fahmi
Email: s-ziad.fahmy@zewailcity.edu.eg
github: [ZiadFahmyZewailCity](https://github.com/ZiadFahmyZewailCity) 
[linkedin] (https://www.linkedin.com/in/ziad-fahmi-940216271/)