# gRPC Example
# FIXED!

  - This project is fork form https://github.com/kantoniak/grpc-example
  - upgrade build gradle to 3.1.4
  - upgradle protobuf gradle version to 0.8.6
  - upgradle grpc library version to 1.14.0
  
=================

A simple project to create a simple bootstrap for Android apps using gRPC and Protocol Buffers.

## Submodules

* `proto` contains proto definitions and generated classes
* `api-server` serves as a gRPC endpoint
* `user-app` is an android application

## Android client

To connect with running local gRPC instance you will have to forward back device localhost port to PC port:
```bash
adb reverse tcp:8800 tcp:8800
```

## API server

Server can be run straight from gradle, or as a part of the distribution.

### Gradle CLI

```bash
cd <root folder>
gradle :api-server:build  # Only builds
gradle :api-server:run    # Builds and starts the server
```

### Running as distribution
```bash
cd <root folder>
gradle :api-server:distZip
cp api-server/build/distributions/api-server.zip <destination>
```
```bash
cd <destination>
unzip api-server.zip
cd api-server
./bin/api-server
```

### Running as distribution from Android Studio

Gradle cannot handle two projects with common dependency at the same time. You can use `api-server` run configuration (or type `gradle :api-server:runServer`) to start server from Android Studio.

### CLI for RPCs

You can use [Polyglot](https://github.com/grpc-ecosystem/polyglot) to test calls. Just send JSON-formatted input to the script:

```bash
echo {} | java -jar polyglot.jar --command=call --endpoint=localhost:8800 \
    --full_method="com.kantoniak.examplegrpc.proto.EntryService/GetAll" \
    --proto_discovery_root="<project-root>/proto/src/main/proto"
```

```bash
java -jar polyglot.jar --command=list_services \
    --proto_discovery_root="<project-root>/proto/src/main/proto"
```

## Dependency on protos

If your module needs to use classes generated from proto, remember to add
```groovy
apply from: project(':proto').file("proto-deps.gradle")
```
```groovy
dependencies {
    implementation project(":proto")
}
```
to `build.gradle` of the module.
