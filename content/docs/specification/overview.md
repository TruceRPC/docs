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
      string: {        # status code to represent error on the wire.
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

Argument is an ordered list of the arguments to the function.
Each argument has a name, which is referenced in the function transport definitions for mapping.
They also have a [type](#Types).

```cue
arguments: [
    {
      name: string
      type: <type>
    }
  ]
```

#### function/return

The return object of a truce function is also an argument type. Functions in Truce have a single return argument (and an implicit return `error`).
The return argument has a name, though currently only marshalling that result to the response body is supported.
So no mapping capabilities exists for the transport yet. It is just implied this will be marshalled to the response body.

```cue
return: {
    name: string
    type: <type>
  }
```

#### function/transports

Currently, Truce support `http` as the only transport protocol. Though it leaves space incase others become desireable.
So the first key beneath `transports` is `http` and it is optional. Incase you want to omit the function from any target generated output.

The `http` object describes how the function becomes accessible on the wire.

It allows you to express:
- The path on which the function matches.
- The HTTPmethod (i.e. the verb 'GET', 'POST' etc).
- The argument mappings from the request onto the parent function call.

```cue
transports:
  http?:
    path:   string # e.g. /resource/{id}
    method: string # e.g. GET or POST
    arguments:
      string: { # argument name to be mapped to (see [function arguments](#function/arguments))
          from: "body"  |
                "path"  |
                "query" |
                <empty> # used to signify which part of the request the argument is being mapped from.
          var:   string # used for from == ("path" or "query") to identify the parameter in the path or query from which to map.
          value: string # used when from is empty to pass a constant value
        }
```

### Types

Truce has support for:
- Primitive types:
  - string
  - bool
  - int
  - float64
  - byte
- Slice of types (e.g. []int or []Model)
- Maps of types (e.g. map[string]Model)
- Custom Struct definition (defined under `types:`).
- Pointers to Structs.

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
