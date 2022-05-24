## 简介
Intel提供的包含Gramine libOS基于SGX的镜像，该镜像包含了默认的SGX运行环境，安装了SGX功能所需要的软件包，包括SGX SDK，SGX PSW以及SGX DCAP等软件包。SGX是Intel推出的旨在提供硬件保护的一组扩展指令集，在支持SGX的平台中，用户程序运行在enclave中，对特权用户，OS，VMM均不可见，从而实现避免机密信息被恶意窥探或者篡改。除内存隔离与保护安全属性之外, SGX架构还支持远程认证的功能，远程认证由Quote的产生和校验来实现。由于Intel SGX 功能的实现对硬件平台有一定的要求，所以该镜像目前仅可运行在腾讯云市场的“安全增强型内存-M6ce”类型的云服务器上。用户可以通过该镜像, 借助于Gramine或者是SGX SDK , 将自己的应用部署在SGX TEE（Trusted Execution Environment）中, 也可以将该镜像作为对SGX学习了解的基础镜像。 
## 应用场景
本镜像包含了SGX driver，SGX SDK，SGX PSW，SGX DCAP和Gramine,提供了基于Gramine运行所需的基础环境, 用户可以自主选择基于Gramine或者是 Intel SGX SDK进行SGX应用程序的开发, 自主部署需要的SGX应用场景，例如pytorch，tensorflow，openjdk，sqlite等, 同时用户也可以借助helm chart,将该镜像部署到多个node上。 
## 部署
由于SGX功能的实现依赖于硬件特性,因此在部署chart文件之前,需要对于添加的node进行检查,SGX功能只存在于ICX及之后的机型中,如果node的kernel版本低于5.11,那么最好手动更换kernel版本至5.11甚至更高(否则可能需要安装很多额外的安装包),更换kernel版本的指令可参考(以5.14.0的kernel版本为例):
```
  wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.14/amd64/linux-headers-5.14.0-051400_5.14.0-051400.202108292331_all.deb 

  sudo dpkg -i linux-headers-5.14.0-051400_5.14.0-051400.202108292331_all.deb

  wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.14/amd64/linux-image-unsigned-5.14.0-051400-generic_5.14.0-051400.202108292331_amd64.deb

  wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.14/amd64/linux-modules-5.14.0-051400-generic_5.14.0-051400.202108292331_amd64.deb

  sudo dpkg -i linux-image-unsigned-5.14.0-051400-generic_5.14.0-051400.202108292331_amd64.deb linux-modules-5.14.0-051400-generic_5.14.0-051400.202108292331_amd64.deb

```
其次,由于Intel SGX对于OS的版本有要求,具体可参照链接(https://github.com/intel/linux-sgx) , 请确保您的node操作系统满足要求.

首先检查node节点下,/dev/sgx/目录下是否存在enclave和provision两个链接文件,如果没有,请使用/dev/sgx_enclave和/dev/sgx_provision手动创建链接文件,指令可参考

```

  cd /dev/sgx/

  sudo ln -s ../sgx_enclave enclave 

  sudo ln -s ../sgx_provision probision 
  
```

由于SGX功能的实现,依赖于硬件支持, 用户需要手动部署tke-sgx-plugin,从而让pod支持SGX特性,tke-sgx-plugin.yaml文件同样位于charts/incubator/目录下,可参考../incubator/tke-sgx-plugin目录下的yaml文件values.yaml.

## 测试指南

1.将helm chart部署到TKE环境中,进入运行的pod.

2.在默认账户下，已经包含了使用实例所需要的目录。 

3.进入实例 

- 目录/opt/intel/sgxsdk/SampleCode/，SampleEnclave可成功编译和运行，即Enclave可以成功产生，关于Enclave的具体描述，可参照链接https://github.com/intel/linux-sgx  
- 目录/SGXDataCenterAttestationPrimitives/SampleCode/，QuoteGeneration和QuoteVerification样例均可运行，QuoteGeneration可产生远程认证所需要的Quote，QuoteVerification则可对产生的Quote进行验证。关于Quote和远程认证的具体描述，可参照链接https://github.com/intel/SGXDataCenterAttestationPrimitives  
- 目录/gramine-1.0/LibOS/shim/test/regression下，helloworld样例可编译运行。关于gramine的其他Example的编译和运行，可能需要额外的安装包。如果用户想借助Gramine运行自定义程序，可参考链接 https://graphene.readthedocs.io/en/latest/manifest-syntax.html  

