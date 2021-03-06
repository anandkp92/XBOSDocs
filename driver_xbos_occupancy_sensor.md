
## XBOS Occupancy Sensor

**Interface**: i.xbos.occupancy_sensor

**Description**: XBOS occupancy sensor

**PONUM**: 2.1.2.1

### Properties

| **Name** | **Type** | **Description** | **Units** | **Required** |
| :------- | :------- | :-------------- | :-------- | :----------- |
| occupancy | boolean | Current occupancy state | on/off | true |
| time | integer | nanoseconds since the Unix epoch | ns | false |


### Signals
- `info`:
    - `occupancy`
    - `time`
    


### Slots


### Interfacing in Go

```go
package main

import (
	"fmt"
	bw2 "gopkg.in/immesys/bw2bind.v5"
)

func main() {
	client := bw2.ConnectOrExit("")
	client.OverrideAutoChainTo(true)
	client.SetEntityFromEnvironOrExit()

	base_uri := "Occupancy Sensor uri goes here ending in i.xbos.occupancy_sensor"

	// subscribe
	type signal struct {
		Occupancy bool  `msgpack:"occupancy"`
		Time      int64 `msgpack:"time"`
	}
	c, err := client.Subscribe(&bw2.SubscribeParams{
		URI: base_uri + "/signal/info",
	})
	if err != nil {
		panic(err)
	}

	for msg := range c {
		var current_state signal
		po := msg.GetOnePODF("2.1.2.1/32")
		err := po.(bw2.MsgPackPayloadObject).ValueInto(&current_state)
		if err != nil {
			fmt.Println(err)
		} else {
			fmt.Println(current_state)
		}
	}
}
```
### Interfacing in Python

```python

import time
import msgpack

from bw2python.bwtypes import PayloadObject
from bw2python.client import Client

bw_client = Client()
bw_client.setEntityFromEnviron()
bw_client.overrideAutoChainTo(True)

def onMessage(bw_message):
  for po in bw_message.payload_objects:
    if po.type_dotted == (2,1,2,1):
      print msgpack.unpackb(po.content)

bw_client.subscribe("Occupancy Sensor uri ending in i.xbos.occupancy_sensor/signal/info", onMessage)

print "Subscribing. Ctrl-C to quit."
while True:
  time.sleep(10000)
```
