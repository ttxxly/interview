### 概念

#### Docker与虚拟机的区别？

```text
1、虚拟机：我们传统的虚拟机需要模拟整台机器包括硬件，虚拟机⼀旦被开启，预分配给他的资源将全部被占⽤。
2、Docker：容器技术是和我们的宿主机共享硬件资源及操作系统可以实现资源的动态分配。

```

![](https://secure.wostatic.cn/static/bFRNvE8RCVUQMzdDVHNFhw/v2-c2a31e2008835b2974170ad1dbac0d42_720w.jpg)



### 具体使用

#### 同⼀个宿主机中多个Docker容器之间如何通信？多个宿主机中Docker容器之间如何通信？

```text
1、这⾥同主机不同容器之间通信主要使⽤Docker桥接（Bridge）模式。
2、不同主机的容器之间的通信可以借助于 pipework 这个⼯具。
```

