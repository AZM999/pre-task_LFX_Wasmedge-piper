
# Pre-Tasks for LFX Mentorship project " Support piper as a new backend of the WASI-NN WasmEdge plugin"

This repo contains the record of pre-qualifying task for the LFX Mentorship project under WasmEdge project. 
I am currently using Debian 12 Bookworm 

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
My previous contribution to Piper Framework :

- https://github.com/rhasspy/piper/issues/503
---
## 1. Build Piper Framework and Run sample applications

To build the piper Framework first we need to have Piper-phonemize in the ```lib/linux-x86_64/``` directory.
 
I cloned the [forked repo](https://github.com/AZM999/piper-phonemize) from my github and build using docker :

```docker buildx build . -t piper-phonemize --output 'type=local,dest=dist'```

then I copied the piper-phonemize folder into the ```/lib/x86_64-linux-gnu/``` folder.

Clone the [fork of Piper](https://github.com/AZM999/piper) to my local directory.
I then made a build folder inside piper directory.

```
mkdir build
cd build/
```
since the ```CMakeLists.txt``` is in the folder above.

```
cmake .. && make . &&> piper_build_logs.txt
```
build logs for piper can be found [here](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/2e245e67e253ed8297a92dbe114ef9fdebb4f3d0/piper_build_logs.txt)

checking if piper is installed correctly :
```
> ./piper --version
     1.2.0
```

after the build was successfull I downloaded a [voice](https://huggingface.co/rhasspy/piper-voices) and a config file and placed it inside the same folder. 
I chose "lessac_en_US" voice.

then I Ran a sample tts in piper with the followwing command :
```
echo 'Welcome to the world of speech synthesis!, the build has been successfull' | \
  ./piper --model en_US-lessac-medium.onnx --output_file welcome.wav
```

![piper-test](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/2e245e67e253ed8297a92dbe114ef9fdebb4f3d0/piper_build_test.png?raw=true)

The output audio file [welcome.wav](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/2e245e67e253ed8297a92dbe114ef9fdebb4f3d0/welcome.wav)

Now Lets Run a Sample application, lets convert the Paragraph 3 of Beyond Good and Evil by Fredrich nietzche from [here](https://www.gutenberg.org/files/4363/4363-h/4363-h.htm#link2HCH0001)

According to [README.md](https://github.com/rhasspy/piper/blob/master/README.md) file from piper we have to format the text in json format as text object with text field in ```.json``` file :
```
{ "text": "First sentence to speak." }
{ "text": "Second sentence to speak." }
```

to pass the JSON file as input we have to specify with ```--json-input``` flag.

```
cat bge_nietzche.json | ./piper --model en_US-ryan-high.onnx --json-input --output_file bge_nietzche.wav 
```
![bge_nietzche.json](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/c7dd19c0003fca2451f697ed5ff5a781d48aefe2/bge_nietzche.json)

![piper-sample-bge](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/c7dd19c0003fca2451f697ed5ff5a781d48aefe2/piper_sample_bge.png)


the output file can be found [here](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/c7dd19c0003fca2451f697ed5ff5a781d48aefe2/bge_nietzche.wav)

---
## 2. Build WasmEdge with llama.cpp
To build WasmEdge I followed the following [guide](https://wasmedge.org/docs/contribute/source/plugin/wasi_nn)
```
docker pull wasmedge/wasmedge
docker rub -it --rm \
    -v /home/azm/projects/WasmEdge/:/root/wasmedge \
    wasmedge/wasmedge:latest

cmake -GNinja -Bbuild -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_CUDA_ARCHITECTURES="60;61;70" \
  -DCMAKE_CUDA_COMPILER=/usr/local/cuda/bin/nvcc \
  -DWASMEDGE_PLUGIN_WASI_NN_BACKEND="GGML" \
  -DWASMEDGE_PLUGIN_WASI_NN_GGML_LLAMA_BLAS=OFF \
  -DWASMEDGE_PLUGIN_WASI_NN_GGML_LLAMA_CUBLAS=ON \
  .

cmake --build build

cmake --install build
```
The build logs for WasmEdge with llama.cpp Can be found [here](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/2e245e67e253ed8297a92dbe114ef9fdebb4f3d0/build_log_wasmedge.txt) 

then I tested the build by running tests :
```
LD_LIBRARY_PATH=$(pwd)/lib/api ctest
```
![wasmedge-test](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/2e245e67e253ed8297a92dbe114ef9fdebb4f3d0/WasmEdge_build_test.png)

To run ```llama2-7b``` I followed the following [repo](https://github.com/second-state/WasmEdge-WASINN-examples)

Compiling the application to Wasm :
```
cd llama
cargo build --target wasm32-wasi --release
```
Downloading Model :
```
curl -LO https://huggingface.co/TheBloke/Llama-2-7b-Chat-GGUF/resolve/main/llama-2-7b-chat.Q5_K_M.gguf
```
Running the ```Llama-2-7b-Chat-GGUF``` model :
```
wasmedge --dir .:. \
  --nn-preload default:GGML:AUTO:llama-2-7b-chat.Q5_K_M.gguf \
  wasmedge-ggml-llama.wasm default
```
![llama-test](https://github.com/AZM999/pre-task_LFX_Wasmedge-piper/blob/2e245e67e253ed8297a92dbe114ef9fdebb4f3d0/llama.cpp_example_wasedge-ggml.png)
