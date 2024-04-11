- [[Remote Procedure Call (RPC)]] framework[^microsoft-learn]
	- high-performance, open-source RPC framework [^grpcio]
- language-agnostic
	- used in systems with multiple languages (polyglot systems)
- `.proto` files
```protobuf
syntax = "proto3";

service Greeter {
  rpc SayHello (HelloRequest) returns (HelloReply);
}

message HelloRequest {
  string name = 1;
}

message HelloReply {
  string message = 1;
}	
```

### References
[^microsoft-learn]: https://learn.microsoft.com/en-us/aspnet/core/grpc/?view=aspnetcore-8.0
https://learn.microsoft.com/en-us/aspnet/core/tutorials/grpc/grpc-start?view=aspnetcore-8.0&tabs=visual-studio
[^grpcio]: https://grpc.io/