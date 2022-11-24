---
title: "How to make software run faster"
date: 2022-08-15T16:03:38-05:00
draft: false
---

After almost a decade working as a professional software engineer, I have seen that there are only a few fundamental levers that an engineer can use to make software more performant, either lower latency or higher through. I'd like to list them here and include tradeoff involving with each:
 
* Pattern 1: Use faster or more hardware:
 * `Common practices`: Upgrade CPU, memory, network equipment, use custom ASIC, use more machines, use the cloud.
 * `Difficulties`: convince management that the engineering hour cost to write faster software is more expensive than the hardware cost.
 * `Tradeoff`: mostly $ cost
 
* Pattern 2: Relax the requirements and/or reduce scope or the software:
 * `Common practices`: reduce requirements like consistency, durability, reduce the amount of features supported, implement simpler features with less data.
 * `Difficulties`: must understand the use cases well in order to strike the right balance between requirements vs usefulness. Need to convince other people of the right balance.
 * `Tradeoff` software's usefulness or feature completeness
 
* Pattern 3: Given a fixed set of requirements, do less stuff by being smarter
 * `Common practices`: Use faster algorithm, data structure, languages, libraries, framework, faster compiler, take advantage of language-specific tricks, of JIT
 * `Difficulties`: must be knowledgeable about algorithms, data structure, languages, libraries, and frameworks
 * `Tradeoff`: potentially more code complexity, less maintainability, less-supported libraries, framework
 
* Pattern 4: Take advantage of hardware pattern
 * `Common practices`: Read/write data from/to disk sequentially instead of randomly, batching data when writing to disk or sending over network, use faster hardware instruction
 * `Difficulties`: must be knowledgeable about low-level hardware, need to restructure code to take advantage of these patterns
 * `Tradeoff`: potentially more code complexity, less maintainability, less portability
 
* Pattern 5: Parallelize code
 * `Common practices`: Use multi-processing, multi-threading, use GPU instead of CPU for ML
 * `Difficulties`: need to be knowledgeable about
 * `Tradeoff`: more code complexity, more bugs, not every logic can be parallelize
 
* Pattern 6: Caching, precomputing
 * `Common practices`: pre-compute result and store in app-level cache, take advantage of OS cache, hardware cache
 * `Difficulties`: cache invalidation, must be knowledgeable about low-level OS and hardware to take advantage of them well
 * `Tradeoff`: more code complexity, less consistency, durability, $ cost
 
* Pattern 7: Move things closer to each other to reduce latency
 * `Common practices`: Move data closer to computation, move data/compute closer to clients
 * `Difficulties`: usually this can reduce consistency of the system by keeping a source of truth, and copies closer to clients/computation
 * `Tradeoff`: more code complexity, less consistency, $ cost
 