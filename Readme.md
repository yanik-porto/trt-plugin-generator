# Description

Ce repos a été créé à partir et reprend des éléments du repos de TensorRt : https://github.com/NVIDIA/TensorRT .
Il permet de générer des librairies dynamiques qui contiennent des plugins custom, non gérés par les versions existantes de TensorRt.

Le liste des plugins contenus dans ce repos sont indiqués dans plugin/Readme.md

Pour ajouter un plugin, créer une nouvelle classe qui dérive de *IPluginV2* ou *IPluginV2DynamicExt* et ajouter celle-ci au CMakeLists.txt.


# Compilation

La compilation reprend les même options que celle de TensorRt OSS.

**Preliminary steps**

	```bash
	export CUDACXX=<CUDA_BIN_PATH>/nvcc
	export TRT_SOURCE=<THIS_REPOS_PATH>
	export TRT_RELEASE=<TENSORRT_ROOT_DIR>
	```

**Example: Linux (x86-64) build with default cuda-11.6.2**

	```bash
	cd $TRT_OSSPATH
	mkdir -p build && cd build
	cmake .. -DTRT_LIB_DIR=$TRT_RELEASE/lib -DTRT_OUT_DIR=`pwd`/out -DCUDA_TOOLKIT_ROOT_DIR=<CUDA_ROOT_PATH> -DCUDNN_ROOT_DIR=<CUDNN_ROOT_PATH>
	make -j$(nproc)
	```

**Example: Ubuntu 20.04 Cross-Compile for Jetson Nano (aarch64) with cuda-10.2 (JetPack)**

	```bash
	cd $TRT_OSSPATH
	mkdir -p build && cd build
	cmake .. -DCMAKE_TOOLCHAIN_FILE=$TRT_SOURCE/cmake/toolchains/JetsonNano.toolchain -DCUDA_VERSION=10.2 -DCUDNN_LIB=<CUDNN_ROOT_PATH>/lib/libcudnn.so -DCUBLAS_LIB=<CUDA_ROOT_PATH>/lib64/libcublas.so -DCUBLASLT_LIB=<CUDA_ROOT_PATH>/lib64/libcublasLt.so -DCMAKE_CUDA_COMPILER=<CUDA_ROOT_PATH>/bin/nvcc -DCUDA_ROOT=<CUDA_ROOT_PATH> -DCUDA_INCLUDE_DIRS=<CUDA_ROOT_PATH>/include
	make -j$(nproc)
	```

# Utilisation

Les plugins générés se trouvent dans $TRT_SOURCE/build/plugin.

Pour générer un model onnx, en engine, qui comporte une fonction se trouvant dans un des plugins compilés :

```
trtexec --onnx=<ONNX_MODEL_PATH> --saveEngine=<ENGINE_OUTPUT_PATH> --plugins=<PLUGIN_1> --plugins=<PLUGIN_2>
```

> NOTE: Ajouter autant d'option `--plugins` que de plugins nécessaires.