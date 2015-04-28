## Golang logging library

[![godoc](http://img.shields.io/badge/godoc-reference-blue.svg?style=flat)](https://godoc.org/github.com/op/go-logging) [![build](https://img.shields.io/travis/op/go-logging.svg?style=flat)](https://travis-ci.org/op/go-logging)

Package logging implements a logging infrastructure for Go. Its output format
is customizable and supports different logging backends like syslog, file and
memory. Multiple backends can be utilized with different log levels per backend
and logger.

## Example

Let's have a look at an [example](examples/example.go) which demonstrates most
of the features found in this library.

[![Example Output](examples/example.png)](examples/example.go)

```go
package main

import (
	"os"

	"github.com/op/go-logging"
)

var log = logging.MustGetLogger("example")

// Example format string. Everything except the message has a custom color
// which is dependent on the log level. Many fields have a custom output
// formatting too, eg. the time returns the hour down to the milli second.
var format = logging.MustStringFormatter(
	"%{color}%{time:15:04:05.000} %{shortfunc} ▶ %{level:.4s} %{id:03x}%{color:reset} %{message}",
)

// Password is just an example type implementing the Redactor interface. Any
// time this is logged, the Redacted() function will be called.
type Password string

func (p Password) Redacted() interface{} {
    return logging.Redact(string(p))
}

func main() {
    /* logger initiailization */
    backend, err := logging.NewSyslogBackendPriority("langley_proxy", syslog.LOG_LOCAL3)
    if err != nil {
        fmt.Printf("logging init error=[%s]", err.Error())
        os.Exit(1)
    }
    format := logging.MustStringFormatter("%{color}[%{module}.%{shortfunc}][%{level:.4s}]%{color:reset}%{message}")
    logging.SetBackend(logging.NewBackendFormatter(backend, format))
    /* end logger initiailization */

    log.Debug("debug %s", Password("secret"))
    log.Info("info")
    log.Notice("notice")
    log.Warning("warning")
    log.Error("err")
    log.Critical("crit")
}
```

## Installing

### Using *go get*

$ go get github.com/op/go-logging

After this command *go-logging* is ready to use. Its source will be in:

$GOROOT/src/pkg/github.com/op/go-logging

You can use `go get -u` to update the package.

## Documentation

For docs, see http://godoc.org/github.com/op/go-logging or run:

$ godoc github.com/op/go-logging

## Additional resources

* [wslog](https://godoc.org/github.com/cryptix/go/logging/wslog) -- exposes log messages through a WebSocket.
