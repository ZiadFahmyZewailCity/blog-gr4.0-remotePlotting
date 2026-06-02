---
layout: post
title: "POC Complete!"
author: "Ziad Fahmi"
---

Hello everyone! Happy to say that the proof of concept for the OOT module is complete.

First, I'll give a general overview of how the OOT module is intended to work, then get into what the POC specifically achieved.

---

## How the OOT Module Works — The Concept

### What lives in the browser

The browser-based dashboard is written in C++ and compiled to WebAssembly (WASM) using Emscripten. It's configured via a `.json` config file that specifies each panel, the elements within each panel (plots, sliders, text labels, etc.), and an ID for the data source of each element that will have data pushed to it.

### What lives in the GR4.0 Flowgraph

Blocks for each kind of plot or widget can be placed directly in the GR4 flowgraph and connected to the data sources or variables you want to visualize or control — very similar to how the old [gr-bokehGUI](https://www.youtube.com/watch?v=0LNlwYrcjps) worked.

Hopfully ill be able to handle all networking  in the background, so users won't need a separate networking block cluttering their flowgraph.

### What happens when you run the flowgraph

1. An HTTP server serves the pre-compiled WASM dashboard to the browser.
2. The config file is sent to the dashboard to set its layout.
3. The flowgraph begins pushing data and receiving widget commands, routed via the data source IDs defined in the config.
4. ~~Profit!~~

---

## What the POC Achieved

The POC successfully demonstrates **bi-directional communication** between a GR4 flowgraph and the configurable dashboard: plotting live signal data coming from the flowgraph, and varying the frequency of a sine wave in real time via a slider widget in the browser.

### The Flowgraph

```
SignalGenerator → SimCompute → Bridge
```

- **SignalGenerator**: Produces a sine wave (amplitude 1, initial frequency 50 Hz)
- **SimCompute**: Throttles throughput to 4800 samples/sec
- **Bridge** (`dashBoardBridge`): Custom GR4 block that handles networking, streams data to the dashboard, and receives slider commands back from the browser

### Differences from the final architecture

The POC intentionally simplifies a few things for speed:

- Networking, data streaming, and widget command handling are all bundled into the single `Bridge` block, rather than being split into separate, blocks
- The HTTP server that serves the WASM file to the browser was started manually, rather than being launched automatically on flowgraph start (which is what the final OOT will do)

---

Successfully achieving bi-directional communication between a live GR4 flowgraph and the configurable browser dashboard confirms that the general concept for the OOT module is sound.

[Here is a video showing the POC working](https://drive.google.com/file/d/1O4apwZWeQtU7gXqFTuRemnZYRrEJB-8U/view?usp=sharing)
