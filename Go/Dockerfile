FROM golang:1.20.3-bullseye as build-stage

WORKDIR /usr/src/fizzbuzz-app 

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 go build -v -o /usr/src/fizzbuzz-app/bin/

FROM scratch
WORKDIR / 
COPY --from=build-stage /usr/src/fizzbuzz-app/bin/ ./
COPY --from=build-stage /usr/src/fizzbuzz-app/templates ./templates
CMD ["./fizzbuzz", "serve"]