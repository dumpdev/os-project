Project 1: Build and Load Your First Kernel Module

This teaches:

How the kernel is extended
Kernel vs user space
Kernel build system
Module loading/unloading
Kernel logging
Step 1: Install Required Packages
sudo apt update
sudo apt install build-essential linux-headers-$(uname -r)

Verify:

uname -r
ls /lib/modules/$(uname -r)/build

The second command should show kernel build files.

Step 2: Create a Project Directory
mkdir ~/kernel-module-lab
cd ~/kernel-module-lab
Step 3: Create hello.c
#include <linux/module.h>
#include <linux/kernel.h>

static int __init hello_init(void)
{
    printk(KERN_INFO "Hello from my first kernel module!\n");
    return 0;
}

static void __exit hello_exit(void)
{
    printk(KERN_INFO "Goodbye from my kernel module!\n");
}

module_init(hello_init);
module_exit(hello_exit);

MODULE_LICENSE("GPL");
MODULE_AUTHOR("Your Name");
MODULE_DESCRIPTION("My first Linux kernel module");
Step 4: Create Makefile
obj-m += hello.o

all:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) modules

clean:
	make -C /lib/modules/$(shell uname -r)/build M=$(PWD) clean
Step 5: Build the Module
make

You should see files like:

hello.ko
hello.o
hello.mod

The important one is:

hello.ko

(Kernel Object)

Step 6: Load the Module
sudo insmod hello.ko

Check loaded modules:

lsmod | grep hello
Step 7: Read the Kernel Log

Recent Ubuntu versions:

sudo dmesg | tail

or

sudo journalctl -k -n 20

You should see:

Hello from my first kernel module!
Step 8: Remove the Module
sudo rmmod hello

Check logs again:

sudo journalctl -k -n 20

Expected:

Goodbye from my kernel module!
What You Just Learned

When you run:

sudo insmod hello.ko

the kernel:

Loads your binary into kernel memory.
Resolves symbols.
Executes hello_init().
Registers the module.

When you run:

sudo rmmod hello

the kernel:

Calls hello_exit().
Releases resources.
Unloads the module.

This is fundamentally different from a normal user-space program.

Bonus Investigation

After loading:

cat /proc/modules

Find your module.

Also inspect:

modinfo hello.ko

You'll see metadata extracted from:

MODULE_LICENSE(...)
MODULE_AUTHOR(...)
MODULE_DESCRIPTION(...)
Your first milestone

Try completing Steps 1–8 and paste:

Any compilation errors,
The output of make,
Or the output of sudo journalctl -k -n 20.
