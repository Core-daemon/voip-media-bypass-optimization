# SIP Signalling and RTP Media Flow

## VoIP Media Bypass Architecture

**Author:** Mohammad Sorower Jahan  
**Organisation:** Tech News 365 IT Institute, Bangladesh  
**Role:** Senior Lecturer & IT Research and Innovation Lead  
**Employment period:** 5 January 2015 – 30 December 2021  
**Technology:** Asterisk, SIP, SDP and RTP

---

## 1. Purpose

This document explains the signalling and media behaviour of the VoIP media-bypass architecture documented in this repository.

The main engineering change was to separate two functions that had previously followed the same network path:

- SIP signalling and call control
- RTP voice media

Asterisk remained responsible for the signalling and call-control functions.

Where the network and connected systems permitted direct media, RTP was allowed to flow between the caller side and the SIP trunk provider without continuing through the Asterisk server.

---

## 2. Original Call Path

Before the optimisation, both signalling and media passed through Asterisk.

The simplified architecture was:

```text
Extension / Caller
        |
        | SIP
        | RTP
        v
     Asterisk
        |
        | SIP
        | RTP
        v
 SIP Trunk Provider
```

Asterisk therefore performed two different roles at the same time:

```text
Call signalling/control
          +
RTP media relay
```

This worked correctly, but it meant that every active voice session continuously generated traffic through the central server.

---

## 3. Optimised Call Path

After the architecture was changed, the two paths were treated separately.

### Signalling

```text
Extension / Caller
        |
       SIP
        |
        v
     Asterisk
        |
       SIP
        |
        v
 SIP Trunk Provider
```

### Media

```text
Extension / Caller
        |
       RTP
        |
        +---------------------------->
                              SIP Trunk Provider
```

Asterisk therefore remained responsible for controlling the call while avoiding unnecessary RTP relay where direct media was possible.

---

## 4. Signalling Plane

The SIP signalling path continued to pass through Asterisk.

Conceptually:

```text
Caller
   |
   | SIP signalling
   v
Asterisk
   |
   | SIP signalling
   v
SIP Trunk Provider
```

Asterisk continued to perform functions such as:

- accepting the call request
- processing the dialplan
- determining the destination
- establishing the provider-side call leg
- participating in media negotiation
- maintaining call state
- processing call termination

Media bypass therefore did not remove Asterisk from the telecommunications architecture.

---

## 5. Media Plane Before Optimisation

Before media bypass, RTP travelled through the central server.

```text
Caller
   |
   | RTP
   v
Asterisk
   |
   | RTP
   v
Provider
```

The reverse media stream also travelled through Asterisk:

```text
Provider
   |
   | RTP
   v
Asterisk
   |
   | RTP
   v
Caller
```

This meant that Asterisk handled the continuous media traffic in both directions.

---

## 6. Media Plane After Optimisation

After optimisation, the intended RTP path was:

```text
Caller <================ RTP ================> Provider
```

Asterisk remained outside the continuous RTP path for calls where direct media could be established successfully.

The architecture therefore became:

```text
                 SIP
Caller --------------------> Asterisk
   ^                           |
   |                           |
   |                           | SIP
   |                           v
   +========= RTP ========= Provider
```

The central server continued controlling the session even though the media followed a different route.

---

## 7. Why the Separation Matters

SIP signalling and RTP media have very different traffic characteristics.

SIP traffic is mainly associated with events such as:

- starting a call
- negotiating a call
- modifying a session
- ending a call

RTP traffic is continuous while voice media is active.

For a large number of simultaneous calls, the continuous media flow can therefore place substantially greater traffic demand on the server than the signalling traffic itself.

---

## 8. Simplified Call Establishment

A simplified signalling sequence can be represented as:

```text
Caller                Asterisk                Provider
  |                        |                       |
  |---- Call request ----->|                       |
  |                        |---- Call request ---->|
  |                        |                       |
  |<--- SIP signalling --->|<--- SIP signalling -->|
  |                        |                       |
  |================ RTP directly =================>|
  |<=============== RTP directly ==================|
  |                        |                       |
  |---- Call end --------->|                       |
  |                        |---- Call end -------->|
```

This diagram is explanatory.

It is intended to show the separation between signalling and media rather than reproduce an original packet trace from the historical system.

---

## 9. SDP and Media Negotiation

SIP commonly carries SDP information used to negotiate media parameters.

Relevant information can include:

- RTP destination address
- RTP port
- codec information
- media capabilities

For media bypass to succeed, the media endpoints need information that allows them to exchange RTP directly.

The basic objective was therefore:

```text
SIP:

Caller -> Asterisk -> Provider


RTP:

Caller <-------------> Provider
```

rather than:

```text
Caller -> Asterisk -> Provider
```

for both signalling and media.

---

## 10. Asterisk's Continuing Role

Even when RTP was bypassing the server, Asterisk continued to maintain the logical call.

This distinction is important.

The project was not:

```text
Asterisk bypass
```

It was:

```text
RTP media bypass
```

Asterisk still remained responsible for the call-control layer.

---

## 11. Media Offloading

The media optimisation can also be understood as media offloading.

Before:

```text
                           RTP
Caller ----------------> Asterisk
                           |
                           | RTP
                           v
                        Provider
```

After:

```text
Caller ================= RTP ================= Provider
```

The RTP workload was moved away from the central signalling system.

---

## 12. Central Network Traffic

When Asterisk relayed media, its network interfaces had to carry the media traffic entering and leaving the server.

For one call:

```text
Caller RTP
    |
    v
Asterisk
    |
    v
Provider
```

For many simultaneous calls:

```text
Call 1 RTP  \
Call 2 RTP   \
Call 3 RTP    \
...            ---> Asterisk ---> Provider side
Call 100+ RTP /
```

The traffic accumulated at the central server.

Media bypass reduced this central concentration of RTP traffic.

---

## 13. High-Concurrency Behaviour

The project was tested with more than 100 simultaneous calls.

At this scale, removing unnecessary RTP relay became particularly relevant.

Asterisk still had to manage the signalling and call state for those calls.

However, where media bypass was successful, it did not need to continuously forward the RTP payload for every eligible session.

---

## 14. Resource Relationship

The original architecture linked call concurrency directly with central RTP traffic.

Conceptually:

```text
More calls
    |
    v
More RTP streams
    |
    v
More traffic through Asterisk
    |
    v
Higher central-server workload
```

The optimised architecture changed this relationship:

```text
More calls
    |
    v
More RTP streams
    |
    v
Media primarily exchanged between endpoints
    |
    v
Reduced central RTP workload
```

Asterisk still processed increasing signalling activity as call volume increased.

---

## 15. CPU Effect

Central RTP forwarding creates ongoing packet-processing work.

By allowing media to bypass Asterisk where possible, the server no longer needed to handle the same continuous RTP path for those calls.

In the historical project environment, this contributed to lower overall central-server resource utilisation.

The exact CPU measurements from the historical tests are no longer available.

---

## 16. Memory Effect

Asterisk continued maintaining call state even after media bypass.

Therefore direct RTP did not eliminate all memory use associated with a call.

However, the project showed an overall reduction in central resource pressure when unnecessary media processing was removed.

No exact historical per-call memory measurements are retained.

---

## 17. Bandwidth Effect

The bandwidth optimisation concerned the bandwidth passing through the Asterisk server.

It did not eliminate the RTP traffic itself.

Before:

```text
Caller
   |
 RTP
   |
   v
Asterisk
   |
 RTP
   |
   v
Provider
```

After:

```text
Caller
   |
 RTP
   |
   +-----------------------> Provider
```

The same voice conversation still required media transport.

The difference was that the central Asterisk server was no longer required to carry the complete RTP stream.

---

## 18. Approximately 50% Overall Improvement

The project produced an historical observation of approximately:

**50% overall reduction in central-server resource and media-path load**

in the implementation environment.

The observed improvement related generally to:

- CPU utilisation
- memory utilisation
- server-side bandwidth utilisation

The figure should not be interpreted as exactly 50% for each individual metric.

The original detailed performance records are no longer available.

---

## 19. Network Reachability

Direct RTP requires the two media endpoints to communicate with one another.

Possible obstacles include:

- NAT
- private IP addresses
- firewall restrictions
- routing
- incorrect SDP information
- provider-side policy
- endpoint behaviour

If the two media endpoints cannot communicate directly, Asterisk or another media relay may need to remain in the path.

---

## 20. One-Way Audio Risk

Incorrect direct-media configuration can produce one-way audio.

For example:

```text
Caller -------- RTP --------> Provider
Caller <------- X ----------- Provider
```

or:

```text
Caller -------- X ----------> Provider
Caller <------- RTP --------- Provider
```

Both media directions therefore need valid network reachability.

The project architecture was tested for working bidirectional audio.

---

## 21. Codec Compatibility

Direct RTP is easier where the connected systems can negotiate compatible codecs.

If one side requires a codec that the other side cannot use, transcoding may be required.

Where Asterisk performs transcoding, it generally needs access to the media stream.

Therefore media bypass is most suitable where direct media negotiation can succeed without requiring central codec conversion.

The historical codec configuration for this project is not documented in this repository because it has not been established confidently from retained evidence.

---

## 22. DTMF and Other Media-Dependent Functions

Some telecommunications functions can depend on the media path or negotiated media behaviour.

Examples can include:

- certain DTMF modes
- recording
- conferencing
- announcements
- media manipulation
- monitoring
- transcoding

Where such functions require central media processing, Asterisk may need to remain in the RTP path.

This is why media bypass should be applied according to the call requirements rather than forced universally.

---

## 23. Call Termination

Even with direct RTP, Asterisk remained involved when the call was terminated.

The simplified relationship was:

```text
Caller
   |
   | SIP termination
   v
Asterisk
   |
   | SIP termination
   v
Provider
```

Once the SIP session ended, the corresponding RTP flow also stopped.

The signalling server therefore continued to manage the call lifecycle.

---

## 24. Failure and Fallback Considerations

A production media-bypass architecture must consider situations where direct RTP cannot be established.

Possible reasons include:

- NAT incompatibility
- firewall blocking
- unreachable media addresses
- SDP mismatch
- codec mismatch
- provider restrictions

In such cases, central media handling may still be required.

This project focused on enabling direct media where the network environment allowed it.

---

## 25. Simplified Before-and-After Flow

### Before

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

### After

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

Caller
  |
 RTP
  |
  +----------------------------> Provider
```

This is the main technical change documented by the project.

---

## 26. Engineering Objective

The objective was to preserve:

```text
Centralised signalling
```

while reducing:

```text
Centralised media processing
```

The result was an architecture in which the server concentrated on call-control functions while media could take a more direct path.

---

## 27. Scalability Effect

This architectural change becomes increasingly relevant as call volume grows.

With 100+ simultaneous calls, the difference between:

```text
100+ RTP sessions relayed through Asterisk
```

and:

```text
100+ RTP sessions primarily exchanged directly between endpoints
```

can materially change the central server's workload.

The exact performance difference depends on the implementation environment.

---

## 28. Historical Project Context

The work was carried out during my employment at:

**Tech News 365 IT Institute, Bangladesh**

where I worked as:

**Senior Lecturer & IT Research and Innovation Lead**

from:

**5 January 2015 to 30 December 2021**

This repository was created later to document the engineering work in a structured form.

---

## 29. Evidence Status

The original:

- SIP traces
- RTP captures
- Asterisk configuration
- server screenshots
- performance graphs
- historical network diagrams
- monitoring logs

are no longer available.

The flow diagrams in this document are therefore explanatory reconstructions.

They should not be represented as original diagrams or packet captures from the historical implementation.

---

## 30. Technical Summary

The media-flow design can be summarised as:

```text
CALL CONTROL

Extension / Caller
        |
       SIP
        |
        v
     Asterisk
        |
       SIP
        |
        v
 SIP Trunk Provider


VOICE MEDIA

Extension / Caller
        |
       RTP
        |
        +==========================>
                              SIP Trunk Provider
```

The central engineering principle was:

**keep Asterisk in the signalling path while removing unnecessary Asterisk involvement from the RTP path.**

---

## Author

**Mohammad Sorower Jahan**

**Role during the project:** Senior Lecturer & IT Research and Innovation Lead

Technical areas:

VoIP | SIP | SDP | RTP | Asterisk | Linux | Telecommunications Infrastructure | Media Architecture | Systems Optimisation
