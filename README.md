== setup ==
- install linkerd (tested with version 1.4.0)
- install consul (tested with version 1.1.0)
- install python deps (tested with python 3.6.3):
  
  `pip install -r requirements.txt`

== testing ==
start linkerd
`linkerd ./linkerd.yml`

make a thrift request via linkerd
`python client.py`

you will get the following error:
```
thrift.Thrift.TApplicationException: Internal error processing ping: 'Failure(Connection refused: localhost/127.0.0.1:8500 at remote address: localhost/127.0.0.1:8500. Remote Info: Not Available, flags=0x100000018) with RemoteInfo -> Upstream Address: Not Available, Upstream id: Not Available, Downstream Address: localhost/127.0.0.1:8500, Downstream label: client, Trace Id: 73d307dff0f3cce8.73d307dff0f3cce8<:73d307dff0f3cce8 with Service -> 0.0.0.0/5000'
```

start consul
`consul agent -dev`

make another thrift request via linkerd
`python client.py`

you will get the following error again, if linkerd had now connected to consul you would get a no brokers exception instead:
```
thrift.Thrift.TApplicationException: Internal error processing ping: 'Failure(Connection refused: localhost/127.0.0.1:8500 at remote address: localhost/127.0.0.1:8500. Remote Info: Not Available, flags=0x100000018) with RemoteInfo -> Upstream Address: Not Available, Upstream id: Not Available, Downstream Address: localhost/127.0.0.1:8500, Downstream label: client, Trace Id: 73d307dff0f3cce8.73d307dff0f3cce8<:73d307dff0f3cce8 with Service -> 0.0.0.0/5000'
```

restart linkerd
`linkerd ./linkerd.yml`

make another thrift request via linkerd
`python client.py`

you will now get the no brokers exception:
```
thrift.Thrift.TApplicationException: Internal error processing ping: 'com.twitter.finagle.NoBrokersAvailableException: No hosts are available for /svc/ping, Dtab.base=[/svc=>/#/fallback/.local/thrift_server;/svc=>/#/default/.local/thrift_server], Dtab.local=[]. Remote Info: Not Available'
```




