## 简介
intel SGX (Software Guard Extensions) 是Intel 推出的一种基于CPU 硬件的安全保障机制，能够不依赖于固件和系统软件的安全状态，提供用户空间的可信执行环境，通过一组新的指令集扩展与访问控制机制，实现不同程序间的隔离运行，保障用户关键代码和数据的机密性与完整性不受黑客的攻击和恶意软件的破坏。

Gramine&SDK-chart-on-tke是由Intel提供的包含Gramine libOS基于SGX的helm chart，方便用户大规模的部署,该镜像包含了默认的SGX运行环境，安装了SGX功能所需要的软件包，包括SGX SDK，SGX PSW以及SGX DCAP等软件包。
## 部署
由于SGX功能的实现依赖于硬件特性,因此在部署chart文件之前,需要对于添加的node进行检查,SGX功能只存在于ICX及之后的机型中,如果node的kernel版本低于5.11,那么最好手动更换kernel版本至5.11甚至更高(否则可能需要安装很多额外的安装包).

其次,由于Intel SGX对于OS的版本有要求,具体可参照链接(https://github.com/intel/linux-sgx) , 请确保您的node操作系统满足要求.

首先检查node节点下,/dev/sgx/目录下是否存在enclave和provision两个链接文件,如果没有,请使用/dev/sgx_enclave和/dev/sgx_provision手动创建链接文件,指令可参考

cd /dev/sgx/

sudo ln -s ../sgx_enclave enclave 

sudo ln -s ../sgx_provision probision 

然后需要部署tke-sgx-plugin,该yaml文件同样位于charts/incubator/目录下.

最后部署Gramine&SDK chart,然后进入pod,可以正常使用SGX的功能,并能正常运行Gramine,SGX SDK,SGX DCAP中所有的示例程序.
