# oscomp-kernel

> 目前支持 os 比赛相关测例的测试，采用形式与 os 比赛相同，选手需要在根目录添加一个 Makefile 文件，使用 make all 命令在根目录生成 sbi-qemu 和 kernel-qemu 两个文件，由评测机自动执行。

- 一个基本的能运行的 `kernel demo`： [https://github.com/yfblock/oscomp-kernel-example](https://github.com/yfblock/oscomp-kernel-example)
- 一个能通过所有测例的 `kernel complete reference`：[暂不公布]()

目前已经支持 `libc-test`， `busybox`, `lua`, `lmbench` 相关测例，测试过程无人工干预，需要由内核自动运行，所有测例文件放在镜像中，内核需要支持 `fat32` 文件系统来读取文件。 [镜像文件](https://github.com/os-autograding/testsuits-in-one/raw/gh-pages/fat32.img)

评测机运行指令如下：

```shell
qemu-system-riscv64 \
    -machine virt \
    -bios sbi-qemu \
    -device loader,file=kernel-qemu,addr=0x80200000 \
    -drive file=fat32.img,if=none,format=raw,id=x0 \
    -device virtio-blk-device,drive=x0,bus=virtio-mmio-bus.0 \
    -kernel kernel-qemu \
    -nographic \
    -smp 4 -m 2G
```

内核执行时间为 `300` 秒（`5`分钟），超时后程序会被终止，不再继续执行，所得分数为超时前完成的部分的分数。

程序执行完毕会在 gp-pages 目录下生成相关的 `log` 文件和 `README` 文件，在 `README` 文件中可以看到当前得到的分数，在 `log` 文件中能看到详细的得分情况。

注意事项：
- `QEMU` 版本为 `7.0.0`
- `RUST ToolChain` 版本为 `nightly-2022-08-08`
- 编译目标架构为 `riscv64imac-unknown-none-elf`
- 内核执行时间为 `5` 分钟
- 内核可用内存大小为 `2G`
- 只有 `main` 分支的提交可以被评测机处理
- 评测机在初次运行时需要编译 `qemu`，可能需要花费一些时间，请耐心等待
