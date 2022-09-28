# Protobuf
```protobuf
syntax = "proto3";

message Address{
	string street = 1;
	uint32 building = 2;
}

message Person{
	string name = 1;
	uint32 age = 2;
	Address address = 3;
}
```

```cmd
unix
protoc -I . --cpp_out . person.proto
```

При работе с grpc
```sh
protoc --go_out=. --go_opt=paths=source_relative \
    --go-grpc_out=. --go-grpc_opt=paths=source_relative \
    proto/rusprofile.proto
```
