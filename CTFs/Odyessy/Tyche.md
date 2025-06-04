*During the [Hellenistic period](https://en.wikipedia.org/wiki/Hellenistic_period "Hellenistic period"), with dramatic socio-political changes starting with [Alexander the Great](https://en.wikipedia.org/wiki/Alexander_the_Great "Alexander the Great", Tyche increasingly embodied the whims of fate (both negative and positive), eclipsing the role of the Olympic gods.* - wikipedia

there is no executable
`nc challenges.hackrocks.com 21337`

we get a shell like program. When we run help, this is the info we get:

```
Built-in commands:
------------------
        . : [ [[ alias bg break cd chdir command continue echo eval exec
exit export false fg getopts hash help history jobs kill let
local printf pwd read readonly return set shift source test times trap true type ulimit umask unalias unset wait
```

this is what we get from running ls:
```
banner    etc       init      proc      sys       usr
bin       flag      lib       root      tmp
dev       home      linuxrc   sbin      tyche.ko
```

the tyche.ko file is likely the exploit. It is a kernel module with a vulnerability that can get us kernel mode execution.

#### Getting the file
using netcat with the -T functionality, we can print out everything from the session. Trimming stuff at the beginning and the end, we have the file and can open it in ghidra.

system calls:
`tyche_open`
- this ight be some user authentication module
- something in here is the pointer to
- creds: lVar1 + 0x638
`tyche_ioctrl`
- sets the `*fun` based on param 3
- this can be used to 
`tyche_read`
- allocates a portion of memory??
- gives you the address of mutex_lock
- can be used to leak address
`tyche_release`

explanation of /proc : https://medium.com/dvt-engineering/how-to-write-your-first-linux-kernel-module-cf284408beeb


## Exploit Path

/proc/tyche

- file = open(/proc/tyche)
- file.ioctl("filename", 0x1001, 0x0)
- file.read(file, mutex_lock, 16, 0)
- base = mutex_lock - 0x1010
- cred = base + 0x1020 + 0x638 - 0x4
- ioctl(file, 0x1001, 0xf0)
- ioctl(file, 0x1002, 0)

But we need to be able to find the cred pointer. How can we do that.

Gain more information about the module by running it on 

qemu build command
```bash
qemu-system-x86_64 \
-m 2048M \
-kernel ./bzImage \
-initrd ./initramfs_new.cpio.gz \
-cpu kvm64,+smep,+smap \
-append "console=ttyS0 oops=panic panic=1 kpti=1 nokaslr quiet" \
-monitor /dev/null \
-drive file=./tyche.ko,format=raw,index=0,media=disk \
-serial mon:stdio \
-nographic
```

zipping up the initramfs file
```bash
find . | cpio -o -H newc | gzip -9 > ../initramfs_new.cpio.gz
```


# We Were Wrong
### Or the solution

