CUDA_VERSION = 10.2
OS_NAME = ubuntu
OS_VERSION = 16.04
PYTHON_VERSION = 3.8

.PHONY: base package runtime

all : runtime

runtime: package
	make -C runtime CUDA_VERSION=$(CUDA_VERSION) OS_NAME=$(OS_NAME) OS_VERSION=$(OS_VERSION) PYTHON_VERSION=$(PYTHON_VERSION)

package: base
	make -C package CUDA_VERSION=$(CUDA_VERSION) OS_NAME=$(OS_NAME) OS_VERSION=$(OS_VERSION) PYTHON_VERSION=$(PYTHON_VERSION)

base:
	make -C base CUDA_VERSION=$(CUDA_VERSION) OS_NAME=$(OS_NAME) OS_VERSION=$(OS_VERSION) PYTHON_VERSION=$(PYTHON_VERSION)
