# ergastirio-arxitektoniki
στεργης νικολαος


Question 1.

CPU type: atomic,minor,hpi

Frequency: 1GHz

Components Voltage: 3.3V

CPU voltage:1,2V

cache: fixed line sized 64 bytes, private L1 caches and a shared L2 cache (using minorCPU)

CPU frequency: 4 GHz (default)

Number of cores:1 by default

Number of memory ranks per channel:2GB by default

Memory: 2GB by default

We can change the operation frequency by searching for the command:

self.clk_domain = SrcClockDomain(clock="1GHz",voltage_domain=self.voltage_domain)

and changing "1GHz" to some other value

    or

change the command

parser.add_argument("--cpu-freq", type=str, default="4GHz")

Question 2.

sim_seconds: Number of seconds simulated

sim_ints: Number of executed instructions during simulation (commited by the CPU)

host_inst_rate: Number of instructions that would be executed per second (performance of gem5)
Question 3.

total instructions: 5028

system.cpu_cluster.l2.overall_misses::.cpu_cluster.cpus.inst: 332
( number of overall misses )

system.cpu_cluster.cpus.dcache.overall_misses::total: 179
( number of overall misses )

system.cpu_cluster.cpus.icache.overall_misses::total: 332
( number of overall misses )
Question 4.

Some in-order CPUs used by gem5 are:

AtomicSimpleCPU

The AtomicSimpleCPU is the version of SimpleCPU that uses atomic memory accesses. It uses the latency estimates from the atomic accesses to estimate overall cache access time. The AtomicSimpleCPU is derived from BaseSimpleCPU, and implements functions to read and write memory, and also to tick, which defines what happens every CPU cycle. It defines the port that is used to hook up to memory, and connects the CPU to the cache.

TimingSimpleCPU

The TimingSimpleCPU is the version of SimpleCPU that uses timing memory accesses. It stalls on cache accesses and waits for the memory system to respond prior to proceeding. Like the AtomicSimpleCPU, the TimingSimpleCPU is also derived from BaseSimpleCPU, and implements the same set of functions. It defines the port that is used to hook up to memory, and connects the CPU to the cache. It also defines the necessary functions for handling the response from memory to the accesses sent out

MinorCPU

Minor is an in-order processor model with a fixed pipeline but configurable data structures and execute behaviour. It is intended to be used to model processors with strict in-order execution behaviour and allows visualisation of an instruction’s position in the pipeline through the MinorTrace/minorview.py format/tool. The intention is to provide a framework for micro-architecturally correlating the model with a particular, chosen processor with similar capabilities.The model isn't currently capable of multithreading but there are THREAD comments in key places where stage data needs to be arrayed to support multithreading.MinorCPU has a fixed four-stage in-order execution pipeline, while it has adjustable data structures and execution behavior. It can therefore be configured at the micro-architecture level to model a specific processor. The four-stage pipeline includes fetching lines, decomposition into macro-ops, decomposition of macro-ops into micro-ops and execute.

HPI (High-Performance In-order) CPU

This model is based on the Arm architecture and we call it HPI. The HPI CPU timing model is configured to represent a modern in-order Armv8-A application. The HPI CPU pipeline uses the same four-stage model as the MinorCPU mentioned earlier.
a)

First,we simulate our program without changing any parameters:

        ./build/ARM/gem5.opt -d program_result_minor configs/example/se.py --cpu-type=MinorCPU --caches -c program_arm

MinorCPU: sim_seconds 0.000036   # Number of seconds simulated from folder stats_minor.

        ./build/ARM/gem5.opt -d program_result_timingsimple configs/example/se.py --cpu type=TimingSimpleCPU --caches -c program_arm

TimingSimpleCPU: sim_seconds 0.000043   # Number of seconds simulated from folder stats_timingsimple

We observe that MinorCPU has a significantly less execution time.
b)

        ./build/ARM/gem5.opt -d program_result_minor_500 configs/example/se.py --cpu-type=MinorCPU --sys-clock=500000000 --caches -c program_arm

MinorCPU: sim_seconds 0.000043   # Number of seconds simulated from folder stats_minor_500

        ./build/ARM/gem5.opt -d program_result_timingsimple_500 configs/example/se.py --cpu-type=TimingSimpleCPU --sys-clock=500000000 --caches -c program_arm

TimingSimpleCPU: sim_seconds 0.000049   # Number of seconds simulated from folder timingsimple

Our observation is that execution time increased in both CPUs, something we anticipated since we decreased the frequency.

Finally we will change our memory's technology to DDR3_2133_8x8 using the command:

        ./build/ARM/gem5.opt -d program_result_minor_DDR3 configs/example/se.py --cpu-type=MinorCPU --mem-type=DDR3_2133_8x8 --caches -c program_arm

MinorCPU: sim_seconds 0.000035   # Number of seconds simulated from folder minor

        ./build/ARM/gem5.opt -d program_result_timingsimple_DDR3 configs/example/se.py --cpu-type=TimingSimpleCPU --mem-type=DDR3_2133_8x8 --caches -c program_arm

TimingSimpleCPU: sim_seconds 0.000042   # Number of seconds simulated from folder timingsimple

We are reaching to the conclusion that our time decreased in both cases. That was expected since we originally used DDR3_1600_8x8 (1.6 x 8 x 8 / 8 = 12.8GBps) and then we used DDR3_2133_8x8 (2.133 x 8 x 8 / 8 = 17.0 GBps). We observe that time decrease is almost insignificant, but that could be justified by the simplicity of our C program.


Bibliography

        https://www.gem5.org/documentation/general_docs/cpu_models/SimpleCPU
        https://www.gem5.org/documentation/general_docs/cpu_models/minor_cpu
        http://learning.gem5.org/book/part1/example_configs.html
        http://learning.gem5.org/book/part1/gem5_stats.html
