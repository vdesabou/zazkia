# Zazkia - Help

### How does it work ?
In order to apply misbehavior, **zazkia** must be used as a proxy between a client and service.
It will accept tcp connections from a client and for each new one, will create a connection to the target service.


![me](/static/img/zazkia-as-proxy.png)

### Routes
By specifying routes, you can tell zazkia on what ports to listen and what target to connect to (hostname and port).

Minimal **zazkia-routes.json** example

	[
	    {
	        "label": "oracle",
	        "service-hostname": "some.host.name",
	        "service-port": 1521,
	        "listen-port": 49997
	    }
	]

With this route definition, your application should use "localhost:49997" instead of "some.host.name:1521" in the connection specification.
Your application (the client) will setup a tcp connection with zazkia which will setup another tcp connection to oracle (the service).

#### Initial transport behavior
The transport part of a route configuration can be used to setup the initial behavior of a new connection pair (called link).
Using a REST api, the transport behavior can be changed on a per-link basis.

Full **zazkia-routes.json** example

	[
	    {
	        "label": "postgresql",
	        "service-hostname": "some.other.host.name",
	        "service-port": 5432,
	        "listen-port": 49998,
	        "transport": {
				"accept-connections": true,
				"throttle-service-response": 1000,
				"delay-service-response": 100,
				"service-response-corrupt-method": "randomize",
				"sending-to-client": true,
				"receiving-from-client": true,
				"sending-to-service": true,
				"receiving-from-service": true,
				"verbose": true
	        }
	    }
	]

| transport property | comment | default, effective values |
|-----------|---------|--------|
| accept-connections | whether connections from the client are accepted | **true**, false |
| throttle-service-response | bytes per second | **0**, positive integer |
| delay-service-response | milliseconds delay | **0**, positive integer |
| service-response-corrupt-method | how the bytes are mangled | **empty**, randomize |
| sending-to-client | whether a response from the service is sent back to the client | **true**, false |
| receiving-from-client | whether a request from the client is read | **true**, false |
| sending-to-service | whether a request from the client is sent to the service | **true**, false |
| receiving-from-service | whether a response from the service is read | **true**, false |