# Copyright 2017 The Kubernetes Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

ifeq ($(REGISTRY),)
	REGISTRY = quay.io/external_storage/
endif
ifeq ($(VERSION),)
	VERSION = latest
endif
ifeq ($(GOOS),)
	GOOS = linux
endif
ifeq ($(GOARCH),)
	GOARCH = amd64
endif
IMAGE_CONTROLLER = $(REGISTRY)snapshot-controller:$(VERSION)
MUTABLE_IMAGE_CONTROLLER = $(REGISTRY)snapshot-controller:latest
IMAGE_PROVISIONER = $(REGISTRY)snapshot-provisioner:$(VERSION)
MUTABLE_IMAGE_PROVISIONER = $(REGISTRY)snapshot-provisioner:latest

.PHONY: all controller provisioner clean container container-quick push test

all: controller provisioner

controller:
	GOOS=$(GOOS) GOARCH=$(GOARCH) go build -o _output/bin/snapshot-controller-$(GOOS)-$(GOARCH) cmd/snapshot-controller/snapshot-controller.go

provisioner:
	GOOS=$(GOOS) GOARCH=$(GOARCH) go build -o _output/bin/snapshot-provisioner-$(GOOS)-$(GOARCH) cmd/snapshot-pv-provisioner/snapshot-pv-provisioner.go

clean:
	-rm -rf _output

test:
	go test `go list ./... | grep -v 'vendor'`

container: controller provisioner container-quick

container-quick:
	docker build -t $(MUTABLE_IMAGE_CONTROLLER) . -f Dockerfile.controller
	docker tag $(MUTABLE_IMAGE_CONTROLLER) $(IMAGE_CONTROLLER)
	docker build -t $(MUTABLE_IMAGE_PROVISIONER) . -f Dockerfile.provisioner
	docker tag $(MUTABLE_IMAGE_PROVISIONER) $(IMAGE_PROVISIONER)

push: container
	docker push $(IMAGE_CONTROLLER)
	docker push $(MUTABLE_IMAGE_CONTROLLER)
	docker push $(IMAGE_PROVISIONER)
	docker push $(MUTABLE_IMAGE_PROVISIONER)
