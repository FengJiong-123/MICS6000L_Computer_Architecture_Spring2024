# The gem5 Simulator

This is the repository for the gem5 simulator. It contains the full source code
for the simulator and all tests and regressions.

The gem5 simulator is a modular platform for computer-system architecture
research, encompassing system-level architecture as well as processor
microarchitecture. It is primarily used to evaluate new hardware designs,
system software changes, and compile-time and run-time system optimizations.

The main website can be found at <http://www.gem5.org>.


## Building gem5

To build gem5, you will need the following software: g++ or clang,
Python (gem5 links in the Python interpreter), SCons, zlib, m4, and lastly
protobuf if you want trace capture and playback support. Please see
<http://www.gem5.org/documentation/general_docs/building> for more details
concerning the minimum versions of these tools.

Here we just simply build the gem5 on X86 Arch for your programming exercise.
The command is: \
scons build/X86/gem5.opt -j 16

This progress is really time-consuming if it's the first compilation.

## The Source Tree

The main source tree includes these subdirectories:

* build_opts: pre-made default configurations for gem5
* build_tools: tools used internally by gem5's build process.
* configs: example simulation configuration scripts
* ext: less-common external packages needed to build gem5
* include: include files for use in other programs
* site_scons: modular components of the build system
* src: source code of the gem5 simulator. The C++ source, Python wrappers, and Python standard library are found in this directory.
* system: source for some optional system software for simulated systems
* tests: regression tests
* util: useful utility programs and files

## gem5 Resources

To run full-system simulations, you may need compiled system firmware, kernel
binaries and one or more disk images, depending on gem5's configuration and
what type of workload you're trying to run. Many of these resources can be
obtained from <https://resources.gem5.org>.

More information on gem5 Resources can be found at
<https://www.gem5.org/documentation/general_docs/gem5_resources/>.

## Prepare the test case

cp MICS6000_CA_test_cases.tar.gz /where/is/your/gem5/tests/test-progs/ \
cd /where/is/your/gem5/tests/test-progs/ \
tar -xvf MICS6000_CA_test_cases.tar.gz \
cd -

## Run Simple Example

build/X86/gem5.opt -d bw_mem \ 
configs/deprecated/example/se.py \
--cmd tests/test-progs/bwmem/threads --options="1M 1 1 rd" \
--mem-size=4GB --cpu-type=O3CPU --caches \
--l2cache --l1d_size=32kB --l1i_size=32kB --l2_size=512kB \

* The basic binary of gem5 is gem5.opt. -d means the output directory of gem5. The default directory is m5out.
  You can obtain the stats.txt and debug info(if you have announced) in this directory.
* Gem5 will use python to connect each sub-module basically. se.py is the System Emulation. 
  It only calls the Core and Memory system of gem5 without IO
  and thus avoid their influence. It is a fast way to achieve the result of simulator.
* --cmd is the running binary that you should provide for gem5. This example is a micro-bench
  named lmbench. This binary is to test the bandwidth of CPU generally. The option '1M' means 
  the test length. '1 1' means test cores are 1 and loops are 1. 'rd' means read test. bwmem
  also supports write, bcopy test and so on.
* The remaining part of commad is the configures for gem5. The O3CPU means that the CPU type 
  we use is 'Out-Of-Order'.

## Run Spec2017

* perlbench: \
cd tests/test-progs/perlbench \
../../../build/X86/gem5.opt -d ../../../perlbench \
../../../configs/deprecated/example/se.py \
--cmd ./perlbench_r  --options="-I./lib checkspam.pl 2500 5 25 11 150 1 1 1 1" \
--mem-size=4GB --cpu-type=O3CPU --caches --l2cache --l1d_size=32kB --l1i_size=32kB --l2_size=512kB \
--restore-simpoint-checkpoint --checkpoint-dir=perl_r_1_init/ -r 12 \
cd -

* mcf: \
build/X86/gem5.opt -d mcf \
configs/deprecated/example/se.py \
--cmd tests/test-progs/mcf/mcf_r \
--options="tests/test-progs/mcf/inp.in" \
--mem-size=4GB --cpu-type=O3CPU --caches --l2cache --l1d_size=32kB --l1i_size=32kB --l2_size=512kB \
--restore-simpoint-checkpoint --checkpoint-dir=tests/test-progs/mcf/mcf_r_init/ -r 8 \

* omnetpp: \
cd tests/test-progs/omnetpp \
../../../build/X86/gem5.opt -d ../../../omnetpp \
../../../configs/deprecated/example/se.py \
--cmd ./omnetpp_r  --options="-c General -r 0" \
--mem-size=4GB --cpu-type=O3CPU --caches --l2cache --l1d_size=32kB --l1i_size=32kB --l2_size=512kB \
--restore-simpoint-checkpoint --checkpoint-dir=omp_r_init/ -r 7 \
cd -

* deepsjeng: \
cd tests/test-progs/deepsjeng \
../../../build/X86/gem5.opt -d ../../../deepsjeng \
../../../configs/deprecated/example/se.py \
--cmd ./deepsjeng_r  --options="ref.txt" \
--mem-size=4GB --cpu-type=O3CPU --caches --l2cache --l1d_size=32kB --l1i_size=32kB --l2_size=512kB \
--restore-simpoint-checkpoint --checkpoint-dir=deep_r_init/ -r 3 \
cd -

* named: \
build/X86/gem5.opt -d named \
configs/deprecated/example/se.py \
--cmd tests/test-progs/named/namd_r \
--options="--input tests/test-progs/named/apoa1.input --output tests/test-progs/named/apoa1.ref.output --iterations 65" \
--mem-size=4GB --cpu-type=DerivO3CPU --caches --l2cache --l1d_size=32kB --l1i_size=32kB --l2_size=512kB \
--restore-simpoint-checkpoint --checkpoint-dir=tests/test-progs/named/nam_r_init/ -r 13
