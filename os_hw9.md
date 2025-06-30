# 操作系统第九次作业

## 问题一
在 wsl 和 hyper-v 22.04 虚拟机里面直接 `sudo apt-get install bpfcc-tools linux-headers-$(uname -r)` 都会出现报错   
简单搜了一下 解决方法是从 source rebuild   

然后 rebuild 的时候会报错大概是找不到符号的问题，怀疑还是找到了旧的 bpfcc-tools，验证为 `dpkg -S libbcc.so.0` 看到有两个，分别装在 /usr/bin 和 /usr/local 下面，所以 sudo apt remove libbpfcc 一波然后 sudo make install 之后就正常了    

![alt text](image-5.png)

## 问题三
[seccomp-tools](https://github.com/david942j/seccomp-tools) 由 bpf 实现    
以及近日对 bpf 的原理 & 攻击流程进行一些研究并写博客如下：   
https://rosayxy.github.io/ebpf-pwn-intro/    
