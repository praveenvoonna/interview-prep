# 06 — Hands-On Lab (Go)

## 🧠 The One Idea

**You only really understand gRPC once you've gone `.proto` → generated code → server → client and
watched a typed call cross the wire.** This lab is that loop, end to end, in Go.

The one-liner: **"Define the contract, generate the stubs, implement the server, call it from the
client — that's the whole gRPC workflow."**

---

## 1. Install the tooling

```bash
# protoc compiler (macOS)
brew install protobuf

# Go plugins
go install google.golang.org/protobuf/cmd/protoc-gen-go@latest
go install google.golang.org/grpc/cmd/protoc-gen-go-grpc@latest
# ensure $(go env GOPATH)/bin is on PATH
```

---

## 2. Define the contract — `proto/user.proto`

```proto
syntax = "proto3";
package user;
option go_package = "example/userpb";

message GetUserRequest { string id = 1; }
message User { string id = 1; string name = 2; int32 age = 3; }

service UserService {
  rpc GetUser(GetUserRequest) returns (User);
}
```

---

## 3. Generate the stubs

```bash
protoc --go_out=. --go_opt=paths=source_relative \
       --go-grpc_out=. --go-grpc_opt=paths=source_relative \
       proto/user.proto
```

This produces `user.pb.go` (messages) and `user_grpc.pb.go` (client + server interfaces).

---

## 4. Implement the server

```go
type server struct{ userpb.UnimplementedUserServiceServer }

func (s *server) GetUser(ctx context.Context, req *userpb.GetUserRequest) (*userpb.User, error) {
    if req.Id == "" {
        return nil, status.Error(codes.InvalidArgument, "id required")
    }
    return &userpb.User{Id: req.Id, Name: "Ann", Age: 30}, nil
}

func main() {
    lis, _ := net.Listen("tcp", ":50051")
    s := grpc.NewServer()
    userpb.RegisterUserServiceServer(s, &server{})
    log.Fatal(s.Serve(lis))
}
```

(Embedding `UnimplementedUserServiceServer` gives forward compatibility when new methods are added.)

---

## 5. Write the client

```go
func main() {
    conn, _ := grpc.NewClient("localhost:50051",
        grpc.WithTransportCredentials(insecure.NewCredentials())) // TLS in prod!
    defer conn.Close()
    c := userpb.NewUserServiceClient(conn)

    ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
    defer cancel()

    u, err := c.GetUser(ctx, &userpb.GetUserRequest{Id: "42"})
    if err != nil { log.Fatal(err) }
    fmt.Printf("%s, age %d\n", u.Name, u.Age)
}
```

Run the server, then the client → you've made a typed remote call over HTTP/2.

---

## 6. Try the extensions

- Add **server streaming**: `rpc ListUsers(Filter) returns (stream User);` and read in a loop.
- Add a **logging interceptor** (Lesson 04).
- Turn on **TLS**, then **mTLS**.
- Force a **`DEADLINE_EXCEEDED`** by sleeping in the handler past the client timeout.
- Inspect the service with **grpcurl** (`grpcurl -plaintext localhost:50051 list`).

---

## 🎤 Say this in the interview

- *"The workflow is `.proto` → `protoc` codegen → implement the server interface → call the typed
  client; the contract drives both sides."*
- *"I embed the Unimplemented server for forward compatibility, set a deadline on the client, and use
  insecure creds only in dev — TLS in production."*
- *"I can extend it with streaming, interceptors, and TLS, and debug it live with grpcurl."*

➡️ **Next:** [07 — Interview Q&A flashcards](./07_Interview_QA_Flashcards.md)
