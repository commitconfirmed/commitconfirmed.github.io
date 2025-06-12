+++
title = "Network Diagram Guide - Intro"
date = "2025-06-12"
description = "A guide to drawing better Network Diagrams"
tags = [
    "network engineering",
    "network diagrams",
    "drawio",
    "visio",
    "excalidraw",
]
series = [
    "Network Diagram Guides"
]
series_order = 1
showComments = "true"
+++

## Introduction

In this series, I will go through some personal experiences, tools and recommendations when drawing Network Diagrams. 

This article deals with the tools I have used to document, plan & draw diagrams. I will also go over where each tool shines and tips & tricks to make your life easier. Again, this is based on personal experience since there are a lot of free and paid tools out there.

## Excel

Excel as a Network Diagram tool? I have surprisingly seen topology diagrams in excel before but generally it is used to draw up Elevation/Rack diagrams and data center floor plans.

Excel does shine here as it is very quick to mock something up and make quick changes while planning a build, but you should move this data to a proper DCIM product once you've completed your planning and are ready to build.

See an example Excel file below to see an example of what I have used in the past to plan a rack build. Also comes with an automatically updating Data tab that you can export as a CSV to import into another system once you're ready

[Example File](./static/cc-rack-design.xlsx)

## Excalidraw

[Excalidraw](https://excalidraw.com/) is a free online & open source whiteboard. It is now my replacement for the old MSPaint or a Physical whiteboard (especially in a post covid world). It is very quick to open up and start drawing whatever you want. Great for concepts or just having that nice whiteboard look on a presentation or wiki article. Being able to export as a SVG file as well ensures your drawing looks great no matter what size it is.

All the images on this blog have been drawn using it!

## Visio

Visio is the defacto tool that most engineers have come across in their careers to draw pretty much any type of diagram you can think of. It's great for Elevation, Physical, Logical, Data Flow, etc. diagrams and has the most vendor provided stencils of any solution. The downside of course is that it is not free, and the place you are working at may not have it included in their 365 subscription.  

## diagrams.net

[diagrams.net](https://app.diagrams.net/) (previously draw.io) is a free web-based diagram tool that is pretty much functionally the same as Visio. Compared to Visio it does offer a lot of advantages a few disadvantages:

Advantages:

- Free
- Runs on your browser, also has an optional offline App
- Ability to export as SVG
- Integration with GitHub, Notion & Confluence/JIRA

Disadvantages:

- Limited Networking Vendor and other Stencils

This is my personal tool of choice these days to do my Network Diagramming unless I need to present very specific diagrams that go down to the physical hardware level.

## Tips & Tricks

TBD
