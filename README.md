## Arm IP Explorer custom software development example

This software is for [Arm IP Explorer](https://ipexplorer.arm.com)

You can prepare software applications for benchmarking on Arm processors and run them with IP Explorer.

You need an Arm account to access IP Explorer. If you need a new account visit [Arm Developer](https://developer.arm.com) and click the person icon in the top right corner and click Register. If you don't have access to IP Explorer you will get a form to request access. Fill out the form and somebody will review and grant access. 

## Create and upload custom software applications

The steps below teach you how to create a basic software application. One you learn them you can create more complex applications for benchmarking.

It helps if you have the GNU compiler for cross-compiling for Cortex-M. 

The executable is `arm-none-eabi-gcc`

If you don't have it IP Explorer can compile your applications, but it saves time if you check them yourself first to save time uploading.

To install the compile on Ubuntu or Debian:

```console
sudo apt update
sudo apt install gcc-arm-none-eabi -y
```

## Get started

### Step 0:

Log in to IP Explorer and click `Simulate IP` on the top right navigation in Arm IP Explorer.

Scroll to the bottom of the page and click `My Uploads` and then `Upload Software`

Download the software source code by clicking on `Download .tgz file`.

Copy downloaded file `user-software.tgz` to a directory where you want to work.

Extract the download:

```console
tar xf user-software.tgz
```

Everything is located in the benchmark-package directory.


### Step 1: 

Follow the instructions in README.txt to compile one of the provided applications or create your own. 

The steps here are condensed version of README.txt

To build the "hello world" application which is provided in the download using the GCC compiler run:

```bash
./build_app.sh hello m0px1_nocache GCC
```

To create a new application called `fp-multiply` use:

```bash
cp -r Applications/app-template Applications/fp-multiply
mv Applications/fp-multiply/app-template.c Applications/fp-multiply/fp-multiply.c
```

### Step 2: 

Edit the file `sw_options.json` and set `name`, `description` and `valid_systems` tags.

The `name` tag value should have the name of all applications you would like to upload. For example: `"name": ["fp-multiply"],`

The `valid_systems` tag value should have the system names you want to run your custom software on. For example: `"valid_systems": ["m0px1_nocache"].`

The Systems directory contains the names of the systems that are options for the `"valid_systems"`. 

```
{
        "software_name": ["fp-multiply"],
        "software_description":["Floating point multiply test"],
        "valid_compilers": ["AC6","GCC"],
        "valid_systems": ["m0px1_nocache"]
}
```

Save changes in the json file.

To create multiple applications, repeat this step for each application.

### Step 3:

Modify the `Applications/fp-multiply/fp-multiply.c` source file to add your benchmark code between the comments "Enter user defined code here" and "End user defined code here" and save changes. For example, use this `main()` function:

```c
int main()
{
    double a = 3.14;
    double b = 6.023456;
    double product;

    (void) start_marker();

    // Calculating product
    product = a * b;

    (void) stop_marker();

    printf("Product = %.2lf\n", product);

    return 0;
}
```

### Step 4 (optional): 

You can compile your custom software before uploading it. 

You can also let IP Explorer compile your software after upload. If there any errors it is more time consuming to skip the local compilation.

To compile the application with Arm Compiler for the `fp-multiply` example:

```bash
./build_app.sh fp-multiply m0px1_nocache GCC
```
  
This step creates the compiled software executable in ./Applications/fp-multiply/GCC/fp-multiply.axf

It's also possible to not share your source code with IP Explorer and just provide binary files to run. Look in the README.txt for the `"cloud_compilation"` option which is used to tell IP Explorer you won't want it to do any compiling. The default is to compile your source code in IP Explorer.

### Step 5: 

Run the app checker script.

```bash
./app_checker.py
```

### Step 6: 

Package the source tree for upload to IP Explorer:

```console
tar -cvzf custom-software.tgz benchmark-package/
```

Upload the `custom-software.tgz` to Arm IP Explorer and run it on the available systems. 
