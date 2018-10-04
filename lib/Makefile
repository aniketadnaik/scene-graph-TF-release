CXX:=g++
NVCC:=nvcc
LINK:=g++ -fPIC
CXXFLAGS= -std=c++11 
CUDA_FLAGS=-D GOOGLE_CUDA=1 --x=cu -Xcompiler -fPIC --expt-relaxed-constexpr -D_GLIBCXX_USE_CXX11_ABI=0
COMPILE_FLAGS ="-std=c++11 -c -o" 
ROOT_DIR = roi_pooling_layer
ROIPOOL_LIB = "$(ROOT_DIR)/roi_pooling_op_gpu.so"  
BUILD_DIR = $(ROOT_DIR)/build
SRC_DIR = $(ROOT_DIR)/src
LIBS = -lcudart 
LIBDIRS = -L/usr/local/cuda/lib64
INCLDIRS = -I/usr/local/cuda/include -I/usr/local 
TF_INCL = $(shell python -c 'import tensorflow as tf; print(tf.sysconfig.get_include())') 
TF_LIB = $(shell python -c 'import tensorflow as tf; print(tf.sysconfig.get_lib())')
DIR_TARGET := $(BUILD_DIR)

all: clean $(ROIPOOL_LIB)	
	python setup.py build_ext --inplace

$(DIR_TARGET):
	mkdir -p $(BUILD_DIR)

$(BUILD_DIR)/roi_pooling_op_gpu.cu.o: $(SRC_DIR)/roi_pooling_op_gpu.cu.cc
	$(NVCC) -std=c++11 -c -o $(BUILD_DIR)/roi_pooling_op_gpu.cu.o -I $(TF_INCL) \
		$(INCLDIRS) -L $(TF_LIB) -ltensorflow_framework $(CUDA_FLAGS) \
		$(SRC_DIR)/roi_pooling_op_gpu.cu.cc

$(ROIPOOL_LIB): $(DIR_TARGET) $(BUILD_DIR)/roi_pooling_op_gpu.cu.o 
	$(CXX) -std=c++11 -shared -o $(ROIPOOL_LIB) $(SRC_DIR)/roi_pooling_op.cc \
		$(BUILD_DIR)/roi_pooling_op_gpu.cu.o $(INCLDIRS) -I $(TF_INCL) -fPIC -L /usr/local/cuda/lib64 \
		-lcudart -L$(TF_LIB) -ltensorflow_framework -D_GLIBCXX_USE_CXX11_ABI=0 

clean:
	rm -rf $(BUILD_DIR)

