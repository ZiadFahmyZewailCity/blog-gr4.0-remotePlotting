---
layout: post
title: "Solutions to core architecture challenges!"
author: "Ziad Haithem Fahmi"
---

## Hello everyone !

Very exciting news, the "what needs to be done next" list from my previous blog post is officially complete.

Here is a deep dive into exactly how i achieved them.

### 1) Getting the flowgraph to start the dashboard server process

To handle this, I used a C++ singleton class called `imGUI_DashboardRegistry` which lives in the process space of the main thread of the flowgraph. 

Once the start function of the OOT blocks is reached, a method called `boot_dashboardServer_Once()` is triggered. This method locks a mutex and checks a simple boolean flag (`is_server_booted`) to ensure we only spawn the server once. If the server isn't running, it triggers a system call executing `"./dashBoard_daemon &"` to get the background process up and running. 

What about cleanup? When the flowgraph stops, the blocks call `unregisterBlockAndTeardown()`. This decrements the count of active blocks, and once that count hits zero, it executes a `"pkill dashboard_daemon"` system call to cleanly kill the server process and clear the registry.

### 2) Making the dashboard generate the config file

This was also handled by our trusty `imGUI_DashboardRegistry` singleton. 

In their constructors, the OOT blocks register themselves by passing a lambda function to `register_imGUI_block()`. These lambdas are stored in a vector called `ptrs_to_imGUIblocks_callbacks`. Right before the server daemon is booted, the registry runs `config_fileGenerator()`. This function loops through all the stored callbacks, executing them to pull the absolute latest raw JSON string defining each block. 

It then parses these strings, appends them to a `dashboardElement` JSON array under the main panel, and dynamically writes the final `config.json` file straight to the disk. 

### 3) Sending updates of the widget values alongside the sink data

The widgets in the flowgraph now send updates about their current state to all dashboard instances given 3 conditions: a new dashboard instance connects, a widget changes state, or the flowgraph makes a change to a monitored variable.

For example, when a new client connects, the WebSocket `on_open()` handler immediately fires off a `"SERVER:SYNC"` command. Using `dispatch_internal_message()`, this command is pushed over a persistent ZMQ `inproc://commands` socket to the ZMQ polling thread[cite: 6, 7]. The flowgraph intercepts this command and queries the blocks for their current status using their `get_external_val` lambdas (which return the persistent states, like the `freq_toggle_state` for checkboxes and dropdowns).

This ensures that whenever a user opens the dashboard, the widgets instantly snap to the actual, current parameters running in the DSP flowgraph.

Both points 1 & 2 utilized that C++ singleton living in the main flowgraph process space. This approach turned out to be incredibly effective. By keeping this singleton in the main process, we guarantee that the dashboard server setup happens automatically upon startup without complicating the flowgraph's internal logic or sacrificing the fault isolation we aimed for in the core architecture.

Thanks for reading, if you have any, please do send feedback to me via email or on the GNU Radio matrix, see you next week !

[The code for this can be found on the V1_Dev branch of the the repo](https://github.com/ZiadFahmyZewailCity/gr4.0-remotePlotting/tree/V1_Dev)