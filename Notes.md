Installed Multipass on my device so i can run a local ubuntu VM: 

brew install --cask multipass        # Uses brew to install multipass 
multipass launch 24.04 --name falco-lab --cpus 4 --memory 8G --disk 40G #create a vm using ubuntu 24.04 called falco-lab that uses 4 cpu cores and 8gb of ram, lastly is takes 40gb of disk space
multipass list                       # shows the created VM´s and their specs including their ip adresses. 
multipass shell falco-lab            # Connects to the terminal of the VM. 

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
  








