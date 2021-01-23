---
title: "Quick Start"
description: "One page summary of how to start a new Doks project."
lead: "One page summary of how to start a new Doks project."
date: 2020-11-16T13:59:39+01:00
lastmod: 2020-11-16T13:59:39+01:00
draft: false
images: []
menu: 
  docs:
    parent: "prologue"
weight: 110
toc: true
---

## Requirements

{{< alert icon="👉" text="Go is required to build Truce." >}}

Make sure all dependencies have been installed:

- [CUE](https://cuelang.org/) >= 0.22.0
- [Go](https://golang.org/) >= 1.15.0
- [CMake](https://cmake.org/)

## Building Truce

Currently, Truce is only accessible by building from source.

We're going to clone the project, build it using Make and the test drive the locally built binary.

### Clone Truce

{{< btn-copy text="git clone https://github.com/GeorgeMac/truce.git truce" >}}

```bash
git clone https://github.com/GeorgeMac/truce.git truce
```

### Change directories

{{< btn-copy text="cd truce" >}}

```bash
cd truce
```

### Run make build

{{< btn-copy text="make build" >}}

```bash
make build
```

### Try out Truce

{{< btn-copy text="bin/truce" >}}

```bash
bin/truce
```

The truce cli binary should output some help text.
