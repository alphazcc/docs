# 使用 PetaLinux 创建 Linux

**开发环境**：Ubuntu 16.04

**开发工具**：Petalinux 2017.4

## Step1 创建 Petalinux 工程
1.1 将 Vivado 工程目录下`*.sdk`文件夹中的`*.hdf`文件复制到新建的proj文件夹中

![图片](./images/petalinux/petalinux01.png)

1.2 在当前文件夹下进入终端，配置 Vivado 和 Petalinux 环境变量

```
source /opt/pkg/petalinux/settings.sh
source /opt/Xilinx/Vivado/2017.4/settings64.sh
```
![图片](./images/petalinux/petalinux02.png)

1.3 创建 Petalinux 工程，工程名为`platform_peta`

```
petalinux-create --type project --template zynq --name platform_peta
```
![图片](./images/petalinux/petalinux03.png)

1.4 进入 petalinux 工作目录
```
cd platform_peta
```
![图片](./images/petalinux/petalinux04.png)

1.5 配置 petalinux 工程的硬件信息，`/home/zcc/my/proj`为`*.hdf`文件所在目录
```
petalinux-config --get-hw-description /home/zcc/my/proj
```
![图片](./images/petalinux/petalinux05.png)

1.6 弹出 petalinux 工程配置窗口

![图片](./images/petalinux/petalinux06.png)

1.7 `Linux Components Selection` 选项可以配置 uboot 和 Linux 内核来源，默认从 git 上下载，这里选用本地内核文件，进入`Linux Components Selection→linux-kernel (linux-xlnx)`，选中`ext-local-src`

![图片](./images/petalinux/petalinux07.png)

1.8 选择`External linux-kernel local source settings→EXternal linux-kernel local source path`，填写本地 Linux 内核源码的绝对路径

![图片](./images/petalinux/petalinux08.png)

1.9 保存并退出，等待编译结束

![图片](./images/petalinux/petalinux09.png)

若想再次配置 petalinux 工程，可使用`petalinux-config`命令再次配置

## Step2 配置 linux 内核
2.1 执行配置 Linux 内核命令
```
petalinux-config -c kernel
```
![图片](./images/petalinux/petalinux10.png)

2.2 一段时间后弹出内核配置界面

![图片](./images/petalinux/petalinux11.png)

2.3 配置以太网 phy 芯片驱动，选中`Device Drivers→Network device support→PHY Device support and infrastructure→Micrel PHYs`

![图片](./images/petalinux/petalinux12.png)

2.4 按需配置其他相关驱动，配置完成后保存并退出，等待编译结束

![图片](./images/petalinux/petalinux13.png)

## Step3 配置根文件系统
执行配置根文件系统命令，这里保持默认
```
petalinux-config -c roofs
```
![图片](./images/petalinux/petalinux14.png)

## Step4 编译 petalinux 工程
4.1 配置编译 uboot、内核、根文件系统、设备树等
```
petalinux-config -build
```
![图片](./images/petalinux/petalinux15.png)

4.2 生成 BOOT 文件
```
petalinux-package --boot --fsbl ./images/linux/zynq_fsbl.elf --fpga ./images/linux/top.bit --u-boot --force
```
![图片](./images/petalinux/petalinux16.png)

4.3 复制`BOOT.bin`和`iamge.ub`到 SD 卡中。

![图片](./images/petalinux/petalinux17.png)

## Step5 板载测试

**测试平台**：黑金 Zynq7035 开发板

**芯片型号**：XC7Z035-2FFG676I

将 SD 卡插入板卡，设置 SD 启动模式，这里使用 PuTTY 查看日志输出

5.1 启动板卡

![图片](./images/petalinux/petalinux18.png)

5.2 Success！

![图片](./images/petalinux/petalinux19.png)

