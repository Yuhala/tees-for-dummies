## About
>The purpose of this repo is to serve as a guide for easily setting up and testing _trusted execution environment_ (TEE) technologies like Intel SGX, Intel TDX, AMD SEV-SNP, Arm TrustZone, or GPU TEEs on Nvidia H100.

**Problem**: From my experience, it is often difficult to find practical and concise **hands on** guides on how to use most TEE technologies. Though official documentations are nice, they usually contain too much information not very necessary for a newbie willing to do a few quick tests. Moreover, it's rare to find articles that bring all of them together.
  
The purpose of this repo is to provide a central base with just enough (i.e., minimal) information and easy-to-follow guides on how to setup and do simple tests with the given TEEs on small applications. It should not be used as a reference guide for deploying your applications in production. In such situations, the official documentations are the most complete and should be followed. This repo as the name suggests, is a relatively easy starting point for "dummies".

All tutorials in this repo are Ubuntu-based. Some links may be added to redirect you to the main website if you use a different OS.
Also, if (one of) the main websites provides a "simple enough" guide, we will just redirect you there. Otherwise, we shall simplify.
- We assume you have access to the hardware and do not require techniques like remote attestation for hardware verification. Where necessary we will provide links to more extensive documentation on these aspects.  

## Background on trusted execution environments
> Trusted execution environments (TEEs) are isolated processing environments provided by the CPU to ensure confidentiality, integrity, and freshness of data and code at runtime. 
  - **Confidentiality**: ensures the data is not accessible to unauthorized entities.
  - **Integrity**: ensures the data cannot be modified/tampered with by an unauthorized entity; all tampering can be detected.
  - **Freshness**: we always have the most up-to-date version of the secured data.
  
TEEs use hardware-based mechanisms (i.e., in the CPU) for encrypting memory and enforcing strong access control mechanisms. They can be classified (broadly) into two categories: 
    1. TEEs for **process-level isolation**: they enable a process to create a secure encrypted (and integrity-protected) region, usually called an _enclave_, in its address space at runtime. Memory pages in this region can only be decrypted in the CPU. Examples include Intel software guard extensions (SGX) and Arm TrustZone (does only memory access control checks, no encryption).
   
    2. TEEs for **virtual machine (VM)-level isolation**: they protect entire VMs rather than single programs. Examples: Intel trusted domain extensions (TDX), AMD secure encrypted virtualizatin (SEV) with secure nested paging (SNP), or Arm confidential compute architecture (CCA).



## Repository structure
- There is a folder corresponding to each TEE, and each folder has a Readme file with the useful information required to deploy simple programs in the TEE.
1. [Intel SGX](sgx/README.md): deploying SGX applications using the [Intel SGX SDK](sgx/sdk-based/README.md), [Gramine LibOS](sgx/gramine-based/README.md), and [Occlum LibOS](sgx/occlum-based/README.md). 
2. [Arm TrustZone](trustzone/README.md): deploying Arm TrustZone cortex A application with [OP-TEE](https://optee.readthedocs.io/en/latest/general/about.html).
3. [Intel TDX](tdx/README.md): deploying Intel-based CVMs
4. [AMD SEV-SNP](sev-snp/README.md): deploying 
5. [Kata containers](https://katacontainers.io/): deploying confidential containers with Kata. 
    
- Most of our tests and setups use Ubuntu-based host and guest OSes.

## Author
- [Peterson Yuhala](https://yuhala.github.io/) has a PhD in computer science, with a specialization in systems security with trusted execution environments. You can find his [PhD thesis here](https://yuhala.github.io/assets/pdf/thesis-peterson-yuhala.pdf).
  

