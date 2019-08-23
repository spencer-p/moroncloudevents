Tired of a complex API surface? Moron CloudEvents makes it easy to set up a
CloudEvent receiver side-by-side with HTTP handlers. Check out this simple
example:

```go
package main

import (
	"context"
	"net/http"
	"net/url"

	cloudevents "github.com/cloudevents/sdk-go"
	moron "github.com/spencer-p/moroncloudevents"
)

func index(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("Hello from HTTP!"))
}

func receive(ctx context.Context, event cloudevents.Event, r *cloudevents.EventResponse) error {
	log.Println("Received CloudEvent with data: ", event.DataBytes())
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
