# Net Core gRPC Troubleshooting of Errors, Exceptions, gRPC status codes and common pitfalls

# This repository

This repository describe common problems when working with gRPC mainly using .net core.

Under normal conditions, grpc-status is returned as an HTTP/2 trailer from the backend, but for some error conditions it might be returned as an HTTP/2 header. More in depth knowledge about internals [here](./README_DETAILS.md)

The following section describes how gRPC clients respond to errors.

# Errors

## Status(StatusCode=Unavailable, Detail="failed to connect to all addresses")

__gRPC Status Code:__ Unavailable

### Alternative

__Details:__ 
- The DNS resolution went well or was skipped,
- Client has resolved numeric IP address,
- Client can not connect using IP, connection was not established,
- Possibly server is not hosted under resolved IP address,

__Suggested solutions:__ 
- verify if the server was deployed for IP resolved by client,

__Verified for:__ gRPC for C# client

### Alternative

__Details:__ 
- The DNS resolution went well or was skipped,
- Client has resolved numeric IP address,
- Client can not connect using IP, connection was not established,
- The cause may be a bad port configuration,

__Suggested solutions:__ 
- verify the server configuration, in particular exposing/listening to ports, 
- verify the port used by the client,

__Verified for:__ gRPC for C# client

## Status(StatusCode=Unimplemented, Detail="")

### Alternative

__gRPC Status Code:__ Unimplemented

__Details:__ Client has successfully connected to server however server had no service defined

__Suggested solutions:__ 
- ensure that your gRPC service definition is configured in server

__Verified for:__ gRPC for C# client and gRPC for dotnet client

### Alternative

__gRPC Status Code:__ Unimplemented

__Details:__ Client has successfully connected to server, service was found but method is not implemented

__Suggested solutions:__ 
- override/implement method in gRPC service 

__Verified for:__ gRPC for C# client and gRPC for dotnet client

## Status(StatusCode=Unimplemented, Detail="Bad gRPC response. HTTP status code: 404")

__gRPC Status Code:__ Unimplemented

__Details:__ Client has successfully connected to server however asp.net core server had no service defined

__Suggested solutions:__ 
- ensure that your gRPC service definition is configured in server 
- when using asp.net core verify Startup.cs (`services.AddGrpc();` and `endpoints.MapGrpcService<ServiceName>();`)

__Verified for:__ gRPC for dotnet client

## Status(StatusCode=Cancelled, Detail="Received http2 header with status: 404")

__gRPC Status Code:__ Cancelled

__Details:__ Client has successfully connected to server however asp.net core server had no service defined

__Suggested solutions:__ 
- ensure that your gRPC service definition is configured in server 
- when using asp.net core verify Startup.cs (`services.AddGrpc();` and `endpoints.MapGrpcService<ServiceName>();`)

__Verified for:__ gRPC for C# client

## Status(StatusCode=Unavailable, Detail="DNS resolution failed")

__gRPC Status Code:__ Unavailable

__Details:__ DNS resolver can not find target host

__Suggested solutions:__ 
- verify that service name is typo free
- verify service was deployed
- verify service is reachable

## Status(StatusCode=Cancelled, Detail="Received http2 header with status: 404") 

__gRPC Status Code:__ Cancelled

__Details:__ Client has successfully connected to server however server had no service defined

__Suggested solutions:__ 
- ensure that your gRPC service definition is configured in server (in asp.net core open Startup.cs)

## Status(StatusCode=Unavailable, Detail="Empty update")

__gRPC Status Code:__ Unavailable

__Details:__ Load balancer returns empty list of available servers

__Suggested solutions:__ 
- ensure services were deployed
- ensure loadbalancer is configured to find servers

## Status(StatusCode=Internal, Detail="Error starting gRPC call: Name or service not known")

__gRPC Status Code:__ Internal

__Details:__ ???

__Suggested solutions:__ 
- ???

## Status(StatusCode=Internal, Detail="Error starting gRPC call: Connection refused")

__gRPC Status Code:__ Internal

__Details:__ Service is not listening on that port

__Suggested solutions:__ 
- verify port configuration for service
- verify target address for client 

## Status(StatusCode=Unavailable, Detail="Bad gRPC response. HTTP status code: 502")

__gRPC Status Code:__ Unavailable

__Details:__ Service is not ready to get requests

__Suggested solutions:__ 
- ensure server is ready before doing request
- apply some delay between server startup and first client call 

## Status(StatusCode=Internal, Detail="Error starting gRPC call: An error occurred while sending the request.")

__gRPC Status Code:__ Internal

### Alternative

__Details:__ Trying to connect using unsecured connection using dotnet client 

__Suggested solutions:__ 
- add `AppContext.SetSwitch("System.Net.Http.SocketsHttpHandler.Http2UnencryptedSupport", true);`
- more info here https://docs.microsoft.com/en-us/aspnet/core/grpc/troubleshoot?view=aspnetcore-3.1#call-insecure-grpc-services-with-net-core-client

__Verified for:__ gRPC for dotnet client

### Alternative

__Details:__ Reverse proxy is listening using HTTP 1.1 protocol

__Suggested solutions:__ 
- ensure all components are using HTTP 2.0

## Status(StatusCode=Internal, Detail="Error starting gRPC call: No connection could be made because the target machine actively refused it.")

__gRPC Status Code:__ Internal

__Details:__ 
- The DNS resolution went well or was skipped,
- Client has resolved numeric IP address,
- Client can not connect using IP, connection was not established,
  - Possibly server is not hosted under resolved IP address,
  - The cause may be a bad port configuration,

__Suggested solutions:__ 
- verify if the server was deployed for IP resolved by client,
- verify the server configuration, in particular exposing/listening to ports, 
- verify the port used by the client,

__Verified for:__ gRPC for dotnet client

## Status(StatusCode=Internal, Detail="Error starting gRPC call: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.")

__gRPC Status Code:__ Internal

__Details:__ 
- The DNS resolution went well or was skipped,
- Server is refusing connection

__Suggested solutions:__ 
- verify if the server was deployed for IP resolved by client,
- verify the server configuration, in particular exposing/listening to ports, 

__Verified for:__ gRPC for dotnet client

## Status(StatusCode=Unavailable, Detail="connections to all backends failing")

__gRPC Status Code:__ Unavailable

__Details:__ 
- Client has received list of servers from load balancer however it is can not connect to any target server

__Suggested solutions:__ 
- verify if load balancer has updated list of servers,
- verify ports returned by load balancer,
- verify target machines if they are alive,
- verify the server configuration, in particular exposing/listening to ports, 

__Verified for:__ gRPC for C# client
