# Resource Utilisation Analysis

## VoIP Media Bypass and Central Server Load Reduction

**Author:** Mohammad Sorower Jahan  
**Organisation:** Tech News 365 IT Institute, Bangladesh  
**Role:** Senior Lecturer & IT Research and Innovation Lead  
**Employment period:** 5 January 2015 – 30 December 2021  
**Technology:** Asterisk, SIP, SDP and RTP

---

## 1. Purpose

This document explains the resource impact observed when RTP media was removed from the central Asterisk path where direct media was technically possible.

The project did not attempt to eliminate SIP signalling from Asterisk.

Asterisk remained responsible for:

- call establishment
- dialplan processing
- destination selection
- SIP signalling
- session control
- call termination

The optimisation focused on reducing unnecessary RTP media relay.

---

## 2. Original Resource Model

Before optimisation, both SIP signalling and RTP media passed through Asterisk.

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

This meant the central server handled:

- signalling traffic
- incoming RTP
- outgoing RTP
- media-related socket activity
- call state
- routing logic

The continuous RTP workload became increasingly significant as concurrent call volume increased.

---

## 3. Optimised Resource Model

After optimisation, Asterisk continued handling signalling but did not remain in the RTP path for eligible calls.

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

Caller <============== RTP ==============> Provider
```

This reduced the amount of continuous traffic processed by the central server.

---

## 4. Why RTP Creates Significant Load

SIP signalling is comparatively intermittent.

A typical call generates signalling during events such as:

- call setup
- negotiation
- changes to the call
- termination

RTP is different.

Once a call is active, RTP packets continue to flow for the duration of the voice session.

For many simultaneous calls, this produces sustained traffic.

Conceptually:

```text
Call 1 RTP
Call 2 RTP
Call 3 RTP
...
Call 100+ RTP
       |
       v
    Asterisk
```

Removing the central server from this path reduces the amount of packet handling required from Asterisk.

---

## 5. CPU Utilisation

When Asterisk relays RTP, the server continuously receives and forwards media packets.

This creates processing work associated with:

- packet reception
- socket handling
- packet forwarding
- call/media session handling
- kernel networking
- context switching
- media-related processing

With more simultaneous calls, this work accumulates.

Media bypass reduced the amount of RTP-related processing required from the central server.

The historical project environment showed a noticeable reduction in overall server load after this change.

The original detailed CPU measurements are no longer available.

---

## 6. Memory Utilisation

Asterisk still required memory for:

- active call state
- SIP sessions
- dialplan execution
- channel information
- signalling-related resources

Media bypass therefore did not eliminate memory use for an active call.

However, reducing the amount of central media handling was associated with lower overall resource pressure in the project environment.

The original per-call memory measurements are no longer available.

For this reason, this repository does not claim a precise percentage reduction in memory utilisation alone.

---

## 7. Central Server Bandwidth

The network impact is easier to visualise.

### Before optimisation

```text
Caller
  |
 RTP
  v
Asterisk
  |
 RTP
  v
Provider
```

The central server's network path carried the media stream in both directions.

### After optimisation

```text
Caller
  |
 RTP
  |
  +---------------------------> Provider
```

The RTP traffic still existed, but it no longer needed to traverse the central Asterisk server for eligible calls.

The bandwidth improvement therefore concerned:

**Asterisk-side network utilisation**

rather than a reduction in the codec's inherent bandwidth requirement.

---

## 8. Bidirectional Media Load

Voice communication is bidirectional.

Before media bypass:

```text
Caller -------- RTP --------> Asterisk -------- RTP --------> Provider
Caller <------- RTP --------- Asterisk <------- RTP --------- Provider
```

The central server handled both directions.

After media bypass:

```text
Caller <===================== RTP =====================> Provider
```

Asterisk continued to handle signalling while the RTP payload travelled separately.

---

## 9. High-Concurrency Impact

The architecture was tested with more than:

**100 simultaneous calls**

At this scale, the cumulative RTP workload became important.

For example:

```text
100+ calls
   |
   v
100+ simultaneous media sessions
   |
   v
Continuous RTP packet flow
   |
   v
Central server processing and bandwidth demand
```

The optimisation reduced the media-related portion of this workload where direct RTP was possible.

---

## 10. Historical Observation

The project produced an approximate historical observation of:

**around 50% overall reduction in central-server resource and media-path load**

after the media-bypass architecture was applied.

The observed improvement related broadly to:

- CPU utilisation
- memory utilisation
- central-server bandwidth usage

The figure should not be interpreted as:

```text
CPU exactly 50%
RAM exactly 50%
Bandwidth exactly 50%
```

That level of precision is not supported by the surviving evidence.

Instead, approximately 50% describes the overall engineering improvement observed in the project environment.

---

## 11. Why the Result Was Significant

The optimisation changed the role of Asterisk from:

```text
Signalling + Call Control + Media Relay
```

towards:

```text
Signalling + Call Control
```

for calls where media bypass was available.

This allowed server resources to be concentrated on functions that actually required central processing.

The result was especially relevant at higher call concurrency.

---

## 12. What Was Not Reduced

The optimisation did not eliminate:

- RTP itself
- endpoint processing
- provider-side media processing
- signalling traffic
- call-state management
- dialplan execution

The media was simply moved away from the central relay path.

This distinction is important.

---

## 13. What Was Reduced

The change reduced the central server's involvement in:

- RTP forwarding
- media-path network traffic
- continuous packet relay
- media-related processing
- unnecessary central bandwidth use

This reduced the amount of infrastructure work performed by Asterisk for each eligible call.

---

## 14. Signalling Load Remained

Asterisk continued receiving and sending SIP messages.

For example:

```text
Caller
   |
   | INVITE / SIP signalling
   v
Asterisk
   |
   | SIP signalling
   v
Provider
```

The signalling architecture was therefore not removed.

The optimisation specifically targeted the sustained RTP workload rather than signalling.

---

## 15. Media Load Was Offloaded

The media relationship became:

```text
Caller <================ RTP ================> Provider
```

rather than:

```text
Caller <==== RTP ====> Asterisk <==== RTP ====> Provider
```

This is why the project can be described as media offloading as well as media bypass.

---

## 16. Scalability Effect

The engineering benefit increases as concurrent call volume rises.

With only a few calls, the resource difference may not be large.

With more than 100 simultaneous calls, the cumulative effect of relaying RTP through one server becomes more significant.

The optimisation therefore supported better use of the central VoIP infrastructure.

---

## 17. Factors Affecting Resource Usage

The exact resource impact of media bypass depends on the environment.

Relevant factors include:

- number of simultaneous calls
- codecs
- packetisation interval
- server CPU
- available RAM
- operating system
- kernel/network stack
- network interface capacity
- Asterisk version and configuration
- transcoding requirements
- recording requirements
- NAT conditions
- provider configuration

For this reason, the project-specific result should not be treated as a universal benchmark.

---

## 18. Transcoding

If Asterisk needs to transcode between different codecs, it generally needs access to the media stream.

In those cases, direct RTP may not be appropriate.

Transcoding can also create additional CPU load.

The historical project focused on calls where the architecture permitted the RTP media path to bypass the central server.

---

## 19. Recording and Other Media Services

Asterisk may need to remain in the media path for services such as:

- call recording
- conferencing
- announcements
- media manipulation
- monitoring
- certain DTMF applications
- transcoding

The optimisation therefore applied to calls where these central media functions were not required.

---

## 20. Network Conditions

Direct media also depends on connectivity between the two media endpoints.

Potential restrictions include:

- NAT
- firewall rules
- private IP addressing
- routing
- provider restrictions
- SDP address advertisement

Where direct RTP cannot be established, central media relay may still be necessary.

---

## 21. Before and After Resource Relationship

### Before

```text
Call Volume
    |
    v
SIP Signalling
    +
RTP Media
    |
    v
Asterisk
    |
    v
Increasing CPU / Memory / Network Load
```

### After

```text
Call Volume
    |
    +------> SIP Signalling ------> Asterisk
    |
    +------> RTP Media -----------> Provider
```

The media workload was no longer concentrated on the Asterisk server for eligible calls.

---

## 22. Engineering Interpretation

The observed improvement was not caused by a single software tuning parameter.

It resulted from changing the architecture.

Instead of attempting only to optimise:

- CPU settings
- memory limits
- operating-system parameters
- process priorities

the project removed an unnecessary class of traffic from the central server.

This is an architectural optimisation rather than merely a server-tuning exercise.

---

## 23. Why Architecture Matters

A server can often be made faster by adding:

- CPU cores
- RAM
- faster network interfaces

However, another approach is to reduce unnecessary work.

The project followed the second approach.

Instead of making Asterisk process more RTP efficiently, the design asked:

**Does Asterisk need to process this RTP at all?**

Where the answer was no, the media path was allowed to bypass the server.

---

## 24. Resource Efficiency Principle

The engineering principle can be summarised as:

```text
Do not consume central resources
for work that does not require central processing.
```

In this project:

```text
SIP signalling
       |
       v
Requires Asterisk


Direct RTP
       |
       v
Does not always require Asterisk
```

This separation improved resource efficiency.

---

## 25. Test Scale

The project was tested with:

**100+ simultaneous calls**

The precise maximum test count is no longer available.

The repository therefore records the test scale as more than 100 calls rather than presenting an unsupported exact number.

---

## 26. Measurement Limitations

The original:

- performance screenshots
- CPU graphs
- memory graphs
- network graphs
- monitoring logs
- server configurations
- test reports

are no longer available.

The exact monitoring application used during the historical comparison is also not being stated because it cannot now be established confidently.

The approximately 50% improvement should therefore be treated as a historical project observation.

---

## 27. Evidence Boundary

This repository does not claim that the current Markdown documentation proves the exact historical resource figures.

The documentation reconstructs:

- the architecture
- the engineering reasoning
- the call scale
- the observed approximate improvement

from the original project work.

Current diagrams were created later for explanatory purposes.

---

## 28. Appropriate Description of the Result

The most accurate description of the historical result is:

> In the project environment, removing unnecessary Asterisk involvement from the RTP media path was associated with an approximately 50% overall reduction in central-server resource and media-path load during high-concurrency testing.

This should not be shortened into a claim that every Asterisk deployment will experience an exact 50% reduction.

---

## 29. Historical Context

This work was carried out during my employment at:

**Tech News 365 IT Institute, Bangladesh**

where I worked as:

**Senior Lecturer & IT Research and Innovation Lead**

from:

**5 January 2015 to 30 December 2021**

The repository was created later as a technical reconstruction of the original engineering work.

---

## 30. Summary

The resource optimisation can be summarised as:

```text
BEFORE

Caller
   |
   | SIP + RTP
   v
Asterisk
   |
   | SIP + RTP
   v
Provider


AFTER

SIP:
Caller -> Asterisk -> Provider

RTP:
Caller <-----------> Provider
```

The architectural change reduced unnecessary central RTP processing.

Testing with more than 100 simultaneous calls indicated approximately 50% overall improvement in central-server resource and media-path load in the project environment.

---

## Author

**Mohammad Sorower Jahan**

**Role during the project:** Senior Lecturer & IT Research and Innovation Lead

Technical areas:

VoIP | SIP | SDP | RTP | Asterisk | Linux | Telecommunications Infrastructure | Resource Optimisation | Systems Architecture
