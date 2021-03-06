## Topo API

### Watching Topology Changes
The µONOS RIC SDK provides a simple-to-use mechanism for application to initially scan, and then continuously monitor
the topology for changes.

Application can do so by writing code similar to this:

```cgo
import (
    topoapi "github.com/onosproject/onos-api/go/onos/topo"
    "github.com/onosproject/onos-ric-sdk-go/pkg/topo"
)

    ...
    // Create a client entity that will be used to interact with the topology service.
    client, err := topo.NewClient(&topo.ServiceConfig{...})

    // Allocate channel on which topology events will be received
    ch := make(chan topoapi.Event)
    
    // Start watching for topology changes that match the following filters:
    //  - entities only (not relations)
    //  - entities of kind "eNB" only
    //  - existing entities or entities being added
    err = client.Watch(context.Background(), ch,
            WithTypeFilter(topoapi.Object_ENTITY), WithKindFilter("eNB"),
            WithEventFilter(topoapi.EventType_NONE, topoapi.EventType_ADDED))
    ...
    
    // Read from the channel; likely in a go routine
    for event := range ch {
        // Process each event
        ...
    }
    
    ...
    
    // Close the channel when stopping
    close (ch)
		
```

To narrow down the type of events to be received, the application can specify the following filter options to the `Watch` call:

* `WithTypeFilter` - list of object types, `ENTITY`, `RELATION`, `KIND`
* `WithKindFilter` - list of specific entity or relationship kinds, e.g. `gNB`, `switch`, `controller`
* `WithEventFilter` - list of event types `NONE` (pre-existing item), or newly `ADDED`, `UPDATED`, `REMOVED`
