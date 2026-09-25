# VoIP Media Bypass Optimisation

## RTP Media Offloading and Infrastructure Resource Optimisation

**Author:** Mohammad Sorower Jahan  
**Engineering Area:** VoIP, RTP, SIP, Telecommunications Infrastructure and Systems Optimisation

---

## Project Overview

This repository documents an engineering project in which I investigated unnecessary media processing within a VoIP infrastructure and redesigned the call architecture to reduce the amount of RTP traffic passing through central processing components.

The original arrangement required intermediate VoIP systems to remain in the media path even where continuous media processing was not technically necessary.

I investigated an alternative architecture in which signalling and call control could remain on the required systems while RTP media was allowed to follow a more direct path where the network design permitted it.

The objective was to reduce unnecessary use of:

- CPU resources
- system memory
- network bandwidth
- media-processing capacity

The project produced an observed reduction of approximately 50% in CPU, RAM and bandwidth utilisation in the implementation environment.

Detailed architecture, testing methodology and evidence boundaries will be documented separately in this repository.
