# gRPC Learning From Scratch

A practical TypeScript implementation demonstrating gRPC concepts with an Address Book service. This project showcases how to build a complete gRPC server with Protocol Buffers, TypeScript type generation, and CRUD operations.

## 🎯 What You'll Learn

- Setting up a gRPC server with TypeScript
- Defining services with Protocol Buffers
- Implementing unary RPC methods
- Generating TypeScript types from .proto files
- Building a complete CRUD API with gRPC

## 📋 Prerequisites

- Node.js (v14 or higher)
- npm or yarn
- Basic knowledge of TypeScript

## 🚀 Getting Started

### Installation

1. Clone the repository:
```bash
git clone https://github.com/princedotrs/grpc-with-protobuf.git
cd grpc-with-protobuf
```

2. Install dependencies:
```bash
npm install
```

3. Build and start the server:
```bash
npm start
```

The gRPC server will start running on `localhost:50051`.

## 📁 Project Structure

```
grpc-learn/
├── src/
│   ├── a.proto                    # Protocol Buffer definitions
│   ├── index.ts                   # Main server implementation
│   └── generated/                 # Auto-generated TypeScript types
│       ├── a.ts                   # Main proto types
│       └── addressbook/           # Service-specific types
│           ├── AddressBookService.ts
│           ├── Person.ts
│           ├── PersonList.ts
│           ├── Empty.ts
│           ├── GetPersonByNameRequest.ts
│           └── GetPersonByAgeRequest.ts
├── dist/                          # Compiled JavaScript output
├── package.json
├── tsconfig.json
└── README.md
```

## 🛠 API Reference

The Address Book service provides the following RPC methods:

### AddPerson
Adds a new person to the address book.
- **Request**: `Person { name: string, age: number }`
- **Response**: `Person`

### GetAllPersons
Retrieves all persons from the address book.
- **Request**: `Empty {}`
- **Response**: `PersonList { people: Person[] }`

### GetPersonByName
Finds a person by their name.
- **Request**: `GetPersonByNameRequest { name: string }`
- **Response**: `Person`

### GetPersonsByAge
Finds all persons with a specific age.
- **Request**: `GetPersonByAgeRequest { age: number }`
- **Response**: `PersonList { people: Person[] }`

### DeletePersonByName
Removes a person from the address book by name.
- **Request**: `GetPersonByNameRequest { name: string }`
- **Response**: `Person` (the deleted person)

## 🔧 Protocol Buffer Schema

The service is defined using Protocol Buffers v3:

```protobuf
syntax = "proto3";

package addressbook;

message Person {
  string name = 1;
  int32 age = 2;
}

message GetPersonByNameRequest {
  string name = 1;
}

message GetPersonByAgeRequest {
  int32 age = 1;
}

message PersonList {
  repeated Person people = 1;
}

message Empty {}

service AddressBookService {
  rpc AddPerson(Person) returns (Person);
  rpc GetAllPersons(Empty) returns (PersonList);
  rpc GetPersonByName(GetPersonByNameRequest) returns (Person);
  rpc GetPersonsByAge(GetPersonByAgeRequest) returns (PersonList);
  rpc DeletePersonByName(GetPersonByNameRequest) returns (Person);
}
```

## 🧪 Testing the API

You can test the gRPC server using various gRPC clients:

### Using grpcurl (if installed)
```bash
# Add a person
grpcurl -plaintext -d '{"name": "John Doe", "age": 30}' localhost:50051 addressbook.AddressBookService/AddPerson

# Get all persons
grpcurl -plaintext -d '{}' localhost:50051 addressbook.AddressBookService/GetAllPersons

# Get person by name
grpcurl -plaintext -d '{"name": "John Doe"}' localhost:50051 addressbook.AddressBookService/GetPersonByName
```

### Using Postman or Insomnia
1. Create a new gRPC request
2. Import the proto file (`src/a.proto`)
3. Connect to `localhost:50051`
4. Test the available methods

## 🏗 Development

### Building the Project
```bash
# Compile TypeScript
npx tsc -b

# Run the compiled JavaScript
node dist/index.js
```

### Project Scripts
- `npm start` - Build and start the server
- `npm test` - Run tests (not implemented yet)

## 📚 Key Technologies

- **[@grpc/grpc-js](https://www.npmjs.com/package/@grpc/grpc-js)** - Pure JavaScript gRPC implementation
- **[@grpc/proto-loader](https://www.npmjs.com/package/@grpc/proto-loader)** - Protocol Buffer loader for gRPC
- **TypeScript** - Type-safe JavaScript development
- **Protocol Buffers** - Language-neutral data serialization

## 🎓 Learning Resources

- [gRPC Official Documentation](https://grpc.io/docs/)
- [Protocol Buffers Guide](https://developers.google.com/protocol-buffers)
- [gRPC Node.js Tutorial](https://grpc.io/docs/languages/node/)

## 🤝 Contributing

Feel free to fork this repository and submit pull requests for improvements or additional examples!

## 📄 License

This project is licensed under the ISC License.
