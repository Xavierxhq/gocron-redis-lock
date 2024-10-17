# redislock

[![CI State](https://github.com/Xavierxhq/gocron-redis-lock/actions/workflows/go_test.yml/badge.svg?branch=main&event=push)](https://github.com/Xavierxhq/gocron-redis-lock/actions)
![Go Report Card](https://goreportcard.com/badge/github.com/Xavierxhq/gocron-redis-lock) [![Go Doc](https://godoc.org/github.com/Xavierxhq/gocron-redis-lock?status.svg)](https://pkg.go.dev/github.com/Xavierxhq/gocron-redis-lock)

## install

```
go get github.com/Xavierxhq/gocron-redis-lock/v2
```

## usage

Here is an example usage that would be deployed in multiple instances

```go
package main

import (
	"fmt"
	"time"

	"github.com/Xavierxhq/gocron/v2"
	"github.com/redis/go-redis/v9"

	redislock "github.com/Xavierxhq/gocron-redis-lock/v2"
)

func main() {
	redisOptions := &redis.Options{
		Addr: "localhost:6379",
	}
	redisClient := redis.NewClient(redisOptions)
	locker, err := redislock.NewRedisLocker(redisClient, redislock.WithTries(1))
	if err != nil {
		// handle the error
	}

	s, err := gocron.NewScheduler(gocron.WithDistributedLocker(locker))
	if err != nil {
		// handle the error
	}
	_, err = s.NewJob(gocron.DurationJob(500*time.Millisecond), gocron.NewTask(func() {
		// task to do
	}, 1))
	if err != nil {
		// handle the error
	}
	
	s.Start()
}
```

The redis UniversalClient can also be used

```go
package main

import (
	"fmt"
	"time"

	"github.com/redis/go-redis/v9"

	redislock "github.com/Xavierxhq/gocron-redis-lock/v2"
)

func main() {
	redisOptions := &redis.UniversalOptions{
		Addrs: []string{"localhost:6379"},
	}
	redisClient := redis.NewUniversalClient(redisOptions)
	locker, err := redislock.NewRedisLocker(redisClient, redislock.WithTries(1))
	if err != nil {
		// handle the error
	}
}
```
