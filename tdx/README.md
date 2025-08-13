## Intel trusted domain extensions (TDX)
- todo: add diagram
- As opposed to TEEs like SGX and TrustZone which protect a single program at runtime, TDX protects an entire VM. In TDX, a protected VM is called a _trust domains_ (TD).
- The VM's memory is transparently encrypted/decrypted using multi-key Total Memory Encryption (MKTME) at cacheline granularity within the CPU.
- Each VM is associated to a unique key which cannot be accessed by the hypervisor or other VMs.
- A central component called the *TDX Module* sits between TDs and the hypervisor and handles access control, TD context switches, etc.
  
## Setup 
- To configure and enable TDX on your server, follow this [Readme from Canonical](https://github.com/canonical/tdx/blob/3.3/README.md)
- Confirgure network with `sudo virsh net-start default`
- In the linked Readme above, if the TDX setup has already been done (steps 1 to 4) on your server, you can skip them and simply run a TDX VM from [step 5](https://github.com/canonical/tdx/tree/3.3?tab=readme-ov-file#5-create-td-image)

- Once your TDX-enabled VM has been deployed, you can connect to it via SSH, using an instruction like:
```bash
ssh -p 10022 tdx@localhost # default password is 123456
```

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

## More resources
- [Confidential VMs explained: An Empirical Analysis of AMD SEV-SNP and Intel TDX](https://dl.acm.org/doi/pdf/10.1145/3700418)
- [Intel TDX Demystified: A Top-Down Approach](https://arxiv.org/pdf/2303.15540)
