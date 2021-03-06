# shrtie [![Build Status](https://travis-ci.org/realfake/shrtie.svg?branch=master)](https://travis-ci.org/realfake/shrtie) [![GoDoc](https://godoc.org/github.com/realfake/shrtie?status.svg)](https://godoc.org/github.com/realfake/shrtie)

## What is this ?
This projects aims to provide a small and flexible url shortner

## Features
**Flexible:** Supports several routers such as `gorilla/mux`, `julienschmidt/httprouter` and the default `http.ServeMux` server.

## How to get it ?
```bash
go get github.com/realfake/shrtie
```

## How to use it ?
Have a look in the `examples/` folder.

```go
package main

import (
	"log"
	"net/http"

	"github.com/julienschmidt/httprouter"
	"github.com/realfake/shrtie"
	backend "github.com/realfake/shrtie/backend/redis"
	redis "gopkg.in/redis.v4"
)

func main() {

	b, err := backend.New(&redis.Options{
		Addr:     "localhost:6379",
		Password: "",
		DB:       0,
	})

	if err != nil {
		log.Fatal(err)
	}

	s := shrtie.New(b)
	server := httprouter.New()

	// Get RedirectHandler and warp it
	// into a julienschmidt/httprouter compatible handler function
	server.GET("/s/:id", s.RedirectHandler().Httprouter())
	server.POST("/s", s.SaveHandler().Httprouter())

	server.GET("/info/:id", s.InfoHandler().Httprouter())

	// Start server
	log.Print(http.ListenAndServe(":9999", server))

}
```
