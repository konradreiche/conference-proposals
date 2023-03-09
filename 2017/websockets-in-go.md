# It doesn't have to be REST: Websockets in Go

This talk was accepted for Golang UK Conference 2017.

## Elevator Pitch

HTTP REST is seen as irreplaceable technology when designing APIs yet many applications require real-time interactivity. There are different WebSocket and WebSocket emulation implementations in the Go ecosystem. This talk will give an overview of the current state of the art of WebSockets in Go.

## Description

WebSockets can be summarized as bi-directional and full-duplex communication channel over a single TCP connection. With that in mind it already tackles a few issues REST faces: HTTP is uni-directional, only the client can issue request and needs to wait for the response, multiple TCP connections are used and it is rather chatty. WebSockets offer a lean protocol and both sides can communicate with each other at any given time, independently.

Looking at benchmarks, WebSockets allow to communicate by a magnitude of 5-10 times faster than HTTP REST. The [RFC 6455](https://tools.ietf.org/html/rfc6455) defines the WebSocket protocol. Many Go libraries implement this standard. I want to give you an overview of existing implementations, how they compare to each other and whether WebSockets might be a reasonable choice for your application. More specifically, we will look into the implementations of each library.

* The standard library ([golang.org/x/net/websocket](https://github.com/golang/net/tree/master/websocket))
* The gorilla way ([github.com/gorilla/websocket](https://github.com/gorilla/websocket))
* SockJS, WebSocket emulation in Go ([github.com/igm/sockjs-go](https://github.com/gorilla/websocket))
* SockJS Protocol test and igm/sockjs-go does not pass all tests
* Why SockJS scales horizontally
* Example application (Go backend, React Native frontend)

WebSockets are not a REST replacement. One needs to be especially mindful about state management. The advantage REST over WebSocket is the fact that it is inherently stateless. With WebSockets this needs to be implemented as a layer on top. With Go being used as infrastructure and backend provider, WebSockets form a reasonable interface for reacting quick to connected clients and sending continuous updates.

This talk should haven given a reasonable insight into the implementation of those WebSockets which cannot only help to understand the protocol better but also to learn the design principles behind those.

## Notes

This talk could be a beginners talk since it gives a more generic overview of the technology WebSockets while looking at concrete implementations. The objective is to motivate more Gophers to try out WebSockets as a backend solution for Go. I have learned a lot about WebSocket libraries in Go while solving a concrete problem for one of my clients during consulting.
