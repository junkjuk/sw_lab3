FROM golang:1.20.3-bullseye

WORKDIR /usr/src/fizzbuzz-app 

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN go build -v -o /usr/src/fizzbuzz-app/

CMD ["./fizzbuzz", "serve"]