Installed Multipass on my device so i can run a local ubuntu VM: 

brew install --cask multipass.  ->         Uses brew to install multipass 

multipass launch 24.04 --name falco-lab --cpus 4 --memory 8G --disk 40G --> create a vm using ubuntu 24.04 called falco-lab that uses 4 cpu cores and 8gb of ram, lastly is takes 40gb of disk space

multipass list                       ->  shows the created VM´s and their specs including their ip adresses. 

multipass shell falco-lab            -> Connects to the terminal of the VM. 

Update and reboot the VM to the newest software version: 

sudo apt update && sudo apt upgrade -y
sudo reboot

Copy the git hub directory to the vm: 

multipass mount ~/Desktop/Kubernetes_project/Kubernetes_Falco_project falco-lab:/home/ubuntu/lab


Ran the following commands to make sure everything was created as expected: 
uname -m                        # Shows the architecture of the VM, ARM64 in this case 
uname -r                        # Shows the kernel version in the VM 
ls -l /sys/kernel/btf/vmlinux   # Shows if the btf file exists in the VM, needed for Falco in the future steps in the lab 
nproc                           # Shows the number of processor cores used by the VM 
free -h                         # Show the amounnt of RAM used by the VM 

Responses i got from the commands: 
Uname -m: aarch64
Uname -r: 6.8.0-142-generic

ls -l /sys/kernel/btf/vmlinux: -r--r--r-- 1 root root 6973125 Sep 25 20:18 /sys/kernel/btf/vmlinux

nproc: 4

free -h: 
               total        used        free      shared  buff/cache   available
Mem:           7.7Gi       338Mi       7.3Gi       1.1Mi       287Mi       7.4Gi
Swap:             0B          0B          0B


Installed Docker, kubectl, Helm and kind onto the VM. 

Docker: 
sudo apt install -y docker.io  #Installs the docker package from ubuntu 
sudo usermod -aG docker $USER  #Changes permissions so that docker commands dont need sudo privileges. 


Kubectl: 
sudo snap install kubectl --classic #Installs the kubectl package (classic so that the package is not sandboxed; i.e in a resticted mode and environment) 
sudo snap install helm --classic #same as over just for helm 


Kind: 
curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.33.0/kind-linux-arm64   # Downloads the ARM64 build of kind

chmod +x ./kind  #making kind a executable 

sudo mv ./kind /usr/local/bin/kind #Putting kind on the PATH

Did the following commands to make sure everything was setup as expected: 

docker run --rm hello-world      #Returns a hello world from the docker 
  Hello from Docker!
  This message shows that your installation appears to be working correctly.

kubectl version --client         #Returns the kubectl version
  Client Version: v1.35.9
  Kustomize Version: v5.7.1

helm version                     #Returns the helm version 
  version.BuildInfo{Version:"v4.3.0", GitCommit:"bec5b06ed841fe5269972d864d5177944fd5970f", GitTreeState:"clean", GoVersion:"go1.27.1", KubeClientVersion:"v1.37"}

kind version                     #Returns the kind version
  kind v0.33.0 go1.26.7 linux/arm64


Installed strace on the VM to be able to see syscalls live: 

sudo apt install -y strace #To install the package onto the VM 

strace -f -e trace=execve,openat,connect curl -s https://example.com -o /dev/null #Trying to curl a fake website to see what syscalls are made in the process. 

returned a big list of syscalls: 
      execve("/usr/bin/curl", ["curl", "-s", "https://example.com", "-o", "/dev/null"], 0xffffc2124ba8 /* 22 vars */) = 0
      openat(AT_FDCWD, "/etc/ld.so.cache", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libcurl.so.4", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libz.so.1", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libc.so.6", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libnghttp2.so.14", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libidn2.so.0", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/librtmp.so.1", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libssh.so.4", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libpsl.so.5", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libssl.so.3", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libcrypto.so.3", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libgssapi_krb5.so.2", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libldap.so.2", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/liblber.so.2", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libzstd.so.1", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libbrotlidec.so.1", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libunistring.so.5", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libgnutls.so.30", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libhogweed.so.6", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libnettle.so.8", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libgmp.so.10", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libkrb5.so.3", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libk5crypto.so.3", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libcom_err.so.2", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libkrb5support.so.0", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libsasl2.so.2", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libbrotlicommon.so.1", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libp11-kit.so.0", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libtasn1.so.6", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libkeyutils.so.1", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libresolv.so.2", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/lib/aarch64-linux-gnu/libffi.so.8", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/etc/gnutls/config", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/proc/sys/crypto/fips_enabled", O_RDONLY) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/ssl/openssl.cnf", O_RDONLY) = 3
      openat(AT_FDCWD, "/usr/lib/locale/locale-archive", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/share/locale/locale.alias", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_IDENTIFICATION", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_IDENTIFICATION", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/aarch64-linux-gnu/gconv/gconv-modules.cache", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_MEASUREMENT", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_MEASUREMENT", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_TELEPHONE", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_TELEPHONE", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_ADDRESS", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_ADDRESS", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_NAME", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_NAME", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_PAPER", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_PAPER", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_MESSAGES", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_MESSAGES", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_MESSAGES/SYS_LC_MESSAGES", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_MONETARY", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_MONETARY", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_COLLATE", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_COLLATE", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_TIME", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_TIME", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_NUMERIC", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_NUMERIC", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/usr/lib/locale/C.UTF-8/LC_CTYPE", O_RDONLY|O_CLOEXEC) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/usr/lib/locale/C.utf8/LC_CTYPE", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/home/ubuntu/.curlrc", O_RDONLY) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/home/ubuntu/.config/curlrc", O_RDONLY) = -1 ENOENT (No such file or directory)
      connect(3, {sa_family=AF_UNIX, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
      connect(3, {sa_family=AF_UNIX, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
      openat(AT_FDCWD, "/etc/nsswitch.conf", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/etc/passwd", O_RDONLY|O_CLOEXEC) = 3
      openat(AT_FDCWD, "/home/ubuntu/.curlrc", O_RDONLY) = -1 ENOENT (No such file or directory)
      strace: Process 2646 attached
      [pid  2646] connect(7, {sa_family=AF_UNIX, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
      [pid  2646] connect(7, {sa_family=AF_UNIX, sun_path="/var/run/nscd/socket"}, 110) = -1 ENOENT (No such file or directory)
      [pid  2646] openat(AT_FDCWD, "/etc/host.conf", O_RDONLY|O_CLOEXEC) = 7
      [pid  2646] openat(AT_FDCWD, "/etc/resolv.conf", O_RDONLY|O_CLOEXEC) = 7
      [pid  2646] openat(AT_FDCWD, "/etc/hosts", O_RDONLY|O_CLOEXEC) = 7
      [pid  2646] connect(7, {sa_family=AF_INET, sin_port=htons(53), sin_addr=inet_addr("127.0.0.53")}, 16) = 0
      [pid  2646] openat(AT_FDCWD, "/etc/gai.conf", O_RDONLY|O_CLOEXEC) = 7
      [pid  2646] connect(7, {sa_family=AF_INET, sin_port=htons(443), sin_addr=inet_addr("104.20.23.154")}, 16) = 0
      [pid  2646] connect(7, {sa_family=AF_UNSPEC, sa_data="\0\0\0\0\0\0\0\0\0\0\0\0\0\0"}, 16) = 0
      [pid  2646] connect(7, {sa_family=AF_INET, sin_port=htons(443), sin_addr=inet_addr("172.66.147.243")}, 16) = 0
      [pid  2646] connect(7, {sa_family=AF_INET6, sin6_port=htons(443), sin6_flowinfo=htonl(0), inet_pton(AF_INET6, "2606:4700:10::6814:179a", &sin6_addr), sin6_scope_id=0}, 28) = 0
      [pid  2646] connect(7, {sa_family=AF_UNSPEC, sa_data="\0\0\0\0\0\0\0\0\0\0\0\0\0\0"}, 16) = 0
      [pid  2646] connect(7, {sa_family=AF_INET6, sin6_port=htons(443), sin6_flowinfo=htonl(0), inet_pton(AF_INET6, "2606:4700:10::ac42:93f3", &sin6_addr), sin6_scope_id=0}, 28) = 0
      [pid  2646] +++ exited with 0 +++
      connect(5, {sa_family=AF_INET, sin_port=htons(443), sin_addr=inet_addr("104.20.23.154")}, 16) = -1 EINPROGRESS (Operation now in progress)
      openat(AT_FDCWD, "/etc/ssl/certs/ca-certificates.crt", O_RDONLY) = 6
      openat(AT_FDCWD, "/etc/localtime", O_RDONLY|O_CLOEXEC) = 6
      openat(AT_FDCWD, "/dev/null", O_WRONLY|O_CREAT|O_TRUNC, 0666) = 6
      +++ exited with 0 +++

  IMPORTANT: strace is slow because it stops the program at every syscall to record it. Falco on the other hand uses eBPF instead which is fast enough so it can run all the time without stoping the      program. 



  Created a single node cluster using the following command: 
  kind create cluster --name lab

  From there used these commands to explore and understand what had been created: 

  kubectl cluster-info # where the API server is
  
  kubectl get nodes -o wide # your single node and its details
  
  kubectl get pods -A # pods in all namespaces: the
  
  control plane's own pods in kube-system
  
  docker ps # the "node" is just a container in the VM
  
  docker exec lab-control-plane crictl ps # the containers running *inside*
  that node container


  From there i did a check to see for myself that the VM kernel, kind node´s kernel and the pods kernel are the same. 

    uname -r
    6.8.0-142-generic

  
    docker exec lab-control-plane uname -r
    6.8.0-142-generic
    
    kubectl run tmp --rm -it --restart=Never --image=alpine -- uname -r
    All commands and output from this session will be recorded in container logs, including credentials and sensitive information passed through the command prompt.
    If you don't see a command prompt, try pressing enter.
    warning: couldn't attach to pod/tmp, falling back to streaming logs: unable to upgrade connection: container tmp not found in pod tmp_default
    6.8.0-142-generic
    pod "tmp" deleted from default namespace



  Setup falco, and from there checked its startup log, which printed out the following: 
  
    ubuntu@falco-lab:~/lab$ kubectl logs -n falco -l app.kubernetes.io/name=falco -c falco --tail=50
    Fri Oct 30 11:46:52 2026: Falco version: 0.45.2 (aarch64)
    Fri Oct 30 11:46:52 2026: Falco initialized with configuration files:
    Fri Oct 30 11:46:52 2026:    /etc/falco/config.d/engine-kind-falcoctl.yaml | schema validation: ok
    Fri Oct 30 11:46:52 2026:    /etc/falco/falco.yaml | schema validation: ok
    Fri Oct 30 11:46:52 2026: System info: Linux version 6.8.0-142-generic (buildd@bos03-arm64-111) (aarch64-linux-gnu-gcc-13 (Ubuntu 13.3.0-6ubuntu2~24.04.1) 13.3.0, GNU ld (GNU Binutils for Ubuntu) 2.42) #142-Ubuntu SMP PREEMPT_DYNAMIC Wed Sep  2 14:30:29 UTC 2026
    Fri Oct 30 11:46:52 2026: Loaded plugin 'container@0.7.6' from file /usr/share/falco/plugins/libcontainer.so
    Fri Oct 30 11:46:52 2026: [libs]: container: Enabled 'podman' container engine.
    Fri Oct 30 11:46:52 2026: [libs]: container: * enabled container runtime socket at '/host/run/podman/podman.sock'
    Fri Oct 30 11:46:52 2026: [libs]: container: Enabled 'docker' container engine.
    Fri Oct 30 11:46:52 2026: [libs]: container: * enabled container runtime socket at '/host/var/run/docker.sock'
    Fri Oct 30 11:46:52 2026: [libs]: container: Enabled 'cri' container engine.
    Fri Oct 30 11:46:52 2026: [libs]: container: * enabled container runtime socket at '/host/run/containerd/containerd.sock'
    Fri Oct 30 11:46:52 2026: [libs]: container: * enabled container runtime socket at '/host/run/crio/crio.sock'
    Fri Oct 30 11:46:52 2026: [libs]: container: * enabled container runtime socket at '/host/run/k3s/containerd/containerd.sock'
    Fri Oct 30 11:46:52 2026: [libs]: container: * enabled container runtime socket at '/host/run/host-containerd/containerd.sock'
    Fri Oct 30 11:46:52 2026: [libs]: container: Enabled 'containerd' container engine.
    Fri Oct 30 11:46:52 2026: [libs]: container: * enabled container runtime socket at '/host/run/host-containerd/containerd.sock'
    Fri Oct 30 11:46:52 2026: [libs]: container: Enabled 'lxc' container engine.
    Fri Oct 30 11:46:52 2026: [libs]: container: Enabled 'libvirt_lxc' container engine.
    Fri Oct 30 11:46:52 2026: [libs]: container: Enabled 'bpm' container engine.
    Fri Oct 30 11:46:52 2026: Loading rules from:
    Fri Oct 30 11:46:52 2026:    /etc/falco/falco_rules.yaml | schema validation: ok
    Fri Oct 30 11:46:52 2026: Hostname value has been overridden via environment variable to: lab-control-plane
    Fri Oct 30 11:46:52 2026: The chosen syscall buffer dimension is: 8388608 bytes (8 MBs)
    Fri Oct 30 11:46:52 2026: Starting health webserver with threadiness 4, listening on 0.0.0.0:8765
    Fri Oct 30 11:46:52 2026: Loaded event sources: syscall
    Fri Oct 30 11:46:52 2026: Enabled event sources: syscall
    Fri Oct 30 11:46:52 2026: Opening 'syscall' source with modern BPF probe.
    Fri Oct 30 11:46:52 2026: One ring buffer every '2' CPUs.
    Fri Oct 30 11:46:52 2026: [libs]: Trying to open the right engine!
    Fri Oct 30 11:46:52 2026: [libs]: libpman: disabled BPF iterators (not running in the root PID namespace, or failed to determine it)
    Fri Oct 30 11:46:53 2026: [libs]: libbpf: failed to determine tracepoint 'syscalls/sys_enter_creat' perf event ID: No such file or directory
    Fri Oct 30 11:46:53 2026: [libs]: libbpf: prog 'creat_e': failed to create tracepoint 'syscalls/sys_enter_creat' perf event: No such file or directory
    Fri Oct 30 11:46:53 2026: [libs]: libpman: failure while attaching TOCTOU mitigation program for 'creat' system call. Detection will continue to work, but TOCTOU mitigation may not properly work (errno: 2 | message: No such file or directory)
    Fri Oct 30 11:46:53 2026: [libs]: libbpf: failed to determine tracepoint 'syscalls/sys_enter_open' perf event ID: No such file or directory
    Fri Oct 30 11:46:53 2026: [libs]: libbpf: prog 'open_e': failed to create tracepoint 'syscalls/sys_enter_open' perf event: No such file or directory
    Fri Oct 30 11:46:53 2026: [libs]: libpman: failure while attaching TOCTOU mitigation program for 'open' system call. Detection will continue to work, but TOCTOU mitigation may not properly work (errno: 2 | message: No such file or directory)

1. Falco runs in a different pod from nginx. How did it see your shell? (Part 0 and the
uname -r experiment.)
  Even though Falco was run on a different shell, it could still see nginx due to them both using the same kernel. This is due to all the pods sharing one kernel. 


2. In the Terminal shell alert, what is the parent of sh? Trace the path of kubectl exec :
kubectl → API server → kubelet → containerd → your shell.
 In the terminal shell alert the parent of sh is listed as: "parent=containerd-shim". When running the kubectl exec command the kubectl tool sends a request to the API server. The API server then checks if you are allowed to exec into the pod, if yes it finds the pod and sends the request. From there the kubelet takes the exec command request and then forwards it to the containerd that then forwards that to the containerd-shim that finally starts the sh process in the container. So the reason that the parent of sh i containerd-shim and not kubectl is because kubectl only sends the network request, whilst containerd-shim actually completes the request. 

3. Run kubectl exec deploy/nginx -- cat /etc/shadow — without -it and without a
shell. Which of the two rules fires now, and why? (Hint: look at proc.tty and
shell_procs in the rule conditions.)
  Only the "Warning Sensitive file opened for reading by non-trusted program" is triggered because a file was read with a program that is considered not-trusted (in this case "cat"). Since there is no shell that was spawned (no shell_procs), furthermore since no terminal was allocated ("terminal" field i.e proc.tty field was 0)(the lack of -t in the command) the two conditions for the "Notice A shell was spawned in a container with an attached terminal" alert was not shown. 














  







