---
layout: post
title: "Widgets are Alive, but Sinks Need Work"
author: "Ziad Haithem Fahmi"
---

# Hello everyone

Exciting news, the widgets of the OOT module are officially functional, while unfortunatly the sinks i am having a bit of trouble with.

---

# The Good News: Functional Widgets

Here is a breakdown of the widgets currently available and fully working in the dashboard:
* **Range slider**: Draggable range of values.
* **Button**: Clickable button that can trigger a callback.
* **Checkbox**: Toggleable box for switching between binary states.
* **Dropdown**: Menu of selectable options which can be collapsed.
* **TextLabel**: Displays text on the interface.
* **TextBox**: Input field the user can enter text into.

---

# The Mixed News: Sink Status

The great news is that the **time sink** and the **frequency sink** are both fully functional.

However, the **vector sink** is currently giving me quite a bit of trouble and misbehaving during the work tick. It is something I will need to spend some time debugging over the next week. Additionally, because of this roadblock, I haven't gotten around to implementing the **constellation sink** and the **waterfall sink** just yet.

---

# What needs to be done next

I need to get the vector sink debugged and the other remaining sinks working as there is still alot of work done adding features to all of the widgets and sinks

Thanks for reading! If you have any feedback or suggestions, please do send them to me via email or on the GNU Radio matrix. 

[The code for this can be found on the V1_Dev branch of the the repo](https://github.com/ZiadFahmyZewailCity/gr4.0-remotePlotting/tree/V1_Dev)