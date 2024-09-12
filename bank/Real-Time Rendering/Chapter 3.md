## Definitions:


- *Shader invocations*: separate instances where shader programs are run
## Data-Parallel Architectures
- CPUs are optimized to handle wide varieties of data structures and large code bases. CPUs can have multiple processors, but each runs code in a mostly serial fashion.
- To minimize the effects of latency, much of a CPUs chip consists of fast local caches, memory that is filled with data likely to be needed next.
- CPUs avoid stalls by using clever techniques such as branch prediction, instruction reordering, register renaming, and cache prefetching
- Much of a GPU's chip area is dedicated to a large set of processors called *shader cores*.
- The GPU is a stream processor, in which ordered sets of similar data are processed in turn, meaning the GPU can process data like pixels in a parallel fashion.
- Shader invocations are as independent as possible. They do not need information from neighboring invocations and do not share writable memory locations.
- GPU is optimized for *throughput*, the maximum rate at which data can be processed.
- The cost of this speed is less chip area dedicated to cache memory and control logic. Latency for each shader core is generally higher than what a CPU encounters.

- Example: Say a mesh is rasterized and 2 thousand pixels have fragments to be processed. Thus, a pixel shader program is to be invoked 2 thousand times. Lets assume we do this on a GPU with only one single shader processor. 
	- It starts to execute the shader program for the first fragment. It performs some arithmetic on values in registers. The registers are local and quick to access, so no stall occurs.
	- The shader processor then comes to an instruction such as a texture access. Since the texture is an entirely separate resource, and not a part of the pixel program's local memory, we have a problem.
	- A memory fetch can take hundreds of thousands of clock cycles, during which, the GPU is doing nothing.
	- This creates a stall for the shader processor.

	-  To make this GPU better, give each fragment a little storage space for its local registers.
	- Now, instead of stalling on a texture fetch, the shader processor is allowed to switch and execute another fragment, #2 of a thousand.
	- This switch is very fast, nothing in the 1st or 2nd fragment is affected other than noting which instruction was executing on the first.
	- Now the 2nd is executes until a texture fetch is encountered again. The shader core now switches to #3. Eventually all fragments are processed.
	- At this point, the shader processor returns to fragment #1. By this time, the texture color has been fetched and can be used. 
	- Processor proceeds in the same fashion until another instruction that is known to stall execution is encountered, or the program completes. Single fragment time is slower, but overall execution time is dramatically reduced.

- In this architecture, latency is hidden by having the GPU stay busy by switching to another fragment. 
- GPUs take this design a step further by separating the instruction execution logic from the data. This is called *single instruction, multiple data (SIMD)*. This arrangement executes the same command in lock-step on a fixed number of shader programs.
- Advantages of SIMD: Considerably less silicon and power needs to be dedicated to processing data and switching compared to using an individual logic and dispatch unit to run each program.
- For our two-thousand fragment example in modern GPU terms, each pixel shader invocation for a fragment is called a *thread*, which in this case is not the same as a CPU thread.
- It consists of a bit of memory for the input values to the shader, along with any register space needed for the shader's execution. 
- Threads that use the same shader program are bundled into groups, called *warps* by NVIDIA and *wavefronts* by AMD.
- A warp/wavefront is scheduled for execution by some number GPU cores, anywhere from 8 to 64, using SIMD-processing. 
- Each thread is mapped to a *SIMD Lane*

-  Example: Say we have two thousand threads to be executed.
	- Warps on NVIDIA GPUs contain 32 threads
	- This yields 2000/32 = 62.5 warps, which is 63 warps with one being half empty
	- The shader program is executed in lock-step on all 32 processors
	- When a memory fetch is encountered, all threads encounter it at the same time, because the same instruction is executed for all
	- The fetch signals that this warp of threads will stall, all waiting for their (different) results
	- Instead of stalling, the warp is swapped out for a different warp of 32 threads, which is then executed by the 32 cores
	- This swapping is just as fast as with the single processor system, since no data within each thread is touched when a warp is swapped
	- Each thread has its own registers, and each warp keeps track of which instruction it is executing
	- Swapping in a new warp is just a matter of pointing the set of cores at a different set of threads to execute, no other overhead.
- In this example, the latency of a memory fetch for a texture can cause a warp to swap out. In practice, they can swap out for shorter delays since the cost of swapping is so low.
- This technique of warp swapping is the major latency-hiding mechanism used by all GPUs.


- The shader program's structure is an imporant characteristic that influences efficiency. A major factor is the amount of register use for each thread.
- In our example, we assume that two thousand threads can all be resident on the GPU at one time. The more registers needed by the shader program associated with each thread, the fewer threads, and thus the fewer warps, can be resident in the GPU
- A shortage of warps can mean that a stall cannot be mitigated by swapping.
- Warps that are resident are said to be "in flight", and this number is called the *occupancy*
- High occupancy means that there are many warps available for processing, so that idle processors are less likely. Low occupancy leads to poor performance
- Frequency of memory fetches also affects how much latency hiding is needed.


