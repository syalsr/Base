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