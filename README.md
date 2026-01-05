# Bacco_build

1. 根据armbian  的edge主线编译 生成6.12.42 d-current-rockchip64 
2. debian 13
3. hdmi线正常使用，使用过程发现BSP不支持npu，没有npu的overlays，选择本地编译。

- 配置环境

  - 1过程一直都是cpu在跑，gpu可以跑这个吗，后面有时间想修改一下)在wsl2 ubuntu24.04环境下编译运行。

- 安装git clone 项目

  ```
  sudo apt install git
  ```

- clone 仓库

  ```markdown
  #新建目录并进行目录空间
  mkdir ~/image && cd ~/image
  #本文是基于amlogic-s9xxx-armbian编译clone该项目
  git clone https://github.com/ophub/amlogic-s9xxx-armbian.git
  #clone kernel用来替换内核
  git clone https://github.com/ophub/kernel.git
  ```

- 安装内核的编译依赖

  ```
  sudo apt install -y $(cat compile-kernel/tools/script/ubuntu2404-build-armbian-depends)
  sudo apt install -y libssl-dev
  ```

- 切换内核根据github actions 过程使用的内核配置

  ```
  cp kernel/kernel-config/release/rk35xx/config-6.1 到 compile-kernel/tools/config/config-6.1
  ```

- 编译内核

  ```
  在imges目录下运行sudo ./recompile -k 6.1.y -r unifreq/linux-6.1.y-rockchip -d true
  ```

- 编译成功进aml目录克隆armbain/build 

  ```
  git clone https://github.com/armbian/build
  cd build
  ```

- 编译armbian根据

  - [BUILD](https://github.com/armbian/build.git)配置参考。我选择debian13 完整镜像后续选择在npu support 查看npu支持，进行编译

- ```
  ./compile.sh BOARD=lckfb-taishanpi
  ```

  编译过程根据sh脚本文件download 文件需要去github 自己可以拉取修改脚本跳过。

  编译成的文件将输出在 `amlogic-s9xxx-armbian/build/output/images/` 目录，文件名类似 `Armbian-unofficial`

​	回到aml目录根据本地内核打包参考仓库rebuild文件

​        修改后二次打包 内核版本6.1.141是你编译内核时候拉取的版本号

- ```
  sudo ./rebuild -b lckfb-tspi -a false -k 6.1.141
  ```

  在刚刚的images目录下会生成tar.gz就是二次打包的文件，

  ![image-20260105165821307](https://gitee.com/tu-liangxiang/pic-img/raw/master/blog/image-20260105165821307.png)

  解压烧录，需要进 [MASKROM升级模式](https://niconiconi.fun/2025/11/14/compile-amlogic-s9xxx-armbian/`https://wiki.lckfb.com/zh-hans/tspi-rk3566/system-usage/img-download.html#maskrom升级模式`)进行升级，可以擦除升级。用户密码root:1234 后面就修改密码创建用户和arm流程一样

- 

  - [调整灯光](https://wiki.lckfb.com/zh-hans/tspi-rk3566/system-usage/buildroot-system-usage.html#%E7%82%B9%E7%81%AF)
  - 接电源管理电路读取温度电源电流

- 系统问题，有大量没用的包，需要精简。比如网口，后面换ubunu24.04打包

- 没有adb shell 想要自己编译进入。

- 本次用到的分支

  -  https://github.com/ophub/amlogic-s9xxx-armbian 47c0864
  - https://github.com/ophub/kernel 049b792e621c4d72e3b05f5919bd98f0fda266b4
  - linux-6.1
  - build
