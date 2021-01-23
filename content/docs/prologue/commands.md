---
title: "Commands"
description: "Truce CLI reference."
lead: "Truce CLI reference."
date: 2020-10-13T15:21:01+02:00
lastmod: 2020-10-13T15:21:01+02:00
draft: false
images: []
menu: 
  docs:
    parent: "prologue"
weight: 130
toc: true
---

## val[idate]

Validate a target truce specification:

{{< btn-copy text="truce validate specification.cue" >}}

```bash
truce validate specification.cue # substitute specification.cue for your truce file(s)
```

## gen[erate]

Run the Truce generator. Target outputs defined in the specification will be generated into the resolved target locations.

{{< btn-copy text="truce generate specification.cue" >}}

```bash
truce generate specification.cue # substitute specification.cue for your truce file(s)
```
