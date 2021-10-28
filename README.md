# lcr

轻量级容器运行时 (`lcr`) 是用于根据 OCI 规范生成和运行容器的 CLI 工具。它基于`liblxc`，采用`C`语言编写，并可以被容器引擎[iSulad](https://gitee.com/openeuler/iSulad)使用。[iSulad](https://gitee.com/openeuler/iSulad)通过 `dlopen` 调用`lcr`中定义的函数。

## 参与贡献

我们总是欢迎新的贡献者，我们很乐意为新的贡献者提供指导。 iSulad 遵循内核编码约定。您可以在以下位置找到详细介绍：

- https://www.kernel.org/doc/html/v4.10/process/coding-style.html

  

## 许可

lcr基于LGPLv2.1+许可。

