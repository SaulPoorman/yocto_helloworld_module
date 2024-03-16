# README for Yocto Hello World Project

## **Introduction**

This README provides step-by-step instructions on how to set up your environment, build a simple Hello World application using the Yocto Project, and execute it. It covers creating a new layer, adding it to your build configuration, preparing necessary files for your application, and building an image that includes your application.

## **Environment Setup**

To prepare your environment, follow these steps:

1. Navigate to your Yocto Project directory:
    
    ```bash
    cd path/poky
    ```
    
2. Source the environment setup script to create a build environment. This step also specifies the location (**`../build`**) where the environment variables and images will be stored:
    
    ```bash
    source oe-init-build-env ../build    
    ```
    
3. Create a new layer for your application:
    
    ```bash
    bitbake-layers create-layer ../meta-helloworld
    ```
    
4. Add the newly created layer to your build configuration:
    
    ```bash
    bitbake-layers add-layer ../meta-helloworld
    ```
    

## **Directory Structure**

Your new layer's directory structure should look like this:

```markdown
markdownCopy code
meta-helloworld
├── conf
│   └── layer.conf
├── COPYING.MIT
├── README
└── recipes-hello
    └── hello
        ├── files
        │   └── helloworld.c
        └── hello_0.1.bb
```

## **Preparing the Hello World Application**

### **The `helloworld.c` File**

Create a **`helloworld.c`** file in the **`recipes-hello/hello/files`** directory with the following content:

```c
#include <stdio.h>int main() {
    printf("Hello, World!\n");
    return 0;
}
```

### **The `hello_0.1.bb` Recipe**

Create a BitBake recipe named **`hello_0.1.bb`** in the **`recipes-hello/hello`** directory. This recipe defines how to build and install your Hello World application:

```bash
SUMMARY = "Simple Hello World application"
SECTION = "examples"
LICENSE = "MIT"
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

SRC_URI = "file://helloworld.c"

TARGET_CC_ARCH += "${LDFLAGS}"

S = "${WORKDIR}"

do_compile() {
    ${CC} helloworld.c -o helloworld
}

do_install() {
    install -d ${D}${bindir}
    install -m 0755 helloworld ${D}${bindir}
}
```

## **Configuring the Build**

To include the Hello World application in your image, add the following line to the **`build/conf/local.conf`** file:

```bash
IMAGE_INSTALL_append = " hello"
```

## **Building the Image**

Run the following commands to build your application and the core image:

```bash
bitbake hello
bitbake core-image-minimal
```

## **Execution**

After building, run the Hello World application. Ensure the **`helloworld`** executable is present in the **`/usr/bin`** directory of your target device or emulator:

```bash
./helloworld

Output:
    Hello, World!
```

## **Notes**

- **COMMON_LICENSE_DIR**: Directory storing common license files, used for verifying package licenses.
    - Example path: **`path/poky/meta/files/common-licenses`**
- **LDFLAGS**: Linker flags, used to control how code and libraries are linked together.
- **WORKDIR**: Temporary working directory where source code is compiled and processed.
- **CC**: Command for the C compiler, such as **`gcc`**, used to compile C programs.
- **bindir**: Target system directory where binary files (executables) should be installed, usually **`/usr/bin`** or **`/bin`**.

## **Additional Information**

The MD5 checksum for the MIT license file can be obtained as follows:

```bash
cd path/poky/meta/files/common-licenses
md5sum MIT
```

This checksum is used in the **`LIC_FILES_CHKSUM`** field of the BitBake recipe to verify the license file.