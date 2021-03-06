## Go-i3barjson

Go-i3barjson is a [Go](https://golang.org/) library that implements the [i3bar JSON input protocol](https://i3wm.org/docs/i3bar-protocol.html).

### Get

Go-i3barjson requires Go version 1.7+.

Fetch and build Go-i3barjson:

```
go get github.com/davidscholberg/go-i3barjson
```

### Usage

Here's a simple example for using Go-i3barjson:

```go
package main

import (
	"fmt"
	"github.com/davidscholberg/go-i3barjson"
	"os"
	"time"
)

func main() {
	h := i3barjson.Header{Version: 1}
	err := i3barjson.Init(os.Stdout, nil, h, false)
	if err != nil {
		fmt.Fprintf(os.Stderr, "%s", err)
		return
	}

	countBlock := i3barjson.Block{}
	timeBlock := i3barjson.Block{}
	status := i3barjson.StatusLine{&countBlock, &timeBlock}

	for i := 0; i < 10; i++ {
		countBlock.FullText = fmt.Sprintf("%d", i)
		timeBlock.FullText = time.Now().Format("2006-01-02 15:04:05")
		err = i3barjson.Update(status)
		if err != nil {
			fmt.Fprintf(os.Stderr, "%s", err)
			break
		}
		time.Sleep(time.Second)
	}
}
```

### Caveats

* The `Update` function is not thread-safe, so it must always be called from the same thread.
* The [i3bar JSON protocol spec](https://i3wm.org/docs/i3bar-protocol.html) allows the `min_width` field to be either a string or an int. It's not easy to parse a field that can have two different types in Go's JSON library, so Go-i3barjson only supports a single type for `min_width`: the string type. The reason the string type was chosen over the int type is that `min_width`'s string type (which specifies character-width) seems more generally useful than its int type (which specifies pixel-width).

### TODO

* Implement stdin handling to read mouse events from i3bar.
