CUDA_VERSION = 10.2
OS_NAME = ubuntu
OS_VERSION = 16.04
PYTHON_VERSION = 3.7

IMG_NAME := rannc_package_cuda$(CUDA_VERSION)-$(OS_NAME)$(OS_VERSION)-py$(PYTHON_VERSION)

BASE_IMG_ID = base_img_id
PKG_IMG_ID := pkg_img_id
CWD := $(dir $(abspath $(lastword $(MAKEFILE_LIST))))
BUILD_DIR := $(abspath $(CWD)/../../..)

define BUILD_CMD
 . /opt/conda/etc/profile.d/conda.sh \
 && conda activate rannc \
 && export CONDA_PREFIX=/opt/conda/envs/rannc \
 && export BOOST_DIR=/usr/include/boost \
 && cd /temp/pyrannc \
 && python setup.py clean build -j20 -g bdist_wheel
endef

define WHEEL_NAME_CMD
 . /opt/conda/etc/profile.d/conda.sh \
 && conda activate rannc \
 && cd /temp/pyrannc \
 && python setup.py -q bdist_wheel_name
endef


.PHONY: wheel_path clean

wheel_path: pkg_img_id
	docker run --rm -v $(BUILD_DIR):/temp/pyrannc $(IMG_NAME) /bin/bash -c "${BUILD_CMD}"
	$(eval WHEEL_NAME=$(shell docker run --rm -v $(BUILD_DIR):/temp/pyrannc $(IMG_NAME) /bin/bash -c "$(WHEEL_NAME_CMD)"))
	@echo $(BUILD_DIR)/dist/$(WHEEL_NAME).whl > wheel_path

pkg_img_id: ../base/$(BASE_IMG_ID) Dockerfile
	docker build -t $(IMG_NAME) \
		--build-arg CUDA_VERSION=$(CUDA_VERSION) \
		--build-arg OS_NAME=$(OS_NAME) \
		--build-arg OS_VERSION=$(OS_VERSION) \
		--build-arg PYTHON_VERSION=$(PYTHON_VERSION) \
	    .
	docker inspect -f "{{ .ID }}" $(IMG_NAME) > ./$(PKG_IMG_ID)

clean:
ifeq (,$(wildcard ./$(PKG_IMG_ID)))
	@echo "Nothing to clean."
else
	docker rmi $(shell cat ./$(PKG_IMG_ID))
	rm ./$(PKG_IMG_ID)
endif
