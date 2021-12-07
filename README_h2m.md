cd openmp
mkdir BUILD
cd BUILD
cmake -DCMAKE_INSTALL_PREFIX=install -DCMAKE_BUILD_TYPE=Release ..
ccmake ..
<disable omptarget stuff>
<configure>
<generate>
make
make install
export LLVM=/path/to/llvm/install

if you clang (11 here) uses libomp.so.5, go to $LLVM/lib and symlibk libomp.so.5 to libomp.so

use example_allocator.c from https://gitlab.inria.fr/h2m/openmp-memory-management/-/tree/master/src/allocator_tests
modify it to use omp_high_bw_mem_alloc or so
compile with something like
C_INCLUDE_PATH=$LLVM/include CPLUS_INCLUDE_PATH=$LLVM/include CPATH=$LLVM/include INCLUDE=$LLVM/include \
LIBRARY_PATH=$LLVM/lib LD_LIBRARY_PATH=$LLVM/lib \
clang -fopenmp example_allocator.c -o example_allocator -DUSE_ONY_DEFAULT=1

run with
LD_LIBRARY_PATH=$LLVM/lib/ ./example_allocator

if you don't have HMS and/or bandwidth info:
lstopo foo.xml
hwloc-annotate foo.xml foo.xml -- node:0 -- memattr bandwidth 0xf 1
export HWLOC_XMLFILE=foo.xml
