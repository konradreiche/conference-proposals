# Building A Highly Concurrent Cache in Go: A Hitchhiker's Guide

## Elevator Pitch

Why doesn't the Go standard library provide a concurrent cache? Because Go emphasizes building custom data structures that fit your need. Learn how to design, implement, and optimize a concurrent cache in Go, combing LRU & LFU eviction policy and advanced concurrency patterns beyond sync.Mutex.

## Description

What do hitchhiking and designing a concurrent cache in Go have in common? Well, in both cases, it's a journey full of unexpected twists and turns, with each ride or design decision presenting its own unique set of challenges.

Our system pulls over 3 million cached values per second from a distributed Redis cluster to accelerate upstream service requests, reducing p99 latency from 100 ms to 5 ms. However, as our system grows, adding more nodes to handle the load increases our cloud service expenses in an unsustainable way.

In the past, we would have addressed this issue by adding a simple local in-memory cache as a layer in-between our service and Redis to reduce the load. But not only are we requesting over 18 million keys per minute, the unique set of keys is constantly changing over time. As a result, we need to come up with a more advanced data structure that can cater to these unique requirements.

This talk will guide Gophers through the process of designing, implementing, measuring the performance, and optimizing a concurrent cache in Go, highlighting the importance of tailoring code to our needs. By the end of the talk, Gophers will know why the Go standard library does not provide a one-size-fits it all when it comes to concurrent data structures.

## Outline

### 1. Motivation: Reducing load of a distributed remote cache (5 min)

Set the scene by talking about a Redis cluster that we use as distributed remote cache and how it differs from a local in-memory cache with respect to memory locality and duplication in a multi-deployment service environment.

### 2. What is a Cache? (5 min)

For those new to Go, a good recommendation is to start by designing your data structure as simple as possible. For example, the local in-memory cache could be implemented in just 44 lines as follows:

    type cache struct {
      data map[string]string
    }
    
    func (c *cache) Set(items map[string]string) {
      for key, value := range items {
        c.data[key] = value
      }
    }

    func (c *cache) Get(keys []string) map[string]string {
      result := make(map[string]string)
      for _, key := range keys {
        if value, ok := c.data[key]; ok {
          result[key] = value
        }
      }
      return result
    }

Explore how this API decision already influences the design of our cache, such as the potential optimization opportunities presented by passing a map instead of a single key-value pair for larger data sets. It's important to remember that this cache is intended for a specific purpose and not as a general-purpose abstraction to be released for public consumption later on.

### 3. Concurrency: A Beginner's Guide (5 min)

Cover the basics of concurrency primitives, such as `sync.Mutex` and `sync.RWMutex`, and emphasize the importance of starting with simple, easy-to-understand scopes before exploring more complex concurrency patterns. It's also essential to assess whether more complicated synchronization methods, for example involving channels, are even necessary.

### 4. Benchmarking & Profiling (10 min)

This section will discuss how Go's benchmark framework can help us establish a performance baseline and improve it incrementally. We'll cover best practices for benchmarking, and show how it can be used to iterate on our implementation by generating profiles highlighting specifically heap, CPU and block profiles and the usage of `go tool pprof`.

### 5. LRFU: Combining LRU and LFU Eviction Policy (7 min)

Based on the results from the previous section we now have a foundation to start improving the solution with respect to: hit rate, runtime and memory allocations. This section will explain two common cache eviction policies, Least Recently Used (LRU) and Least Frequently Used (LFU), and illustrate how differently they perform. The concept will be taken further by the idea of combining both policies into one algorithm: [LRFU cache replacement algorithm](https://www.openu.ac.il/home/wiseman/2os/lru/lrfu.pdf), which combines both policies into one metric score.

However, implementing LRFU involves more complex code, including maintaining a synchronized heap to order the items by score, and decaying reference scores over time. A [more efficient](https://github.com/dbaarda/DLFUCache/blob/master/DESIGN.rst) way to implement the decay is by incrementally increasing the scores of new values instead.

### 6. Advanced Concurrency: Atomic Operations, Channels & More (10 min)

The increase in complexity, while improving hit rate, has made the implementation slower. Explore additional concurrency primitives that can boost performance beyond `sync.Mutex`, such as the `atomic` package, `sync.Map` and alternative implementations like `xsync.Map`. Examine the potential performance impact of using `sync.RWMutex` on a multi-core deployment with examples of benchmarks that show how systems with 4 or more CPU cores `sync.Map` starts to outperform `sync.RWMutex`.

Additionally, introduce the use of channels, bearing in mind Rob Pike's advice to "share memory by communicating" rather than "communicating by sharing memory" and "Channels orchestrate; mutexes serialize". In particular, rethinking when to perform updates and performing them alongside set operations can be crucial in improving performance. Finally, show how the tight lifecycle between set and get operations can be used to optimize allocations using `sync.Pool` as well as the dangers that come with it.

### 7. Caching in Go: Lessons Learned (3 min)

Writing a cache in Go can be straightforward, but maintaining simplicity and optimizing performance can be challenging. To succeed, it's essential to follow some of Go principles of starting simple and measuring progress, as well as avoiding guesswork when identifying performance issues. In this section, we'll summarize key learnings from this journey and the Go ecosystem to help you on your own adventure of writing concurrent data structures.
