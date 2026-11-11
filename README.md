# grpc-with-protobuf: gRPC from scratch in TypeScript

A small gRPC server in TypeScript with an address-book service defined in Protocol Buffers. It's the groundwork for streaming Solana data over gRPC (Yellowstone / Geyser): the same `.proto` files, generated types and call patterns, without the blockchain noise.

What you'll find here:

- defining messages and a service in a `.proto` file
- loading the schema at runtime with `@grpc/proto-loader`
- generating TypeScript types from the schema so every handler is typed
- implementing unary RPCs on a `@grpc/grpc-js` server
- calling the service from the command line with `grpcurl`

## The service

```protobuf
service AddressBookService {
  rpc AddPerson(Person) returns (Person);
  rpc GetAllPersons(Empty) returns (PersonList);
  rpc GetPersonByName(GetPersonByNameRequest) returns (Person);
  rpc GetPersonsByAge(GetPersonByAgeRequest) returns (PersonList);
  rpc DeletePersonByName(GetPersonByNameRequest) returns (Person);
}
```

`Person` is `{ name: string, age: int32 }`. The full schema is in [`src/a.proto`](src/a.proto).

## How it works

1. **Schema.** `src/a.proto` defines the messages and the `AddressBookService`.
2. **Types.** `src/generated/` holds TypeScript types generated from the schema with `proto-loader-gen-types`, so the handlers in `src/index.ts` are typed as `AddressBookServiceHandlers`.
3. **Server.** `src/index.ts` loads the `.proto` at runtime, registers one handler per RPC and listens on `0.0.0.0:50051`. People are kept in an in-memory array, so data resets when the server restarts.

To regenerate the types after changing the schema:

```bash
npx proto-loader-gen-types --grpcLib=@grpc/grpc-js --outDir=src/generated src/a.proto
```

## Run it

Prerequisites: Node.js 18+, and [grpcurl](https://github.com/fullstorydev/grpcurl) to call the server.

```bash
git clone https://github.com/princedotrs/grpc-with-protobuf.git
cd grpc-with-protobuf
npm install
npm start          # compiles and starts the server on port 50051
```

In another terminal (the server doesn't enable reflection, so pass the schema with `-proto`):

```bash
grpcurl -plaintext -import-path src -proto a.proto \
  -d '{"name": "Ada", "age": 30}' localhost:50051 addressbook.AddressBookService/AddPerson

grpcurl -plaintext -import-path src -proto a.proto \
  -d '{}' localhost:50051 addressbook.AddressBookService/GetAllPersons

grpcurl -plaintext -import-path src -proto a.proto \
  -d '{"age": 30}' localhost:50051 addressbook.AddressBookService/GetPersonsByAge
```

## Project layout

```
src/
├── a.proto        # messages and service
├── index.ts       # server and handlers
└── generated/     # TypeScript types generated from a.proto
```

## Ideas to extend it

- Add a server-streaming RPC that pushes every new person to subscribers, the same pattern Yellowstone uses for transactions
- Write a typed TypeScript client
- Persist people in SQLite or Postgres

## License

MIT
