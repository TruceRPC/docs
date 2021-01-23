---
title: "Overview"
description: "Overview of the Truce specification language."
lead: "Overview of the Truce specification language."
date: 2020-10-06T08:48:57+00:00
lastmod: 2020-10-06T08:48:57+00:00
draft: false
images: []
menu:
  docs:
    parent: "specification"
weight: 100
toc: true
---

## Root Document

As mentioned the Truce framework consumes definition defined in [CUE](https://cuelang.org/).
It is recommended that users become aquainted with the language before continuing.
Though it is not strictly necessary, if you just want to get started.
The [example](https://github.com/georgemac/truce/tree/master/example) directory aims to illustrate the capabilities of truce through CUE templating.
It may also be a good starting place to get familiar.

> Checkout [truce.cue](https://github.com/georgemac/truce/blob/master/truce.cue) for the latest truce CUE specification.

Truce currently supports two top-level declarations:

```cue
outputs: ... # Target output definitions.
specifications: ... # Truce RPC specifications.
```

## Outputs

The outputs declarations define which target specification should be generated into definitions for a particular platform.

It is structure like so:

```cue
outputs:
  <name>:           # e.g. "userService"
    <version>:      # e.g. "3"
      <outputType>: # e.g. go or openapi
```

Truce currently supports two sets of target generators:

1. Go (types, client and servers).
2. OpenAPI (swagger definition).

### Go

The Go output object expresses where to generate the struct definitions (types), http client and http server Go definitions separately.
The type name `type` of each of the client and server can be overriden. Along with the package `pkg` in which to generate for all targets.

Example:

```cue
outputs:
  example:
    "1":
      go:
        types: {
          path: "example/types.go" # Destination of generated struct definitions.
          pkg: "example"           # Package in which to generate struct definitions.
        }
        server: {
          path: "example/server.go" # Destination of generated server.
          type: "Server"            # Target server type name to generate.
          pkg:  "example"           # Package in which to generate http server.
        }
        client: {
          path: "example/client.go" # Destination of generated client.
          type: "Client"            # Target client type name to generate.
          pkg:  "example"           # Package in which to generate http client.
        }
```

### OpenAPI

The OpenAPI output object expresses a location in which to output a target OpenAPI3 (swagger) json file.

Example:

```cue
outputs:
  example:
    "1":
      openapi:
        version: 3 # Currently only 3 is supported.
        path:    "example/swagger.json" # Destination of output OpenAPI 3 specification.
```


## Specifications

The heart of Truce is nested beneath the `specifications` field. This is where RPC definitions are housed beneath a `name` and a `version`:

```cue
specifications:
  <name>:
    <version>: { ... } # Specification object
```

### Specification Object

The specification object consists three top-level fields:

```cue
transports: # Configuration pertaining to a particular transport (i.e. `http`)
functions:  # The function definitions of the Truce service being defined.
types:      # The type definitions of the Truce service being defined.
```

The product of these three definition objects drives the generators to output scaffolding, API definitions and frameworks.

### Transports

```cue
transports:
  <type>: # currently only "http" is supported as a transport target.
```

#### HTTP Transport

```cue
transports:
  http:
    versions: [string] # array of http version strings e.g. ["1.0", "1.1", "2.0"]
    prefix: string     # prefix for all generated endpoints e.g. "/api/v1".
    errors: {
      <status>: {      # status code to defined type mapping for.
        type: string   # reference to an error defined in "types" section.
      }
    }
```

### Functions

```cue
functions:
  string:          # the name of the function defined as a string.
    arguments:  [] # see function/arguments below.
    return:     {} # see function/return below.
    transports: {} # see function/transports below.
```

#### function/arguments
TODO(georgemac)
#### function/return
TODO(georgemac)
#### function/transports
TODO(georgemac)

### Types

```cue
types:
  string:                     # a string defining the types name.
    type: *"struct" | "error" # a type of either "struct" or "error" (default "struct").
    fields: {
      string: {               # a string defining the fields name.
        type: string          # a string definined the fields type.
      }
    }
```
