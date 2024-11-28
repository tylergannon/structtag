# structtag

[![](https://github.com/tylergannon/structtag/workflows/build/badge.svg)](https://github.com/tylergannon/structtag/actions)

`structtag` is a library for parsing struct tags in Go during static analysis. It is designed to provide a robust API for extracting metadata from struct fields in Go code. This fork focuses exclusively on **reading struct tags** and is no longer intended for modifying or rewriting them.

This project is a fork of [`fatih/structtag`](https://github.com/fatih/structtag), originally created by Fatih Arslan. The primary changes in this fork include a simplified API tailored for read-only access to struct tags, along with enhancements to better support static analysis use cases.

---

## Install

To install the package:

```bash
go get github.com/tylergannon/structtag
```

---

## Example Usage

The following example demonstrates how to parse and inspect struct tags using `structtag`:

```go
package main

import (
	"fmt"
	"reflect"
	"sort"

	"github.com/tylergannon/structtag"
)

func main() {
	type Example struct {
		Field string `json:"foo,omitempty" xml:"bar"`
	}

	// Get the struct tag from the field
	tag := reflect.TypeOf(Example{}).Field(0).Tag

	// Parse the tag using structtag
	tags, err := structtag.Parse(string(tag))
	if err != nil {
		panic(err)
	}

	// Iterate over all tags
	for _, t := range tags.Tags() {
		fmt.Printf("Key: %s, Options: %v\n", t.Key, t.Options)
	}

	// Access a specific tag by key
	jsonTag, err := tags.Get("json")
	if err != nil {
		panic(err)
	}
	fmt.Printf("Key: %s, Options: %v\n", jsonTag.Key, jsonTag.Options)

	// Print the full tag string
	fmt.Println(tags.String()) // Output: json:"foo,omitempty" xml:"bar"

	// Sort tags by key and print them
	sort.Sort(tags)
	fmt.Println(tags.String()) // Output: json:"foo,omitempty" xml:"bar"
}
```

---

## Key Features

- **Read-Only Access**: Extract and inspect struct tags without modifying them.
- **Simplified API**: A clean interface for retrieving tag keys and options.
- **Static Analysis**: Ideal for tools that analyze Go code, such as linters or code generators.

---

## API Overview

### Parsing Struct Tags
Use `Parse` to parse a struct field's tag into a `Tags` object:
```go
tags, err := structtag.Parse(`json:"foo,omitempty" xml:"bar"`)
if err != nil {
    panic(err)
}
```

### Accessing Tags
- **Retrieve all tags**:
  ```go
  allTags := tags.Tags()
  ```
- **Retrieve a specific tag by key**:
  ```go
  jsonTag, err := tags.Get("json")
  if err != nil {
      // Handle missing tag
  }
  fmt.Println(jsonTag.Key, jsonTag.Options)
  ```

### Inspecting Tags
- **Key**: The key of the tag (e.g., `json` or `xml`).
- **Options**: A slice of strings representing the options in the tag (e.g., `["foo", "omitempty"]` for `json:"foo,omitempty"`).

---

## Acknowledgments

This project is based on [`fatih/structtag`](https://github.com/fatih/structtag), created by Fatih Arslan. While the API and functionality have been modified significantly, the foundation of this library owes much to the original implementation.

