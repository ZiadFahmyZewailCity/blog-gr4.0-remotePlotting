---
layout: post
title: "Everything Is Functional"
author: "Ziad Haithem Fahmi"
---

# Hello everyone

All the widgets and sinks are now functional ! 

I had previously had issues with the 

1- **Vector sink**
2- **waterfall sink**
3- **constellation sink**

But i have managed to get them all working

## An interesting note
The vector sink was the one that was causing the most trouble, trying to pass actual vectors into GR4 ports was my mistake. Using GR4s dataset data structure is what finally got the vector sink working.

Took a bit of refactoring once i discovered that the issue was with trying to use vectors but it just goes to show that sometimes you have to be really carful about *what* actually works within the frame work your using or *what* am i actually meant to utilize here.

# Whats next

I am in the final strech of work now

Now its all about adding features, tackling bugs and testing. I created a long list of issues on the repository which i am tackling

Thank you for reading and [the code for this can be found on the V1_Dev branch of the the repo](https://github.com/ZiadFahmyZewailCity/gr4.0-remotePlotting/tree/V1_Dev)
