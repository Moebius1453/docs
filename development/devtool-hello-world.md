# OpenBMC Hello World using devtool

**Document Purpose:** Walk through compiling and running an OpenBMC application
in QEMU.

**Prerequisites:** Completed Development Environment Setup
[Document](https://github.com/openbmc/docs/blob/master/development/dev-environment.md)

## Clone and Build a Repo

devtool is a command line tool provided by the yocto distribution that allows
you to extract a targeted repositories source code into your local bitbake
environment and modify it.

Today we'll be extracting the
[phosphor-state-manager](https://github.com/openbmc/phosphor-state-manager.git)
repository using devtool and modifying it.

This assumes you have followed the first tutorial and are in the bitbake
environment, right after doing the ". setup" command.

1. Use devtool to extract source code

   ```bash
   devtool modify phosphor-state-manager
   ```

   The above command will output the directory the source code is contained in.

2. Modify a source file and add a cout

   ```bash
   vi workspace/sources/phosphor-state-manager/bmc_state_manager_main.cpp
   ```

   Your diff should look something like this:

   ```bash
   +#include <iostream>

   int main(int argc, char**)
   {
   @@ -17,6 +18,8 @@ int main(int argc, char**)

       bus.request_name(BMC_BUSNAME);

   +    std::cout<<"Hello World" <<std::endl;
   +
       while (true)
       {
   ```

3. Rebuild the flash image which will now include your change

   This will be a much faster build as bitbake will utilize all of the cache
   from your previous build, only building what is new.

   ```bash
   bitbake obmc-phosphor-image
   ```

   Follow the steps in the first tutorial to load your new image into a QEMU
   session and boot it up.

4. Confirm your "Hello World" made it into the new image

   After you login to your QEMU session, verify the message is in the journal

   ```bash
   journalctl | grep "Hello World"
   ```

   You should see something like this:

   ```bash
   <date> romulus phosphor-bmc-state-manager[1089]: Hello World
   ```

You made a change, rebuilt the flash image to contain that change, and then
booted that image up and verified the change made it in!

## Loading an Application Directly Into a Running QEMU

In this section we're going to modify the same source file, but instead of fully
re-generating the flash image and booting QEMU again, we're going to just build
the required binary and copy it into the running QEMU session and launch it.

1. Modify your hello world

   ```bash
   vi workspace/sources/phosphor-state-manager/bmc_state_manager_main.cpp
   ```

   Change your cout to "Hello World Again"

2. Bitbake only the phosphor-state-manager repository

   Within bitbake, you can build only the repository (and it's dependencies)
   that you are interested in. In this case, we'll just rebuild the
   phosphor-state-manager repo to pick up your new hello world change.

   ```bash
   bitbake phosphor-state-manager
   ```

   Your new binary will be located at the following location relative to your
   bitbake directory:
   `./workspace/sources/phosphor-state-manager/oe-workdir/package/usr/bin/phosphor-bmc-state-manager`

3. Create a safe file system for your application

   Now is time to load your Hello World application in to QEMU virtual hardware.
   The OpenBMC overrides the PATH variable to always look in /usr/local/bin/
   first so you could simply scp your application in to /usr/local/bin, run it
   and be done. That works fine for command line tests, but when you start
   launching your applications via systemd services you will hit problems since
   application paths are hardcoded in the service files. Let's start our journey
   doing what the professionals do and create an overlay file system. Overlays
   will save you time and grief. No more renaming and recovering original files,
   no more forgetting which files you messed with during debug, and of course,
   no more bricking the system. Log in to the QEMU instance and run these
   commands.

   ```bash
   mkdir -p /tmp/persist/usr
   mkdir -p /tmp/persist/work/usr
   mount -t overlay -o lowerdir=/usr,upperdir=/tmp/persist/usr,workdir=/tmp/persist/work/usr overlay /usr
   ```

4. scp this binary onto your QEMU instance

   If you used the default ports when starting QEMU then here is the scp command
   to run from your phosphor-state-manager directory. If you chose your own port
   then substitute that here for the 2222.

   ```bash
   scp -P 2222 ./workspace/sources/phosphor-state-manager/oe-workdir/package/usr/bin/phosphor-bmc-state-manager root@127.0.0.1:/usr/bin/
   ```

## Run the Application in QEMU

1. Stop the BMC state manager service

   ```bash
   systemctl stop xyz.openbmc_project.State.BMC.service
   ```

2. Run the application in your QEMU session:

   ```bash
   phosphor-bmc-state-manager
   ```

   You'll see your "Hello World Again" message displayed. Ctrl^C to end that
   application. In general, this is not how you will test new applications.
   Instead, you'll be using systemd services.

3. Start application via systemd service

   OpenBMC uses systemd to manage its applications. There will be later
   tutorials on this, but for now just run the following to restart the BMC
   state service and have it pick up your new application:

   ```bash
   systemctl restart xyz.openbmc_project.State.BMC.service
   ```

   Since systemd started your service, the "Hello World Again" will not be
   output to the console, but it will be in the journal. Later tutorials will
   discuss the journal but for now just run:

   ```bash
   journalctl | tail
   ```

   You should see something like this in one of the journal entries:

   ```bash
   <date> romulus phosphor-bmc-state-manager[1089]: Hello World Again
   ```

That's it! You customized an existing BMC application, built it using devtool,
and ran it within QEMU by both regenerating the entire flash image and patching
it on via scp!

## Advanced Workflow

Once you've got a good handle on the above, there are some more advanced tools
and wrappers you can utilize to optimize this flow. See the
[workflow document](https://amboar.github.io/notes/2022/01/13/openbmc-development-workflow.html)
for more information.


# 使用 devtool 编写 OpenBMC Hello World 程序

**文档目的**：讲解如何在 QEMU 中编译并运行 OpenBMC 应用程序。

**前置条件**：已完成开发环境搭建（参考[文档](https://github.com/openbmc/docs/blob/master/development/dev-environment.md)）

## 克隆并构建代码仓库

devtool 是 Yocto 发行版提供的命令行工具，可将目标代码仓库的源码提取到本地 bitbake 环境中并进行修改。

本次我们将使用 devtool 提取 [phosphor-state-manager](https://github.com/openbmc/phosphor-state-manager.git) 代码仓库并对其进行修改。

本教程假设你已完成第一篇教程的操作，处于 bitbake 环境中（即刚执行完 `. setup` 命令）。

1. 使用 devtool 提取源码

   ```bash
   devtool modify phosphor-state-manager
   ```

   执行上述命令后，终端会输出源码所在的目录路径。

2. 修改源文件，添加 cout 输出

   ```bash
   vi workspace/sources/phosphor-state-manager/bmc_state_manager_main.cpp
   ```

   修改后的代码差异应大致如下：

   ```bash
   +#include <iostream>

   int main(int argc, char**)
   {
   @@ -17,6 +18,8 @@ int main(int argc, char**)

       bus.request_name(BMC_BUSNAME);

   +    std::cout<<"Hello World" <<std::endl;
   +
       while (true)
       {
   ```

3. 重新构建固件镜像（已包含你的修改）

   本次构建速度会快很多，因为 bitbake 会复用之前构建的全部缓存，仅编译新增的修改部分。

   ```bash
   bitbake obmc-phosphor-image
   ```

   按照第一篇教程的步骤，将新生成的镜像加载到 QEMU 会话中并启动。

4. 确认 "Hello World" 已写入新镜像

   登录 QEMU 会话后，在系统日志中验证输出信息：

   ```bash
   journalctl | grep "Hello World"
   ```

   你应该会看到类似如下的输出：

   ```bash
   <日期> romulus phosphor-bmc-state-manager[1089]: Hello World
   ```

至此，你完成了代码修改、重新生成包含修改的固件镜像、启动镜像并验证修改生效的全流程！

## 直接将应用程序加载到运行中的 QEMU 环境

本节我们将修改同一个源文件，但不再重新生成完整固件镜像并重启 QEMU，而是仅编译所需的二进制文件，将其复制到正在运行的 QEMU 会话中并执行。

1. 修改 Hello World 输出内容

   ```bash
   vi workspace/sources/phosphor-state-manager/bmc_state_manager_main.cpp
   ```

   将 cout 输出内容改为 "Hello World Again"

2. 仅编译 phosphor-state-manager 仓库

   在 bitbake 中，你可以只编译目标仓库及其依赖项。本例中，我们仅重新构建 phosphor-state-manager 仓库，以应用新的 Hello World 修改。

   ```bash
   bitbake phosphor-state-manager
   ```

   新生成的二进制文件位于 bitbake 工作目录下的以下路径：
   `./workspace/sources/phosphor-state-manager/oe-workdir/package/usr/bin/phosphor-bmc-state-manager`

3. 为应用程序创建安全的文件系统

   接下来将 Hello World 应用程序加载到 QEMU 虚拟硬件中。OpenBMC 会覆盖 PATH 环境变量，优先查找 `/usr/local/bin/` 目录，因此你可以直接通过 scp 将应用程序传到 `/usr/local/bin` 并运行。这种方式对于命令行测试没问题，但当你通过 systemd 服务启动应用程序时会遇到问题——因为服务文件中硬编码了应用程序的路径。

   我们采用专业开发者的做法，创建叠加文件系统（overlay file system）。叠加文件系统能节省时间、避免很多问题：无需重命名和恢复原文件，不会忘记调试时修改了哪些文件，当然也不会把系统搞崩。

   登录 QEMU 实例，执行以下命令：

   ```bash
   mkdir -p /tmp/persist/usr
   mkdir -p /tmp/persist/work/usr
   mount -t overlay -o lowerdir=/usr,upperdir=/tmp/persist/usr,workdir=/tmp/persist/work/usr overlay /usr
   ```

4. 将二进制文件通过 scp 传到 QEMU 实例

   如果你启动 QEMU 时使用了默认端口，可在 phosphor-state-manager 目录下执行以下 scp 命令。如果你自定义了端口，请将 2222 替换为你设置的端口。

   ```bash
   scp -P 2222 ./workspace/sources/phosphor-state-manager/oe-workdir/package/usr/bin/phosphor-bmc-state-manager root@127.0.0.1:/usr/bin/
   ```

## 在 QEMU 中运行应用程序

1. 停止 BMC 状态管理器服务

   ```bash
   systemctl stop xyz.openbmc_project.State.BMC.service
   ```

2. 在 QEMU 会话中运行应用程序：

   ```bash
   phosphor-bmc-state-manager
   ```

   你会看到 "Hello World Again" 的输出。按 Ctrl+C 终止程序。通常不会用这种方式测试新应用，而是通过 systemd 服务来运行。

3. 通过 systemd 服务启动应用程序

   OpenBMC 使用 systemd 管理各类应用程序。后续教程会详细讲解相关内容，现在只需执行以下命令，重启 BMC 状态服务以加载新的应用程序：

   ```bash
   systemctl restart xyz.openbmc_project.State.BMC.service
   ```

   由于程序由 systemd 启动，"Hello World Again" 不会输出到控制台，但会记录在系统日志中。后续教程会讲解日志相关内容，现在只需执行：

   ```bash
   journalctl | tail
   ```

   你会在其中一条日志中看到类似内容：

   ```bash
   <日期> romulus phosphor-bmc-state-manager[1089]: Hello World Again
   ```

完成！你已经实现了自定义 BMC 应用、通过 devtool 构建，并且通过两种方式在 QEMU 中运行：重新生成完整闪存镜像，以及通过 scp 直接打补丁部署。

## 进阶工作流

熟练掌握上述内容后，你可以使用更多进阶工具和封装脚本来优化开发流程。详见[工作流文档](https://amboar.github.io/notes/2022/01/13/openbmc-development-workflow.html)。