## Network I/O benchmarking
> A common use case for confidential VMs is deploying secure web servers, e.g., `Nginx`. To that effect, one could evaluate the overhead that a TEE like TDX introduces.

For our network benchmarking, we will `wrk`, a popular HTTP benchmarking tool, which can be used to generate and send requests to a server (e.g., Nginx in a TDX VM)
To fully appreciate the cost of TDX, we will evaluate three scenarios:
1. `wrk` on the host OS sending requests to `Nginx` running on the same host OS.
2. `wrk` on the host OS sending requests to `Nginx` running in a TDX VM.
3. `wrk` on the host OS sending requests to `Nginx` running on a regular (non TDX VM).


- Setup and build `wrk` as follows. 
```bash
# 1. install openssl 
sudo apt update
sudo apt install openssl net-tools libssl-dev unzip -y
# 2. Download and build wrk
git clone https://github.com/wg/wrk.git && cd wrk
make 
```
### Nginx and wrk on the same host OS
- Setup an HTTP server on the host to listen for connections
```bash
sudo apt install nginx -y
sudo systemctl start nginx
```
- If `nginx` port already in use, modify config in `/etc/nginx/sites-available/default` to use something different, e.g., `8080`
```bash
listen 8080 default_server;
listen [::]:8080 default_server;
```
- Test the config with `sudo nginx -t` and start with `sudo systemctl start nginx`. This should make `nginx` reachable from `http://127.0.0.1:8080`.
- Send requests using wrk.
```bash
./wrk -t4 -c100 -d30s http://127.0.0.1:8080/
```
The above command runs wrk which uses 4 threads to generate connections independently towards the target URL. The total test duration is 30s.
The results generated are: requests per second, latency stats, total requests sent. 
- Below are the corresponding results; we will focus on the requests throughput which is `162588.64 Requests/sec` here.
```bash
Running 30s test @ http://127.0.0.1:8080/
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency   646.74us    0.95ms  27.51ms   92.63%
    Req/Sec    40.85k     5.43k   70.05k    68.92%
  4879138 requests in 30.01s, 49.63GB read
Requests/sec: 162588.64
Transfer/sec:      1.65GB
```

### Nginx in TDX VM and wrk on host OS
- Create and login into a standard VM as described [here](README.md). Install and setup the Nginx server similar to the host setup. My VM's IP is: `192.168.122.100` and is running nginx on port 8080. Similarly, the host I launch wrk as follows.
```bash
./wrk -t4 -c100 -d30s http://192.168.122.100:8080/
```
- To verify that the VM is actually receiving `wrk` requests, check the Nginx access logs
```bash
sudo tail -f /var/log/nginx/access.log
```
The above should show many entries from `wrk` requests, for example.
```bash
192.168.122.1 - - [20/Aug/2025:10:11:55 +0000] "GET / HTTP/1.1" 200 615 "-" "-"
192.168.122.1 - - [20/Aug/2025:10:11:55 +0000] "GET / HTTP/1.1" 200 615 "-" "-"
...
```
My host machine has IP `192.168.122.1` so the TDX VM is indeed receiving the `wrk` requests.

 The results from the `wrk` run are as follows.
```bash
Running 30s test @ http://192.168.122.100:8080/
  4 threads and 100 connections
  Thread Stats   Avg      Stdev     Max   +/- Stdev
    Latency     3.22ms    2.71ms  32.92ms   78.31%
    Req/Sec     8.78k     1.14k   14.80k    71.33%
  1049350 requests in 30.02s, 862.63MB read
Requests/sec:  34951.73
Transfer/sec:     28.73MB
```

### Nginx in non TDX VM and wrk on host OS
- Create and login into a standard VM as described [here](standard_vm.md), and setup the Nginx server similar to the host setup. My VM's IP is: `192.168.122.100` and is running nginx on port 8080. Similarly, I launch wrk as follows.
- TODO.



















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
