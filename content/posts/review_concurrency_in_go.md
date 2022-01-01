---
title: "Book Review: Concurrency in Go"
date: 2021-01-01T16:03:38-05:00
draft: false
---
I recently finished reading the book "Concurrency in Go" by Katherine Cox-Buday. It is a relatively short book. A focused read can finish it in a few days. The content is relatively basic, consisting of a mix of concurrency fundamentals and of specific patterns commonly found in Go concurrent code. Overall, for someone who is new to Go concurrent primitives, the book provides a good review.

I learnt some new things from the books:
* `Concurrency is a property of the code, parallelism is a property of the running program.` I like this observation. Intuitively, I understsand that concurrency means different codes that run together within the same time window while parallelism means different codes that run at the exact same point in time. While concurrent code can run in parallel or not, parallel code must be concurrent. However, that intuitive understanding is rather descrptive more than definitive. The statement above significantly clarify my intuitive understanding.
* `Communicating sequential processes (CSP)` The idea that concurrent processes communicating with each other through channel is not new. It has been there with Linux inter-process communication mechanism like pipe and Unix domain socket. However, the idea of modeling all concurrent code with this formal communication is rather new to me. Before, my mental model around concurrent code cooperation had been centered more around shared memory than communication channels. However, shared memory incurs the downside of having developer making careful decisions and implementations around concurrent access in critical sections to avoid race conditions. I couldn't believe that I didn't see this line in the Golang `sync` library before but now it all makes sense. `Other than the Once and WaitGroup types, most are intended for use by low-level library routines. Higher-level synchronization is better done via channels and communication.`
* `sync.Pool` a concurrent-safe implementation of the object pool pattern. This construct is for optimization. Developers can use it to implement patterns like database connection pool and objective pool. The limitation is that object in pools should be mostly read-only and indistinguishable.
* `<-chan` and `chan<-` as read-only and write-only channel types respectively.
* `Channel ownership` The goroutine instantiates a channel should own it. That means the owner goroutine should be the sole goroutine which is responsible for writing and closing it. A owner goroutine can pass ownership to another goroutine. An exception to this rule that I can think of is the case of multiple producers in the producer-consumer pattern.
* `select` statement with multiple case clause all ready to be read from will uniformly randomly chose a case statement to evaluate.
* `or-done-channel` pattern. Instead of writing this verbose piece of code
```
for {
    select {
    case <-done:
        ....
        return
    case val, ok := <- some_channel:
        if !ok {
            return
        }
        .....
    }
}
```
we can shorten it to
```
for val := range or_done(some_channel, done) {
    ....
}
```
* Adding queue, a.k.a buffered channel, can be dangerous because it can hide deadlock, livelock issues
* `Little's law` to analyze a stable system
```
L = nW

L: the average number of units in the system
n: average arrival rate of units
W: average time a unit spent in the system
```
Little's law can be used to analyze a QPS, latency and system resource capacity in a webserver or a streaming processing system
* `context.Context` is a data structure with 2 primary elements 1) a done channel 2) a key-value map of values
* `Goroutine scheduling algorithm:` Go runtime uses a work stealing algorithm to schedule go routines. The algorithm uses `continuation` instead of `goroutine` as the basic unit. There are 3 primary concepts: `OS thread`, `green thread/context` (controlled by GOMAXPROCS env var), `continuation/goroutine`. (M os threads : P green threads : G goroutine, usually M == P)
    * When a goroutine reaches a fork-join/preemption point, it can be preempted, and is added to a deque of the existing context. The preemption points include I/O call, function call, syscall and async preemption. Since go 1.14, goroutine can be asynchronously preempted (https://go.dev/doc/go1.14#runtime) so long loop can nolonger deadlock the scheduler or delay GC significantly. (The proposal is at https://github.com/golang/proposal/blob/master/design/24543-non-cooperative-preemption.md)
    * When a continuation is added to a dequeue, another green thread/context can steal the continuation to do it.
    * When a goroutine is blocked by I/O, it is added to a deque of a global context. The goroutine is decoupled from the green thread but still coupled to the OS thread. This way the green thread is free to do other works.