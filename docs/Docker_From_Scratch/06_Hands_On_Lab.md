# 06 — Hands-On Lab

## 🧠 The One Idea

**Build the same Go app two ways — single-stage and multistage — and watch the image shrink from
~800MB to ~10MB.** Seeing the number drop is what makes the concept stick.

The one-liner: **"Single-stage vs multistage on the same app proves the size and security win in one
`docker images` line."**

---

## 1. The tiny app — `main.go`

```go
package main

import ("fmt"; "net/http")

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintln(w, "hello from a container")
    })
    http.ListenAndServe(":8080", nil)
}
```

```bash
go mod init demo
```

---

## 2. The "bad" single-stage image — `Dockerfile.fat`

```dockerfile
FROM golang:1.25
WORKDIR /src
COPY . .
RUN go build -o /app .
ENTRYPOINT ["/app"]
```

```bash
docker build -f Dockerfile.fat -t demo:fat .
```

---

## 3. The multistage image — `Dockerfile`

```dockerfile
FROM golang:1.25 AS builder
WORKDIR /src
COPY go.mod ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 go build -o /app .

FROM gcr.io/distroless/static
COPY --from=builder /app /app
USER nonroot
ENTRYPOINT ["/app"]
```

```bash
docker build -t demo:slim .
```

---

## 4. Compare the sizes

```bash
docker images | grep demo
# demo  fat    ~800MB
# demo  slim   ~12MB
```

That's the headline number to remember and quote.

---

## 5. Run it & inspect

```bash
docker run -d -p 8080:8080 --name web demo:slim
curl localhost:8080            # hello from a container

docker exec -it web sh         # ❌ fails — distroless has no shell (that's the point)
docker history demo:slim       # see the layers
docker stop web && docker rm web
```

**Extensions:** add a `.dockerignore`; scan with `trivy image demo:slim`; flip the COPY order to
break the cache and watch deps re-download; try a `scratch` base.

---

## 🎤 Say this in the interview

- *"I've taken a Go service from ~800MB single-stage to ~12MB multistage on distroless — same
  binary, fraction of the surface."*
- *"The slim image has no shell, so `docker exec sh` fails — which is exactly the security property I
  want in production."*
- *"I verify the win with `docker images` and `docker history`, and scan the result in CI."*

➡️ **Next:** [07 — Interview Q&A flashcards](./07_Interview_QA_Flashcards.md)
