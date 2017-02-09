# Clog [![Build Status](https://travis-ci.org/go-clog/clog.svg?branch=master)](https://travis-ci.org/go-clog/clog)

![](https://avatars1.githubusercontent.com/u/25576866?v=3&s=200)

Clog is a channel-based logging package for Go.

This package supports multiple logger adapters across different levels of logging. It uses Go's native channel feature to provide goroutine-safe mechanism on large concurrency.

## Installation

To use a tagged revision:

	go get gopkg.in/clog.v1

To use with latest changes:

	go get github.com/go-clog/clog
    
Please apply `-u` flag to update in the future.

### Testing

If you want to test on your machine, please apply `-t` flag:

	go get -t gopkg.in/clog.v1

Please apply `-u` flag to update in the future.

## Getting Started

Clog currently has two builtin logger adapters: `console` and `file`. 

It is extremely easy to create one with all default settings. Generally, you would want to create new logger inside `init` or `main` function.

```go
...

import (
	"fmt"
	"os"

	log "gopkg.in/clog.v1"
)

func init() {
	err := log.New(log.CONSOLE, log.ConsoleConfig{})
	if err != nil {
		fmt.Printf("Fail to create new logger: %v\n", err)
		os.Exit(1)
	}

	log.Trace("Hello %s!", "Clog")
	// Output: Hello Clog!


	log.Info("Hello %s!", "Clog")
	log.Warn("Hello %s!", "Clog")
	...
}

...
```

The above code is equivalent to the follow settings:

```go
...
	err := log.New(log.CONSOLE, log.ConsoleConfig{
		Level:      log.TRACE, // Record all logs
		BufferSize: 0,         // 0 means logging synchronously
	})
...
```

In production, you may want to make log less verbose and asynchronous:

```go
...
	err := log.New(log.CONSOLE, log.ConsoleConfig{
		// Logs under INFO level (in this case TRACE) will be discarded
		Level:      log.Info, 
		// Number mainly depends on how many logs will be produced by program, 100 is good enough
		BufferSize: 100,      
	})
...
```

Console logger comes with color output, but for non-colorable destination, the color output will be disabled automatically.

### Error Location

When using `log.Error` and `log.Fatal` functions, the first argument allows you to indicate whether to print the code location or not. 

```go
...
	// 0 means disable printing code location
	log.Error(0, "So bad... %v", err)

	// To print appropriate code location mainly depends on how deep your call stack is, 
	// you need to try and verify
	log.Error(2, "So bad... %v", err)
	// Output: 2017/02/09 01:06:16 [ERROR] [...uban-builder/main.go:64 main()] ...
	log.Fatal(2, "Boom! %v", err)
	// Output: 2017/02/09 01:06:16 [FATAL] [...uban-builder/main.go:64 main()] ...
...
```

Calling `log.Fatal` will exit the program.

## Credits

- Avatar is a modified version based on [egonelbre/gophers' scientist](https://github.com/egonelbre/gophers/blob/master/vector/science/scientist.svg).

## License

This project is under Apache v2 License. See the [LICENSE](LICENSE) file for the full license text.