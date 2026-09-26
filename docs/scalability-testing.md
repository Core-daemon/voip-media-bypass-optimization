# Scalability and High-Concurrency Testing

## VoIP Media Bypass Under 100+ Simultaneous Calls

**Author:** Mohammad Sorower Jahan  
**Organisation:** Tech News 365 IT Institute, Bangladesh  
**Role:** Senior Lecturer & IT Research and Innovation Lead  
**Employment period:** 5 January 2015 – 30 December 2021  
**Technology:** Asterisk, SIP, SDP and RTP

---

## 1. Purpose

This document describes the high-concurrency testing carried out as part of the VoIP media-bypass project.

The purpose of the testing was to examine the behaviour of the Asterisk-based architecture when the number of simultaneous calls increased.

The principal comparison was between:

```text
Asterisk remaining in both
the SIP signalling and RTP media path
```

and:

```text
Asterisk remaining in the SIP signalling path
while eligible RTP media bypassed the server
```

The project was tested with more than 100 simultaneous calls.

---

## 2. Why Concurrency Was Important

A media-path optimisation is more meaningful when evaluated under multiple concurrent calls.

With a small number of calls, the resource difference between the two architectures may be relatively modest.

As call concurrency increases, the amount of continuous RTP traffic can increase substantially.

Conceptually:

```text
1 call
   |
   v
1 RTP session

10 calls
   |
   v
10 simultaneous RTP sessions

100+ calls
   |
   v
100+ simultaneous RTP sessions
```

When all of those media streams pass through one central server, the cumulative workload becomes more significant.

---

## 3. Original High-Concurrency Architecture

Before media bypass, the logical call path was:

```text
Extension / Caller
        |
      SIP + RTP
        |
        v
     Asterisk
        |
      SIP + RTP
        |
        v
 SIP Trunk Provider
```

At higher concurrency, Asterisk therefore handled:

- SIP signalling for every call
- active call state
- incoming RTP streams
- outgoing RTP streams
- central network traffic
- continuous media packet forwarding

The RTP portion of this workload continued for the duration of each active call.

---

## 4. Optimised High-Concurrency Architecture

After the media-path optimisation, the architecture became:

```text
SIGNALLING

Caller
   |
  SIP
   v
Asterisk
   |
  SIP
   v
Provider


MEDIA

Caller <=============== RTP ===============> Provider
```

Asterisk remained responsible for call control.

However, where direct media could be established, it did not need to relay the corresponding RTP streams.

---

## 5. Test Scale

The historical implementation was tested with:

**more than 100 simultaneous calls**

The exact maximum concurrent-call count is no longer available.

For this reason, this repository deliberately records the test scale as:

```text
100+ simultaneous calls
```

rather than introducing a more precise figure that cannot now be supported.

---

## 6. Testing Objective

The objective was not simply to prove that an individual call could use direct RTP.

The more important engineering question was whether separating signalling from media continued to provide a practical benefit when many calls were active at the same time.

The testing therefore focused on:

- maintaining successful call establishment
- maintaining SIP call control through Asterisk
- maintaining bidirectional audio
- allowing eligible RTP traffic to bypass Asterisk
- observing central-server resource behaviour
- comparing central-server workload before and after media bypass

---

## 7. Signalling at Scale

Even after optimisation, Asterisk continued to manage the SIP sessions.

For more than 100 simultaneous calls, the signalling relationship remained conceptually:

```text
Call 1 SIP  \
Call 2 SIP   \
Call 3 SIP    \
...            ---> Asterisk ---> SIP Trunk Provider
Call 100+ SIP /
```

Asterisk therefore continued to provide the central call-control function.

The project did not attempt to remove this responsibility.

---

## 8. Media at Scale Before Optimisation

Before bypass, the corresponding media relationship was approximately:

```text
Call 1 RTP  \
Call 2 RTP   \
Call 3 RTP    \
...            ---> Asterisk ---> Provider media side
Call 100+ RTP /
```

and the reverse RTP direction also returned through Asterisk.

This concentrated the media traffic on the central server.

---

## 9. Media at Scale After Optimisation

After media bypass, eligible sessions followed a more direct media path:

```text
Call 1:   Caller <====== RTP ======> Provider
Call 2:   Caller <====== RTP ======> Provider
Call 3:   Caller <====== RTP ======> Provider
...
Call 100+: Caller <===== RTP ======> Provider
```

Asterisk still handled the SIP signalling for those calls.

The difference was that the continuous RTP payload did not need to remain concentrated on the central server.

---

## 10. Why the Difference Grows With Call Volume

Each additional active RTP session adds more continuous media traffic.

If Asterisk relays the media:

```text
More calls
    |
    v
More RTP packets through Asterisk
    |
    v
More network activity
    |
    v
More central processing
```

With media bypass:

```text
More calls
    |
    +------ SIP signalling ------> Asterisk
    |
    +------ RTP -----------------> Media endpoints
```

This reduces the growth of the media-related workload on the central Asterisk server.

---

## 11. Call Establishment Requirement

A successful optimisation still required calls to establish correctly.

The expected sequence remained:

```text
1. Caller initiates SIP session.

2. Asterisk receives the request.

3. Asterisk processes the call.

4. Asterisk establishes the provider-side SIP leg.

5. Media parameters are negotiated.

6. Where technically possible, RTP is exchanged directly.

7. Asterisk continues maintaining the SIP session.

8. Asterisk processes termination when the call ends.
```

The media optimisation therefore depended on preserving normal call-control behaviour.

---

## 12. Bidirectional Audio

Media bypass is only useful if both RTP directions work.

A successful media relationship requires:

```text
Caller -------- RTP --------> Provider
Caller <------- RTP --------- Provider
```

not merely one of those directions.

The project testing therefore included confirmation that voice media operated bidirectionally under the media-bypass architecture.

---

## 13. Central CPU Behaviour

With Asterisk relaying media, higher concurrency increases packet-processing work.

The server must continuously handle media packets associated with all active sessions.

Removing eligible RTP streams from the central path reduced this continuous workload.

The historical testing indicated lower overall central-server resource pressure after media bypass was applied.

The original CPU graphs are no longer available.

---

## 14. Central Memory Behaviour

Asterisk still required memory for active call state.

Therefore increasing call concurrency still increased some resource requirements even after media bypass.

The optimisation did not remove:

- SIP channel state
- call-state information
- dialplan-related state
- signalling-related resources

However, the overall central resource pressure was observed to improve when unnecessary media handling was removed.

---

## 15. Network Interface Behaviour

One of the most direct architectural differences involved the central network interface.

Before:

```text
100+ media sessions
       |
       v
Asterisk network interface
       |
       v
Provider side
```

After:

```text
100+ media sessions
       |
       v
Direct endpoint-to-provider media paths
```

This reduced the concentration of RTP traffic on the Asterisk server.

---

## 16. Historical Resource Observation

During the project, the optimised architecture was associated with an approximate:

**50% overall reduction in central-server resource and media-path load**

in the implementation environment.

This was an overall historical engineering observation.

It should not be interpreted as proof of exactly:

```text
50% CPU reduction
50% RAM reduction
50% bandwidth reduction
```

for each individual metric.

The surviving information does not support that level of precision.

---

## 17. What the Test Demonstrated

The testing indicated that Asterisk could remain responsible for:

- SIP signalling
- call routing
- call state
- dialplan processing
- call termination

while avoiding unnecessary central RTP relay for eligible calls.

This was significant because signalling and media place different types of demand on the infrastructure.

---

## 18. What Was Not Tested as a Universal Benchmark

The project was not intended to establish a universal maximum call capacity for Asterisk.

The result should therefore not be interpreted as:

```text
Asterisk supports exactly X calls
```

or:

```text
Media bypass always doubles capacity
```

Maximum capacity depends on many environmental factors.

---

## 19. Factors Affecting Maximum Call Capacity

Relevant factors include:

- server processor
- number of CPU cores
- available RAM
- network-interface capacity
- codec
- packetisation
- Asterisk configuration
- transcoding
- recording
- conferencing
- NAT
- operating-system configuration
- provider behaviour
- endpoint capabilities
- call duration and traffic characteristics

The 100+ call figure therefore describes the scale of the original project testing, not a general product specification.

---

## 20. Transcoding Considerations

If Asterisk must transcode between codecs, it generally needs to remain involved in the media path.

Transcoding can also significantly increase CPU demand.

The media-bypass architecture is therefore most effective where compatible media parameters can be negotiated without central transcoding.

---

## 21. Recording and Conferencing

Some features require access to RTP.

Examples include:

- call recording
- conferencing
- media announcements
- media analysis
- monitoring
- transcoding

Calls using those functions may need Asterisk to remain in the media path.

For this reason, the scalability benefit depends partly on how many calls are eligible for media bypass.

---

## 22. NAT and Reachability

Direct RTP depends on endpoint reachability.

Potential obstacles include:

- NAT
- private addressing
- firewall restrictions
- incorrect SDP addresses
- routing restrictions
- provider policy

High concurrency does not change this fundamental requirement.

Every direct-media session must still have a valid bidirectional media path.

---

## 23. Architectural Scalability

The project approached scalability through architecture rather than only through additional hardware.

Instead of asking only:

```text
How can Asterisk process more RTP?
```

the project also asked:

```text
Which RTP traffic actually needs to pass through Asterisk?
```

Where central media processing was unnecessary, the media stream was allowed to bypass the server.

---

## 24. Horizontal Effect of Media Offloading

Consider a growing call environment.

Without bypass:

```text
10 calls   -> 10 RTP sessions through Asterisk
50 calls   -> 50 RTP sessions through Asterisk
100 calls  -> 100 RTP sessions through Asterisk
100+ calls -> 100+ RTP sessions through Asterisk
```

With successful media bypass:

```text
10 calls   -> mainly signalling through Asterisk
50 calls   -> mainly signalling through Asterisk
100 calls  -> mainly signalling through Asterisk
100+ calls -> mainly signalling through Asterisk
```

This does not eliminate the cost of signalling, but it changes the rate at which media-related workload accumulates on the central server.

---

## 25. Reliability Consideration

Performance improvement is not useful if it causes unstable calls.

The project therefore treated successful communication as a prerequisite.

The architecture needed to maintain:

- successful call establishment
- bidirectional audio
- call continuity
- normal call termination

while reducing unnecessary media relay.

---

## 26. Historical Test Records

The original:

- concurrency logs
- call-generation records
- performance graphs
- server screenshots
- SIP traces
- RTP captures
- resource monitoring output
- Asterisk configuration

are no longer available.

This limits the level of numerical analysis that can responsibly be presented today.

---

## 27. Measurement Tool

The exact historical monitoring application used for the resource comparison has not been established confidently.

The repository therefore does not assign the historical approximately 50% result to a particular monitoring utility.

This avoids reconstructing a technical detail that cannot now be verified.

---

## 28. Evidence Boundary

The following points are retained as historical engineering recollection:

- testing at more than 100 simultaneous calls
- Asterisk remaining in the SIP signalling path
- RTP bypassing Asterisk where technically possible
- successful bidirectional media
- lower overall central-server load
- approximately 50% overall improvement in central-server resource and media-path load

The original technical measurements are no longer available.

---

## 29. Appropriate Interpretation

The appropriate conclusion is not:

```text
Media bypass guarantees 50% more capacity.
```

A more accurate project-specific conclusion is:

```text
During testing with more than 100 simultaneous calls,
removing unnecessary RTP relay substantially reduced the
workload placed on the central Asterisk server in the
original implementation environment.
```

---

## 30. Historical Context

The work was carried out during my employment at:

**Tech News 365 IT Institute, Bangladesh**

where I worked as:

**Senior Lecturer & IT Research and Innovation Lead**

from:

**5 January 2015 to 30 December 2021**

The repository was created later as a retrospective technical record.

---

## 31. Scalability Summary

The project can be summarised as:

```text
BEFORE

100+ simultaneous calls
         |
         v
   SIP signalling
         +
      RTP media
         |
         v
      Asterisk


AFTER

100+ simultaneous calls
         |
         +---- SIP signalling ----> Asterisk
         |
         +---- RTP media ---------> Direct media path
```

The central engineering result was that high call concurrency no longer required every eligible RTP stream to remain concentrated on the Asterisk server.

---

## Author

**Mohammad Sorower Jahan**

**Role during the project:** Senior Lecturer & IT Research and Innovation Lead

Technical areas:

VoIP | SIP | SDP | RTP | Asterisk | Linux | Telecommunications Infrastructure | Scalability | Media Architecture
