VERSION?=v3
MODEL_CKPT=$(MODEL_NAME)

# Inception model names as defined in https://github.com/tensorflow/models/blob/master/research/slim/nets/nets_factory.py
MODEL_NAME=inception_$(VERSION)

ifeq ($(VERSION),v1)
IMGSIZE=224
MODEL_CKPT_TAR=$(MODEL_CKPT)_2016_08_28.tar.gz
OUTPUT_NODE_NAME=InceptionV1/Logits/Predictions/Reshape_1
else ifeq ($(VERSION),v2)
IMGSIZE=224
MODEL_CKPT_TAR=$(MODEL_CKPT)_2016_08_28.tar.gz
OUTPUT_NODE_NAME=InceptionV2/Predictions/Reshape_1
else ifeq ($(VERSION),v3)
IMGSIZE=299
MODEL_CKPT_TAR=$(MODEL_CKPT)_2016_08_28.tar.gz
OUTPUT_NODE_NAME=InceptionV3/Predictions/Reshape_1
else ifeq ($(VERSION),v4)
IMGSIZE=299
MODEL_CKPT_TAR=$(MODEL_CKPT)_2016_09_09.tar.gz
OUTPUT_NODE_NAME=InceptionV4/Logits/Predictions
endif

PROJECT_DIR = $(CURDIR)
ZOO_RELATIVE_TF_SRC_PROJ = ../tf_src
ZOO_RELATIVE_TF_MODELS_PROJ = ../tf_models
ZOO_TF_SRC_PATH = ${PROJECT_DIR}/${ZOO_RELATIVE_TF_SRC_PROJ}/tensorflow
ZOO_TF_MODELS_PATH = ${PROJECT_DIR}/${ZOO_RELATIVE_TF_MODELS_PROJ}/models

YELLOW='\033[1;33m'
NOCOLOR='\033[0m'

.PHONY: all
all: profile

# Checks if TF_MODELS_PATH is set.  If it isn't set then will make the
# tf_models project in the ncappzoo/tensorflow/tf_models directory.
check_model:
ifndef TF_MODELS_PATH
	@echo "TF_MODELS_PATH not set, making tf_models";
	(cd ../tf_models; make all; cd ${PROJECT_DIR})
	$(eval TF_MODELS_PATH = ${ZOO_TF_MODELS_PATH})
	@echo "TF_MODELS_PATH is ${TF_MODELS_PATH}" ;
else
	@echo "TF_MODELS_PATH is ${TF_MODELS_PATH}" ;
endif

# Checks if TF_SRC_PATH is set.  If it isn't set then will make the
# tf_models project in the ncappzoo/tensorflow/tf_src directory.
check_tf:
ifndef TF_SRC_PATH
	@echo "TF_SRC_PATH not set, making tf_src";
	(cd ../tf_src; make all; cd ${PROJECT_DIR})
	$(eval TF_SRC_PATH=${ZOO_TF_SRC_PATH})
	@echo "TF_SRC_PATH is ${TF_SRC_PATH}" ;
else
	@echo "TF_SRC_PATH is ${TF_SRC_PATH}" ;
endif


.PHONY: checkpoint
checkpoint:
	@echo $(YELLOW)"\nDownloading checkpoint files..."$(NOCOLOR)
	(mkdir -p model/$(VERSION))
	(cd model/$(VERSION); wget -nc http://download.tensorflow.org/models/$(MODEL_CKPT_TAR);)
	(cd model/$(VERSION); tar -xvf $(MODEL_CKPT_TAR);)

.PHONY: export
export: check_model
	@echo $(YELLOW)"\nExporting GraphDef file..."$(NOCOLOR)
	(cd model/$(VERSION); python3 $(TF_MODELS_PATH)/research/slim/export_inference_graph.py \
		--alsologtostderr \
		--model_name=$(MODEL_NAME) \
		--batch_size=1 \
		--dataset_name=imagenet \
		--image_size=$(IMGSIZE) \
		--output_file=$(MODEL_CKPT).pb;)

.PHONY: freeze
freeze: check_tf checkpoint export
	@echo $(YELLOW)"\nFreezing model for inference..."$(NOCOLOR)
	(cd model/$(VERSION); python3 $(TF_SRC_PATH)/tensorflow/python/tools/freeze_graph.py \
		--input_graph=$(MODEL_CKPT).pb \
		--input_binary=true \
		--input_checkpoint=$(MODEL_CKPT).ckpt \
		--output_graph=$(MODEL_CKPT)_frozen.pb \
		--output_node_name=$(OUTPUT_NODE_NAME);)

.PHONY: compile
compile: freeze
	@echo $(YELLOW)"\nCompiling model to TensorRT graph..."$(NOCOLOR)
	(python3 convert_plan.py ./model/$(VERSION)/$(MODEL_CKPT)_frozen.pb ./model/$(VERSION)/$(MODEL_CKPT).plan input $(IMGSIZE) $(IMGSIZE) $(OUTPUT_NODE_NAME) 1 0 float)
	@echo $(YELLOW)"TenroRT graph generated! You can run inferences using ncappzoo/apps/image-classifier project.\n"$(NOCOLOR)

INPUT?=../data/pic_002.jpg
.PHONY: infer
infer:
	@echo $(YELLOW)"\nRunning inferences on $(INPUT)""..."$(NOCOLOR)
	(./build/classify_image/classify_image $(INPUT) ./model/$(VERSION)/$(MODEL_CKPT).plan ./model/$(VERSION)/labels.txt input $(OUTPUT_NODE_NAME) inception)

.PHONY: help
help:
	@echo $(YELLOW)"\nPossible make targets: "$(NOCOLOR);
	@echo "  make help - Shows this message.";
	@echo "  make all - Builds all dependencies, but does not run this program.";
	@echo "  make checkpoint - Downloads pre-trained checkpoint files."
	@echo "  make clean - Removes all files created in this project."
	@echo "  make export - Export the neural network model for inference.";
	@echo "  make freeze - Freeze the neural network model for inference.";
	@echo "  make compile - Convert the frozen model into TensorRT graph file.";
	@echo "  make infer - Run a single image classification.";

.PHONY: clean
clean:
	@echo $(YELLOW)"\nMaking clean..."$(NOCOLOR);
	rm -rf model
