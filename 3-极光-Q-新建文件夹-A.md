## Question 1

**Storage hierarchy structure - What hidden assumptions are behind the simplified concepts presented in textbooks?**

Ans:
1. Access latency is entirely same/uniform (However, in reality, access latency can vary significantly depending on the 
storage device and the specific access pattern. E.g. SSDs generally have much lower access latency than HDDs, and Nvme
SSDs using 3DX-point technology NAND generally have much lower latency compared to traditional SSDs.)
2. The speed of each level is regular (However, in reality, the speed of each level may not be regular. For example, 
the speed of FSB Bus is much slower than that of memory in 2000-2010. For now, the speed of DMI Bus is much faster than
that of memory. Therefore, we have known that the speed of each level is not regular)
3. Programmes are run in local and expected disk formats (However, nowadays many users like me prefer to install
programmes in NAS or cloud storage in order to save the local disk space. Under this situation, programmes are not run
in local, some errors may occur. Plus, some programmes would be installed in external drives with different disk format
, such as exFAT, by users, which is different from the local disk format(maybe APFS))

## Question 2

**Interrupts, DMA and polling - Why is the "choice" of I/O control methods not purely a matter of technical superiority?**

Textbooks will introduce three I/O control methods: program query (polling), interrupt-driven, and DMA. They usually imply that "from polling to DMA is an advancement".

**Ans:** No matter Interrupts, DMA or Polling, each is applicable to different situation which should be considered with   
your actual runtime requirements and CPU-usage etc. For instance, if CPU is designed for sequential reading and writing,
(like Intel Atom Series), polling is more suitable since Interrupts would force CPU to input and output data again.

**Why DMA requires the cooperation of interrupts rather than completely replacing interrupts?**
As for this question,
**Ans:** Because DMA needs to notify the CPU for transferring data and tell CPU the transfer is complete. DMA is only responsible for transferring itself, but not responsible for control(which CPU is supposed to do)


## Question 3

**Is the operating system the "abstract layer of hardware" or the "deception layer of hardware"?**

**Ans:** The operating system is the "deception layer of hardware". It provides a layer of abstraction that makes the 
hardware easier to use. However, it also hides many of the underlying complexities and constraints of the hardware. 
For example, the operating system abstracts away the details of memory management, making it easier for programmers to 
write software without having to worry about things like cache coherence and memory allocation.

## Question 4

**Why do we systematically get misled when measuring computer performance with "instructions per second" and 
"floating-point operations per second"?**

Ans: 
Because different programmes/systems require different precision/algorithms. For instance, as for deploying AI in local,
some prefill models require NVFP4/NVFP8, yet other require INT8/INT4. Under different precision, we need to use
different standard to measure their performance. In addition, for some hardwares, their INTPS and FLOPS could be very
different under NVFPX and INTX. Some even does not support INTX at all.

## Question 5

Textbooks formalise algorithms relying on Knuth's five essential properties: input, output, definiteness, finiteness and effectiveness, meanwhile establishing a clear conceptual boundary between abstract algorithm descriptions and concrete executable computer programs. Many textbook discussions implicitly assume that these five properties can be fully satisfied in real-world software. Critically analyse the mismatch existing between this ideal formal model and practical software development practice, and provide multiple concrete real-world examples to demonstrate how deployed software systems may violate one or more of those idealised properties presented in course materials.

**Ans:** The formal model assumes a closed world: a fixed input domain, a complete specification, guaranteed termination, and primitive steps whose cost can be ignored. Software lives in an open world: inputs are arbitrary, specifications evolve, services are expected to run indefinitely, and “primitive” operations are library or hardware calls that can fail. The boundary is therefore normative rather than descriptive; engineering adds validation, timeouts, and monitoring to approximate the ideal.

Examples, each violating a specific property:
Web server / OS event loop — finiteness: runs forever by design.
Parser fed malformed JSON — output/definiteness: crashes or behaves in an unspecified way.
Floating-point or parallel code — definiteness: same input can produce different results.
Remote API call or big-integer mod — effectiveness: not a pencil-and-paper primitive, and may fail or take unbounded time.
## Question 6

 Big-O asymptotic notation serves as the dominant analytical framework within algorithm textbooks for evaluating algorithm time complexity, which abstracts away constant-factor overheads and low-order terms and only focuses on theoretical growth behaviour under sufficiently large-size input. While this abstraction brings convenience for theoretical comparison among different algorithms, it ignores numerous practical factors that dominate actual program runtime. Discuss multiple important reasons why depending solely on Big-O complexity will produce incorrect judgements for real-world application performance, and give representative practical examples to back up your analysis.
 
**Ans:** Big-O compares growth rates, not runtime. It discards constants and lower-order terms that dominate at realistic sizes, assumes uniform-cost operations on a RAM machine, and ignores caches, memory traffic, I/O, and parallelism. It also compares worst cases, while real inputs are often small, structured, or adversarial in a different way.

Examples:
3n² + 5n + 42 is “O(n²)”, but at n = 10 the constant term matters; insertion sort beats merge sort on small arrays.
Binary search is O(log n) but can lose to a linear scan because of cache misses.
Quicksort is O(n²) worst case but usually fastest; hash tables are O(1) average but O(n) under collisions.
Python str += in a loop is O(n²), and big-integer arithmetic is not constant-time.
## Question 7

The greedy design paradigm taught in algorithm courses builds upon repeatedly making locally optimal decisions at every step with the expectation of finally obtaining a globally optimal solution. Textbook examples such as canonical coin-change problems show cases where greedy strategies succeed. However, greedy algorithms cannot guarantee global optimality for general-purpose problems. Elaborate on the core preconditions that must hold if a greedy algorithm is to compute globally optimal results, and describe several realistic problem scenarios where local greedy choices lead to sub-optimal outcomes even though valid optimal solutions exist for the given problem.

**Ans:** Greedy is optimal only when the greedy-choice property holds (some optimal solution contains the first local choice), optimal substructure holds, and the problem has enough structure — a matroid, or a canonical coin system — to make the choice safe. This is normally proved by an exchange argument; without such a proof, “locally best” is only a heuristic.

Scenarios:
Coins {1, 3, 4}, amount 6: greedy takes 4 + 1 + 1, but 3 + 3 is optimal.
0/1 knapsack, capacity 10, items (6, 60), (5, 50), (5, 50): greedy takes 60, optimum is 100.
Weighted interval scheduling: earliest-finish greedy picks a short low-value job and blocks a long high-value one.

## Question 8

The textbook version of Dijkstra's algorithm is presented as an efficient classic solution for single-source shortest-path problems over graphs, widely applied to abstract graph examples as well as introductory network-routing illustrations. This standard algorithm is derived under a set of unstated built-in prerequisites. Identify these hidden assumptions behind the textbook implementation of Dijkstra's algorithm, and conduct an in-depth discussion on realistic network and graph scenarios in which those assumptions break down, causing the standard Dijkstra procedure to produce wrong results or become completely inapplicable.

**Ans:** The standard implementation assumes non-negative, static, additive, scalar edge weights on a finite, fully known graph, with one source and a centralized priority queue; these assumptions justify finalizing a vertex when it is extracted. It also assumes exact, comparable weights and that “shortest” means minimizing a sum.

Where they break:
Negative edges — finalization is wrong: A→B = 1, A→C = 2, C→B = −2 gives B = 1 instead of 0.
Negative cycles — “shortest path” is undefined, as in currency arbitrage.
Dynamic topology — distances go stale; OSPF reconvergence can cause transient loops and blackholes.
Policy or bandwidth metrics — costs are not additive; BGP uses policy, and bandwidth-constrained routing is NP-hard, so Dijkstra is inapplicable.
Wireless or time-dependent networks — weights fluctuate or depend on departure time.
Very large graphs — the centralized computation is impractical; hierarchy or A* is needed.

## Question 9

**Number representation and floating-point arithmetic - How much of "mathematics" actually survives inside the machine?**

Textbooks introduce data representation with fixed-width binary integers and the IEEE 754 floating-point format, and usually present the latter as "the computer's way of representing real numbers"; the four arithmetic operations are then used as if they obeyed the algebraic laws taught in mathematics. This model silently assumes that: (1) the value a variable holds is the value the programmer intended to store; (2) floating-point addition and multiplication are associative and distributive, and above all order-independent, so that the result does not depend on how the computation is arranged; (3) numbers can be compared for equality, ordered and used as dictionary keys exactly as real numbers can; and (4) a numerical result is reproducible across machines, compilers and optimisation levels. Please argue these assumptions one by one and support your argument with concrete cases in which they fail (for example: `0.1 + 0.2` not being equal to `0.3`; the sum of the same array differing between a sequential loop and a parallel reduction; catastrophic cancellation in a formula that is mathematically well conditioned; NaN making a comparison-based sort inconsistent; the truncation errors accumulated by the Patriot missile timing system and by the Vancouver Stock Exchange index).

Finally, discuss what these failures imply for the claim that "a program implementing a mathematical formula is a faithful realisation of that formula", and whether remedies such as higher precision, compensated (Kahan) summation, decimal arithmetic or interval arithmetic really remove the problem or merely relocate it.

## Question 10

**Protocol layering and "reliable" transport - Is the network really a stack of independent, transparent layers?**

Textbooks present network architecture as a stack of layers (physical → link → network → transport → application), each of which provides a service to the layer above while hiding its own implementation, and they describe TCP as offering a "reliable, ordered byte stream" that frees applications from the unreliable network underneath. This account rests on unstated assumptions: (1) each layer's service contract is complete, so that the layer above can be designed, verified and replaced without any knowledge of the layer below; (2) when the transport layer reports that data has been delivered, that data really did arrive intact and in order; (3) the intermediate network is transparent, and does not inspect, rewrite, silently drop or terminate traffic; and (4) a byte stream is a sufficient interface for every application. Critically analyse these assumptions and give concrete real-world scenarios in which they break (for example: TCP over wireless links, where loss caused by the radio is misread as congestion; bufferbloat in routers with oversized queues; NAT and other middleboxes that rewrite or terminate connections; path-MTU-discovery black holes; a connection reset discarding data that the application believed was safely queued; HTTP/2 running over TCP compared with QUIC over UDP).

Finally, explain why so many applications still implement their own checksums, timeouts and message framing on top of a "reliable" transport, and whether this redundancy is a symptom of a leaky abstraction or an inherent consequence of the end-to-end design.

## Question 11

**Characters, code points and bytes - Is a string really a sequence of characters?**

Textbooks introduce text as "a sequence of characters encoded by a character set such as ASCII or Unicode", after which string operations - measuring length, indexing, equality, ordering, case conversion, truncation and hashing - are treated as trivial. This presupposes that: (1) a character is an atomic and uniquely representable unit; (2) the number of characters, the number of code points and the number of bytes are interchangeable counts; (3) two strings denote the same text exactly when their byte sequences are identical; and (4) case conversion, collation and substring extraction are representation-independent operations. Critically examine each assumption with concrete counterexamples (for example: variable-width UTF-8 and the `char` of C being a byte rather than a character; emoji sequences and flags whose "length" is 1 grapheme, 7 code points, 11 UTF-16 units or 25 bytes; the same accented letter stored in composed and decomposed forms, and its consequences for file names shared between operating systems; Han unification giving one code point several region-specific glyphs; the Turkish dotless i and the German ß whose upper case occupies two letters; a substring cut at a byte boundary splitting a code point in half).

Finally, answer precisely: what is "one character"? Show that several equally reasonable definitions lead to mutually inconsistent answers to the questions "how long is this string?" and "are these two strings equal?", and discuss the consequences for programs that compare, sort, search, truncate or hash text.

## Question 12

**Source code, compiler and machine code - Is the program that runs the program that was written?**

Textbooks introduce the stored-program concept and the compilation pipeline (source → compiler/optimiser → assembly → machine code), and describe the compiler as translating a program into an equivalent machine-level program; the semantics of a high-level language is then often taught as if the statements were executed one at a time, exactly as written. This notion of equivalence rests on unstated assumptions: (1) every program that compiles successfully has a well-defined meaning; (2) every transformation the optimiser performs preserves the behaviour that the programmer can observe; (3) what a language standard calls "observable behaviour" covers the properties that actually matter - execution time, memory footprint, security and termination; and (4) the source text alone determines the behaviour, independently of the compiler, its flags and the target machine. Critically analyse these assumptions and support your analysis with concrete cases (for example: signed integer overflow being undefined, so that a compiler deletes the very check the programmer wrote; dead-store elimination removing the `memset` intended to wipe a cryptographic key; `-ffast-math` breaking code that tests for NaN or infinity; strict-aliasing assumptions invalidating type-punning; a spin-wait on a non-atomic flag that never re-reads memory; the same source file behaving differently at `-O0` and at `-O2`, or under two different compilers).

Finally, discuss what it means for correctness and security that a program may "work" in a debug build and its security check may silently disappear in a release build, and whether, once the optimiser is free to remove any computation whose result nobody can observe, the notion of an "equivalent program" is still well defined at all.

## Question 13

**Threads, locks and shared memory - Is a "critical section" really atomic?**

Textbooks introduce concurrency with threads, mutual-exclusion locks and the critical-section pattern, presenting a lock as the guarantee that only one thread executes the protected code at a time, and treating a concurrent program as an interleaving of the statements written in the source. This account rests on unstated assumptions: (1) the program order written by the programmer is the order observed by other threads, that is, memory operations behave as if there were a single global order (sequential consistency); (2) a lock protects every access to the data it guards, including reads, compiler-generated accesses and accesses made through aliases; (3) locks compose, so that independently correct critical sections combine into a correct program regardless of the order in which locks are acquired; and (4) the cost of synchronisation is negligible compared with the work inside the critical section. Critically analyse these assumptions and support your analysis with concrete cases (for example: store buffers and weak memory models on ARM and POWER that require explicit fences, so that a textbook Dekker or Peterson algorithm fails without them; double-checked locking that is broken without `volatile`; a spin flag that the compiler hoists out of the loop because it is not declared atomic; deadlock caused by lock-order inversion, and priority inversion in a real-time system; false sharing that makes a "parallel" program slower than its sequential version; a lock-free queue that is correct only under a memory model the language does not actually guarantee).

Finally, discuss whether a "correct concurrent program" can even be defined without first fixing a memory model, and whether the move towards message passing, actors and ownership types removes the problem or merely relocates it.

## Question 14

**File systems and durability - When a write returns, is the data really saved?**

Textbooks describe a file as a named byte stream and `read`/`write` as operations on persistent storage, so that a successful write is understood to mean that the data has been stored. This rests on unstated assumptions: (1) a write reaches stable storage before the call returns; (2) the data and the metadata of an update become visible atomically, so that a crash leaves either the old state or the new one; (3) `rename` is atomic and `fsync` orders every write issued before it, including directory entries; and (4) the storage device tells the truth - a drive that reports a completed flush has actually persisted the data. Critically analyse these assumptions with concrete cases (for example: the page cache and writeback meaning that a write followed by a power cut loses the data; ext4 delayed allocation producing a zero-length file after a crash even though the write "succeeded"; torn 512-byte sectors on 4K-native drives; the difference between `fsync` and `fdatasync`, and the need to fsync the directory after a `rename`; SSD and RAID controllers with volatile write-back caches that ignore or lie about flushes; NFS close-to-open consistency; the PostgreSQL "fsyncgate" incident, in which fsync errors were ignored and the system continued as if the data were durable).

Finally, discuss what "durable" can mean when every layer - application, operating system, controller and drive - has its own cache, and whether ordinary applications should be expected to reason about crash consistency at all.

## Question 15

**Testing, verification and "correct" software - Does passing the tests mean the program is correct?**

Textbooks present testing as the practical way to establish that a program works, with coverage criteria and a passing test suite treated as evidence of quality. This rests on unstated assumptions: (1) the test suite specifies the intended behaviour completely, so that any behaviour not tested is not required; (2) passing the tests implies the absence of bugs, or at least that the remaining bugs do not matter; (3) the oracle is correct - the expected outputs are themselves right; and (4) tests are deterministic, so that a suite which passes today will pass tomorrow on another machine. Critically analyse these assumptions with concrete cases (for example: Dijkstra's observation that testing can show the presence of bugs but never their absence; a suite with 100% line coverage and no assertions; mutation testing revealing that a "complete" suite kills only a small fraction of deliberately injected faults; the oracle problem in compilers and machine learning, where the expected output cannot be written down by hand; flaky tests and concurrency bugs that appear once a year; a test that passes only because it mocks the very component that is broken).

Finally, discuss whether the quality signal that industry actually relies on is "the tests pass" or "few defects reach production", and what an honest engineering claim about correctness would look like.

## Question 16

**Memory safety and security - Is a correct program also a safe program?**

Textbooks separate correctness (does the program do what it is specified to do) from security (does it resist an adversary), and treat buffer overflows and use-after-free as bugs that careful programmers avoid. This rests on unstated assumptions: (1) a program that produces the right output for valid inputs is correct, and invalid inputs are somebody else's problem; (2) memory errors affect only the erroneous program, and the worst outcome is a crash; (3) safety can be added at the boundary, by validating input and putting a firewall in front of the service; and (4) memory-safe languages are too slow for real systems, so unsafe languages are a necessary price. Critically analyse these assumptions with concrete cases (for example: Heartbleed, where one missing bounds check in a heartbeat handler leaked private keys from otherwise correct servers; the Morris worm and stack smashing; use-after-free and type confusion in browsers; Spectre and Meltdown, where architecturally correct execution leaks secrets through microarchitectural side channels; Rowhammer flipping bits in memory that the program never wrote; the observation that most serious vulnerabilities in large codebases are memory-safety issues, which is why memory-safe languages and C++ profiles exist).

Finally, discuss whether "safe by construction" - memory-safe languages, or formally verified kernels such as seL4 - can scale to the systems we actually build, and who should bear the cost of the unsafe code that remains.
