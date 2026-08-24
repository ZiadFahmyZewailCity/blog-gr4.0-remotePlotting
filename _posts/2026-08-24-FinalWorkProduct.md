---
layout: post
title: "GSoC 2026 Final Work Product: gr4-remotePlotting OOT for GR4"
author: "Ziad Haithem Fahmi"
---

If you want to get straight to the code, [here is the link to the repository]() you will find everything you need to get this Out Of Tree (OOT) module running with [GNU Radio 4.0]() (GR4).

# Overview
This GSoC project has developed an OOT module for [GR4]() that is a successor to the OOT module gr-bokehGUI for GR3.x. It's meant to allow for remote plotting and interactivity with a GR4 flowgraph by creating an imGUI dashboard containing sinks and widgets that runs in the browser. This is especially useful for monitoring and interacting with flowgraphs on headless devices such as embedded systems.

Place a video here
[Here is a quick taste of what the module is capable of]()

# Current State
The project successfully implemented all its deliverables being the following sinks & widgets, although some features are yet to be supported to acheive full feature parity with gr-bokehGUI. 

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

# Go try it out ! 

# Challenges and Things I Learned

# Acknolwedgments