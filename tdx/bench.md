## Benchmarking with UnixBench
- Install CPU and memory stress benchmark
```bash
git clone https://github.com/kdlucas/byte-unixbench.git
sudo apt update
sudo apt install build-essential libgcc-12-dev
```
- Evaluate system call
```bash
cd byte-unixbench/UnixBench
./Run syscall
```
- Example results on TDX VM
```bash 
System Benchmarks Partial Index              BASELINE       RESULT    INDEX
System Call Overhead                          15000.0   18750490.3  12500.3
```
- The "baseline" or reference system is a `SPARCstation 20-61`. This shows system calls were `12500` times faster in TDX.
- Corresponding results on regular VM (no TDX) with 32 CPUs
```bash
System Benchmarks Partial Index              BASELINE       RESULT    INDEX
System Call Overhead                          15000.0   18801524.5  12534.3
```
- Results slightly better on non TDX VM; TDX introduces overhead for some syscalls.
- Corresponding results on non virtualized (bare metal) system
```bash
System Benchmarks Partial Index              BASELINE       RESULT    INDEX
System Call Overhead                          15000.0   19818118.3  13212.1
```
- We have a higher result here wrt. the TDX VM and regular VM, which is expected.
