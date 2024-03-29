# Moron CloudEvents

[![GoDoc](https://godoc.org/github.com/spencer-p/moroncloudevents?status.svg)](https://godoc.org/github.com/spencer-p/moroncloudevents)

Tired of a complex API surface? Moron CloudEvents makes it easy to set up a
CloudEvent receiver side-by-side with HTTP handlers. Check out this simple
example:

```go
package main

import (
	"context"
	"log"
	"net/http"

	cloudevents "github.com/cloudevents/sdk-go"
	moron "github.com/spencer-p/moroncloudevents"
)

func index(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("Hello from HTTP!"))
}

func receive(ctx context.Context, event cloudevents.Event, r *cloudevents.EventResponse) error {
	databytes, _ := event.DataBytes()
	log.Printf("Received CloudEvent with data: %q\n", databytes)
	return nil
}

func main() {
	svr, err := moron.NewServer(&moron.ServerConfig{
		Port:                  "8080",
		CloudEventReceivePath: "/apis/receive",
	})
	if err != nil {
		log.Fatal("Could not create server: ", err)
	}

	svr.HandleCloudEvents(receive)

	svr.HandleFunc("/", index)

	log.Fatal(svr.ListenAndServe())
}
```
