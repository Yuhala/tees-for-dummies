## Intel trusted domain extensions (TDX)
> As opposed to TEEs like SGX and TrustZone which protect a single program at runtime, TDX protects an entire VM. In TDX, a protected VM is called a _trust domain_ (TD).
The VM's memory is transparently encrypted/decrypted using multi-key Total Memory Encryption (MKTME) at cacheline granularity within the CPU. Each VM is associated to a unique key which cannot be accessed by the hypervisor or other VMs. A central component called the *TDX Module* sits between TDs and the hypervisor and handles access control, TD context switches, etc.
  
## Setup 
- To configure and enable TDX on your server, follow this [Readme from Canonical](https://github.com/canonical/tdx/blob/3.3/README.md)
- Confirgure network with `sudo virsh net-start default`
- In the linked Readme above, if the TDX setup has already been done (steps 1 to 4) on your server, you can skip them and simply run a TDX VM from [step 5](https://github.com/canonical/tdx/tree/3.3?tab=readme-ov-file#5-create-td-image)

- Once your TDX-enabled VM has been deployed, you can connect to it via SSH, using an instruction like:
```bash
ssh -p 10022 tdx@localhost # default password is 123456
```

- See [bench](./bench.md) for ideas on benchmarking.

## More resources
- [Confidential VMs explained: An Empirical Analysis of AMD SEV-SNP and Intel TDX](https://dl.acm.org/doi/pdf/10.1145/3700418)
- [Intel TDX Demystified: A Top-Down Approach](https://arxiv.org/pdf/2303.15540)
