# Project Evidence and Engineering Contribution

## VoIP Media Bypass and RTP Offloading Project

**Author:** Mohammad Sorower Jahan  
**Organisation:** Tech News 365 IT Institute, Bangladesh  
**Role:** Senior Lecturer & IT Research and Innovation Lead  
**Employment period:** 5 January 2015 – 30 December 2021  
**Independent confirmer:** Joy Prakash Ghose, CEO, Tech News 365 IT Institute  
**Organisation website:** www.technews365.net

---

## 1. Purpose of This Document

This document records the background, engineering contribution and currently available supporting evidence for the VoIP media-bypass project described in this repository.

The project involved redesigning the media path of an Asterisk-based VoIP environment so that Asterisk could remain responsible for SIP signalling and call control while RTP media could flow directly between the caller side and the SIP trunk provider where the network conditions permitted it.

The work formed part of my research, engineering and operational responsibilities at Tech News 365 IT Institute.

---

## 2. Employment Context

I worked at Tech News 365 IT Institute, Bangladesh, from:

**5 January 2015 to 30 December 2021**

My role was:

**Senior Lecturer & IT Research and Innovation Lead**

My work included practical telecommunications engineering, network infrastructure, VoIP systems, Linux-based infrastructure and technical research.

The media-bypass work documented in this repository was carried out during this employment period.

---

## 3. Project Context

The project was not only an isolated theoretical experiment.

The architecture was used as part of ongoing Tech News 365 operations.

The engineering work focused on improving the efficiency of an Asterisk-based VoIP environment where the central server was handling both:

- SIP signalling
- RTP media relay

The original arrangement caused the central Asterisk server to remain involved in the media stream for the duration of active calls.

The project investigated whether Asterisk could remain responsible for call signalling and routing while unnecessary RTP processing was moved away from the central server.

---

## 4. Original Architecture

Before optimisation, the logical path was:

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

Asterisk therefore handled both call control and continuous media traffic.

---

## 5. Optimised Architecture

The redesigned architecture separated the signalling and media paths.

### SIP signalling

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

### RTP media

```text
Extension / Caller
        |
       RTP
        |
        +---------------------------->
                              SIP Trunk Provider
```

Asterisk remained responsible for establishing and controlling the SIP session.

The RTP stream could bypass Asterisk where direct media was technically possible.

---

## 6. My Engineering Contribution

I personally carried out the principal R&D and implementation work for the project.

My contribution included:

- analysing the existing Asterisk media path;
- identifying unnecessary central RTP processing;
- separating SIP signalling requirements from RTP media requirements;
- designing the media-bypass architecture;
- implementing the Asterisk-side changes;
- testing direct RTP behaviour;
- validating SIP call establishment;
- validating bidirectional audio;
- testing the architecture under high call concurrency;
- examining the effect on central server resources;
- refining the implementation as part of the operational environment.

The architecture was therefore based on my own technical research and implementation work.

---

## 7. Collaboration

I worked on the project with:

**Joy Prakash Ghose**

who was the CEO of Tech News 365 IT Institute.

The engineering design and implementation were my own R&D work, while Joy Prakash Ghose worked with me during the project and is able to confirm the work independently.

This provides an independent organisational source who had direct knowledge of the project.

---

## 8. Operational Use

The project was used as part of ongoing Tech News 365 operations rather than remaining only as a laboratory demonstration.

This is important because the architecture was evaluated in a practical operating environment.

The work therefore involved both:

```text
Research and engineering
```

and:

```text
Operational implementation
```

The objective was to reduce unnecessary media processing while preserving normal VoIP call operation.

---

## 9. High-Concurrency Testing

The architecture was tested with:

**more than 100 simultaneous calls**

The exact maximum call count is no longer available.

For that reason, the repository records the historical testing scale as:

```text
100+ simultaneous calls
```

rather than claiming a more precise value.

---

## 10. Historical Performance Observation

The project produced an approximate historical observation of:

**around 50% overall reduction in central-server resource and media-path load**

after the media-bypass architecture was implemented.

The improvement related broadly to areas including:

- CPU utilisation
- memory utilisation
- server-side bandwidth utilisation

The approximately 50% figure is an overall project observation.

It is not being presented as proof that each individual metric was reduced by exactly 50%.

---

## 11. Independent Confirmation

The project and my involvement can be independently confirmed by:

**Joy Prakash Ghose**  
**CEO**  
**Tech News 365 IT Institute, Bangladesh**  
**Website:** www.technews365.net

He worked with me during the project and had direct knowledge of the media-bypass engineering work and its operational implementation.

---

## 12. Employment Evidence

I have an employment letter from Tech News 365 IT Institute confirming my employment with the organisation.

The letter supports the employment relationship under which the engineering work was carried out.

The letter is maintained separately from this public technical repository.

It is not reproduced here because this repository is intended primarily to document the technical architecture and engineering work.

---

## 13. Recommendation Evidence

I also have a recommendation letter from Tech News 365 IT Institute.

The recommendation specifically refers to the VoIP media-architecture optimisation work, including:

- media bypass / RTP offloading;
- reductions in CPU, RAM and bandwidth utilisation; and
- the approximately 50% resource improvement observed in the project environment.

This provides documentary support for both my engineering contribution and the reported operational result.

The recommendation letter is maintained separately from this public repository.

---

## 14. Relationship Between Repository and External Evidence

The GitHub repository itself was created after the original engineering work.

It should therefore not be treated as contemporaneous evidence of when the project took place.

Instead, the repository serves as a technical reconstruction of:

- the problem;
- the architecture;
- the engineering reasoning;
- the implementation approach;
- the media flow;
- the scalability considerations;
- the historical result.

The employment and recommendation documentation provide separate supporting evidence relating to the original work.

---

## 15. Original Technical Records

The original technical records from the project are no longer available.

This includes the original:

- Asterisk configuration files;
- server configuration;
- SIP traces;
- RTP packet captures;
- screenshots;
- performance graphs;
- monitoring output;
- historical network diagrams;
- testing logs.

The absence of these records is stated explicitly so that retrospective documentation is not confused with original project evidence.

---

## 16. Retrospective Documentation

The diagrams and technical explanations in this repository were created later to document the engineering work in a structured form.

For example:

```text
Caller
   |
  SIP
   v
Asterisk
   |
  SIP
   v
Provider
```

and:

```text
Caller <============== RTP ==============> Provider
```

are explanatory reconstructions.

They are not presented as diagrams created during the original project period.

---

## 17. Evidence Categories

The project evidence can be separated into three categories.

### A. Current documentary evidence

Available:

- Tech News 365 employment letter;
- Tech News 365 recommendation letter.

### B. Independent confirmation

Available through:

- Joy Prakash Ghose, CEO of Tech News 365 IT Institute.

### C. Historical technical records

No longer available:

- original server configuration;
- original Asterisk files;
- packet captures;
- historical screenshots;
- monitoring logs;
- performance graphs.

This distinction is maintained throughout the repository.

---

## 18. What the Employment Letter Supports

The employment letter supports the fact that I worked at Tech News 365 IT Institute during the relevant employment period.

It provides organisational evidence of my role and relationship with the institution.

The technical details of the project are supported more specifically by the recommendation letter and independent confirmation.

---

## 19. What the Recommendation Letter Supports

The recommendation letter provides more project-specific confirmation.

It refers to the engineering work involving:

- VoIP infrastructure;
- media-path optimisation;
- RTP offloading / media bypass;
- infrastructure resource reduction.

It also refers to the approximate improvement observed in CPU, RAM and bandwidth utilisation.

This makes the recommendation relevant to the technical project documented in this repository.

---

## 20. What Joy Prakash Ghose Can Confirm

Joy Prakash Ghose can independently confirm:

- my role at Tech News 365 IT Institute;
- my technical responsibilities;
- my involvement in VoIP and infrastructure R&D;
- the media-bypass project;
- the use of Asterisk;
- the engineering objective of reducing unnecessary RTP processing;
- my design and implementation contribution;
- the use of the architecture within Tech News 365 operations;
- the resource-efficiency improvement associated with the project.

This independent confirmation comes from someone who worked with me during the relevant period.

---

## 21. Engineering Ownership

The technical design was primarily my own R&D and implementation.

My work included moving the architecture from:

```text
SIP + RTP through Asterisk
```

to:

```text
SIP through Asterisk
RTP directly between media endpoints
```

where the environment supported it.

The objective was to retain Asterisk's call-control functionality without requiring it to process unnecessary media traffic continuously.

---

## 22. Practical Engineering Problem

The project addressed a practical infrastructure problem.

When Asterisk remained in the RTP path:

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

every active call placed additional continuous network and packet-processing load on the central server.

At more than 100 simultaneous calls, this became increasingly relevant.

The media-bypass architecture reduced this central workload.

---

## 23. Engineering Result

The principal engineering result was not the removal of Asterisk.

Asterisk still handled:

- SIP signalling;
- call routing;
- dialplan processing;
- call state;
- call termination.

The optimisation was specifically concerned with removing unnecessary RTP relay.

The resulting architecture was:

```text
SIGNALLING

Caller -> Asterisk -> SIP Trunk Provider


MEDIA

Caller <---------- RTP ----------> SIP Trunk Provider
```

---

## 24. Interpretation of the Approximately 50% Figure

The approximately 50% figure must be understood in the context of the original implementation.

It represents an overall observed improvement in the project environment.

It should not be interpreted as a universal statement that:

```text
all Asterisk servers will show 50% CPU reduction
```

or:

```text
all Asterisk servers will show 50% RAM reduction
```

or:

```text
all Asterisk servers will show 50% bandwidth reduction
```

The result depended on the specific call load, configuration and infrastructure involved in the original project.

---

## 25. Why No Exact Benchmark Table Is Included

A precise benchmark table would normally require retained information such as:

- before and after measurements;
- test duration;
- server specification;
- monitoring interval;
- call-generation data;
- codec configuration;
- exact concurrency;
- monitoring screenshots.

Those original records are no longer available.

Creating exact numerical tables now would therefore risk introducing unsupported precision.

For that reason, the repository retains only the historical approximately 50% overall observation.

---

## 26. Evidence Integrity

The documentation follows a simple evidence principle:

**Do not present reconstructed material as original historical evidence.**

Where information is based on retrospective technical reconstruction, that is stated clearly.

Where external supporting evidence exists, such as employment and recommendation documentation, it is identified separately.

Where the original records are unavailable, the repository states that limitation rather than attempting to recreate them as historical artefacts.

---

## 27. Repository Documents

The technical reconstruction is divided across the following files:

```text
README.md

docs/
├── architecture.md
├── media-flow.md
├── resource-analysis.md
├── scalability-testing.md
└── project-evidence.md
```

Each document addresses a different part of the project.

---

## 28. Architecture Evidence

The architecture documentation explains the design relationship between:

```text
Caller / Extension
       |
       v
Asterisk
       |
       v
SIP Trunk Provider
```

and the direct media relationship:

```text
Caller <============== RTP ==============> Provider
```

These diagrams are explanatory reconstructions.

---

## 29. Resource Evidence

The resource documentation explains why removing RTP from the central server could reduce:

- continuous packet processing;
- central network utilisation;
- media-related workload;
- infrastructure pressure.

The approximate 50% overall observation is recorded with appropriate limitations.

---

## 30. Scalability Evidence

The scalability documentation records the historical testing scale as:

**100+ simultaneous calls**

The exact maximum is not stated because the original testing records are unavailable.

This avoids unsupported precision.

---

## 31. Current Evidence Summary

The currently available supporting position is:

| Evidence | Status |
|---|---|
| Employment relationship with Tech News 365 | Supported by employment letter |
| Project-specific recommendation | Available |
| Media-bypass work mentioned in recommendation | Yes |
| CPU/RAM/bandwidth improvement mentioned | Yes |
| Approximately 50% improvement mentioned | Yes |
| Independent organisational confirmer | Joy Prakash Ghose |
| Original Asterisk configuration | Not retained |
| Original monitoring screenshots | Not retained |
| Original packet captures | Not retained |
| Original performance logs | Not retained |
| Current GitHub technical reconstruction | Available |

---

## 32. Independent and Documentary Support

The strongest surviving support for this historical project therefore consists of two different forms of evidence:

```text
Documentary support
        +
Independent confirmation
```

Documentary support comes from the Tech News 365 employment and recommendation documentation.

Independent confirmation is available from Joy Prakash Ghose, who worked with me during the relevant project period.

---

## 33. Historical Context

The project took place during my employment between:

**5 January 2015 and 30 December 2021**

at:

**Tech News 365 IT Institute, Bangladesh**

My position was:

**Senior Lecturer & IT Research and Innovation Lead**

The exact project date within that employment period is not stated more narrowly in this repository because no surviving historical record has been identified that establishes a more precise date.

---

## 34. Evidence Boundary

The following should be treated as retrospective technical documentation:

- present GitHub diagrams;
- present Markdown documents;
- recreated call-flow illustrations;
- present architectural explanations.

The following constitute separate supporting evidence:

- employment letter;
- recommendation letter;
- independent confirmation from Joy Prakash Ghose.

The two categories should not be confused.

---

## 35. Project Summary

The historical engineering work can be summarised as:

```text
ORIGINAL

Extension
   |
 SIP + RTP
   |
   v
Asterisk
   |
 SIP + RTP
   |
   v
Provider


OPTIMISED

SIP:
Extension -> Asterisk -> Provider

RTP:
Extension <------------> Provider
```

The architecture was tested with more than 100 simultaneous calls and was associated with an approximately 50% overall reduction in central-server resource and media-path load in the original environment.

The work was principally my own R&D and implementation and was carried out with the involvement of Joy Prakash Ghose as part of ongoing Tech News 365 operations.

---

## Author

**Mohammad Sorower Jahan**

**Role during the project:** Senior Lecturer & IT Research and Innovation Lead  
**Organisation:** Tech News 365 IT Institute, Bangladesh

Technical areas:

VoIP | SIP | SDP | RTP | Asterisk | Linux | Telecommunications Infrastructure | Media Offloading | Systems Optimisation
