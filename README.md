
# Pre-Taska for LFX Mentorship project " Support piper as a new backend of the WASI-NN WasmEdge plugin"

This repo contains the record of pre-qualifying task for the LFX Mentorship project. 
I am using Debian 12 Bookworm 

``` Static hostname: debian
       Icon name: computer-laptop
         Chassis: laptop 💻      Machine ID: f4cf47ccae8d41be85ed6fec77733513
         Boot ID: bef04fc4ac9447f0999005928c062ada
Operating System: Debian GNU/Linux 12 (bookworm)  
          Kernel: Linux 6.1.0-21-amd64
    Architecture: x86-64
 Hardware Vendor: Lenovo
  Hardware Model: IdeaPad Gaming3 15ARH05D
Firmware Version: FCCN18WW

```
## 1. Build Piper Framework and Run sample applications

To build the piper Framework first we need to have Piper-phonemize in the ```lib/linux-x86_64/``` directory. 
Clone the Piper-phonemize repo in local directory and 
I cloned the [forked repo]() from my github and build using docker :
```docker buildx build . -t piper-phonemize --output 'type=local,dest=dist'```
then I copied the piper-phonemize folder into the ```/lib/x86_64-linux-gnu/``` folder.

Clone the [fork of Piper] to my local directory.
I then made a build folder inside piper directory.
```mkdir build
   cd build/
```
since the ```CMakeLists.txt``` is in the folder above.

```cmake .. && make . &&> piper_build_logs.txt
```
build logs can be found [here](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/2e245e67e253ed8297a92dbe114ef9fdebb4f3d0/piper_build_logs.txt)

checking if piper is installed correctly :
```> ./piper --version
     1.2.0
```

after the build was successfull I downloaded a [voice](https://huggingface.co/rhasspy/piper-voices) and a config file and placed it inside the same folder. I chose "lessac_en_US" voice.

then I tested piper with the followwing command :
```echo 'Welcome to the world of speech synthesis!, the build has been successfull' | \
  ./piper --model en_US-lessac-medium.onnx --output_file welcome.wav```

![alt text](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/2e245e67e253ed8297a92dbe114ef9fdebb4f3d0/piper_build_test.png?raw=true)

The output audio file [welcome.wav](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/2e245e67e253ed8297a92dbe114ef9fdebb4f3d0/welcome.wav)


## 2. Build WasmEdge 

## 3. Build WasmEdge with llama.cpp plugin
