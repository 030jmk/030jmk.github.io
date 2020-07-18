---
layout: post
title:  "Public Transport Departures"
date:   2020-01-04 12:00:00 +0200
categories: [bots and scripts]
---

Annoyed by very irregular departure times of a specific bus line run by the local public transportation company I wrote a python script that displays delays on a html page when run. The script can be run on demand or with a time-based job scheduler such as cron and can be combined with a web server.

Information is called through an API provided by the HaCon (Siemens) Fahrplan-Auskunfts-System (HAFAS) and requires an API key. Bootstrap is the CSS framework chosen for the generation of the html file in order to be easily viewable on mobile and desktop devices.

[direct link to the python script](https://gist.githubusercontent.com/030jmk/b9706ab802a5b64a04c804f2c4b1393f/raw/529370b051b2ee7687c6602d2354d21ac402e3b5/VBB.py)

