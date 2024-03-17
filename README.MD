# Yocto Hello World Module

For adding a new layer/recipe in Yocto, you can refer to the detailed guide available at:

"For more details on adding a new layer or recipe, please refer to the comprehensive guide available at: https://saulpoorman.blogspot.com/2024/03/yocto-hello-world-project.html"

---

## **Directory Structure**

The following example demonstrates adding a new **`hellomod`** recipe directly under the **`recipes-hello`** layer:

```bash
user@user-Standard-PC-Q35-ICH9-2009:~/yocto_101/meta-helloworld$ tree
.
├── conf
│   └── layer.conf
├── COPYING.MIT
├── README.MD
└── recipes-hello
    ├── hello
    │   ├── files
    │   │   └── helloworld.c
    │   └── hello_0.1.bb
    └── hellomod
        ├── files
        │   ├── hellomod.c
        │   └── Makefile
        └── hellomod_0.1.bb
```

---

## **hellomod_0.1.bb Recipe**

```bash
SUMMARY = "Simple Hello Module application"
SECTION = "examples"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

inherit module

SRC_URI = "file://Makefile \
           file://hellomod.c \
          "

S = "${WORKDIR}"

# The inherit of module.bbclass will automatically name module packages with
# "kernel-module-" prefix as required by the oe-core build environment.

RPROVIDES_${PN} += "kernel-module-hello"
```

---

## **hellomod.c Source File**

```bash
#include <linux/module.h>
#include <linux/init.h>

/* Meta Information */
MODULE_LICENSE("GPL");
MODULE_AUTHOR("Saul Poorman");
MODULE_DESCRIPTION("A hello world LKM");

/**
 * @brief This function is called when the module is loaded into the kernel.
 */
static int __init my_init(void) {
	printk("Hello, Kernel!\n");
	return 0;
}

/**
 * @brief This function is called when the module is removed from the kernel.
 */
static void __exit my_exit(void) {
	printk("Goodbye, Kernel\n");
}

module_init(my_init);
module_exit(my_exit);
```

---

## **Makefile**

```bash
obj-m := hellomod.o

SRC := $(shell pwd)

all:
	$(MAKE) -C $(KERNEL_SRC) M=$(SRC)

modules_install:
	$(MAKE) -C $(KERNEL_SRC) M=$(SRC) modules_install

clean:
	rm -f *.o *~ core .depend .*.cmd *.ko *.mod.c
	rm -f Module.markers Module.symvers modules.order
	rm -rf .tmp_versions Modules.symvers
```

---

## **Build Commands**

```bash
bitbake hellomod
bitbake core-image-minimal
```

---

## **Append Extra Dependencies**

```bash
# in path/build/conf/local.conf
MACHINE_ESSENTIAL_EXTRA_RDEPENDS += "kernel-module-hello"
```

---

## **Checking the .ko File in the Root Filesystem**

```bash
user@:path/build/tmp/work/qemux86_64-poky-linux/core-image-minimal/1.0-r0/rootfs/lib/modules/5.10.107-yocto-standard/extra$ ls
hellomod.ko
```

---

## **Running the Emulator**

```bash
runqemu
```

---

## **Installing the Driver**

```bash
modprobe hellomod
```


## Result
![!\[alt text\](pictures/image.png)](pictures/hellomod_result.png)