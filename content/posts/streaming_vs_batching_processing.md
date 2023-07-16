---
title: "Streaming vs batching"
date: 2023-07-16T16:03:38-05:00
draft: true
---

Pros of streaming:
  * Lower latency

Cons of streaming:
  * Harder to achieve good throughput: processing a bunch of data point in a batching fashion can usually take advantage of natural hardware support for batching like:
    * CPU: SIMD, vectorization, multicore parallelism
    * Memory/Disk: read/write a big chunk of data at once has better throughput than read/write a bunch of small chunks
    * Network: sending/receiving a group of large packets at once is more efficient than sending/receiving a bunch of small packets
  * Harder operation-wise to run a production streaming system. This is a significant problem that should not be overlooked:
    * The periodic shutdown and restart nature of batch processing system can mitigate, resolve lots of problem. (Remember the common advice to restart your computer when there is a problem?) Streaming processing runs continuously so it's harder to maintain its uptime.
    * Since batching processing run in concrete chunk, it has a nature start and stop point, which makes it easier to debug e.g easier to associate batch run X with concrete input set Y, output log Z. 
    * Downstream consumer of streaming system usually makes assumption about the uptime of streaming system making it harder to stop, debug, and fix problems of a streaming system.
  * Because of the two drawbacks above, streaming processing technologies are also more complex than batch one. One shouldn't discount this factor. It makes running a streaming system more complex: more tuning, can be more buggy, harder to debug, ...

Of course, there is not a clear cut difference between streaming and batching. It's a gradient of how much data your system process at once and how continuos/uptime your system is supposed to be.

Even though streaming processing is more fashionable these days, it comes with pretty significant drawbacks. One should think carefully whether low-latency is worth the cost of lower throughput, harder operation, and more complexity.