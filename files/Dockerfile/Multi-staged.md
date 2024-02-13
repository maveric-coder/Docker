# Multi-stage builds
Multi-stage builds are useful to anyone who has struggled to optimize Dockerfiles while keeping them easy to read and maintain.

## Use multi-stage builds
With multi-stage builds, you use multiple `FROM` statements in your Dockerfile. Each `FROM` instruction can use a different base, and each of them begins a new stage of the build. You can selectively copy artifacts from one stage to another, leaving behind everything you don't want in the final image.

The following Dockerfile has two separate stages: one for building a binary, and another where the binary gets copied from the first stage into the next stage.

```yaml
# syntax=docker/dockerfile:1
FROM golang:1.21
WORKDIR /src
COPY <<EOF ./main.go
package main

import "fmt"

func main() {
  fmt.Println("hello, world")
}
EOF
RUN go build -o /bin/hello ./main.go

FROM scratch
COPY --from=0 /bin/hello /bin/hello
CMD ["/bin/hello"]
```

You only need the single Dockerfile. No need for a separate build script. Just run `docker build`.

```sh
docker build -t hello .
```
The end result is a tiny production image with nothing but the binary inside. None of the build tools required to build the application are included in the resulting image.

**How does it work?**
The second `FROM` instruction starts a new build stage with the scratch image as its base. The `COPY --from=0` line copies just the built artifact from the previous stage into this new stage. The `Go SDK` and any intermediate artifacts are left behind, and not saved in the final image.

## Name your build stages
By default, the stages aren't named, and you refer to them by their integer number, starting with 0 for the first `FROM` instruction. However, you can name your stages, by adding an AS <NAME> to the `FROM` instruction. This example improves the previous one by naming the stages and using the name in the `COPY` instruction. This means that even if the instructions in your Dockerfile are re-ordered later, the `COPY` doesn't break.

```yaml
# syntax=docker/dockerfile:1
FROM golang:1.21 as build
WORKDIR /src
COPY <<EOF /src/main.go
package main

import "fmt"

func main() {
  fmt.Println("hello, world")
}
EOF
RUN go build -o /bin/hello ./main.go

FROM scratch
COPY --from=build /bin/hello /bin/hello
CMD ["/bin/hello"]
```

## Stop at a specific build stage
When you build your image, you don't necessarily need to build the entire Dockerfile including every stage. You can specify a target build stage. The following command assumes you are using the previous Dockerfile but stops at the stage named build:
```sh
docker build --target build -t hello .
```
A few scenarios where this might be useful are:

* Debugging a specific build stage
* Using a `debug` stage with all debugging symbols or tools enabled, and a lean `production` stage
* Using a `testing` stage in which your app gets populated with test data, but building for production using a different stage which uses real data
