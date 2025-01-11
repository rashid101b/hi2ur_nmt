OpenNMT-py
OpenNMT-py is a toolkit to train Neural Machine Translation.

Installation
Install OpenNMT-py from pip to Ubuntu 18.04 64-bit OS

A ) Setup and Traning
Step 0: Install OpenNMT-py
pip install --upgrade pip
pip install OpenNMT-py

Step 1: Prepare the data
To get started, we propose to download a toy Hindi-Urdu dataset for machine translation containing 10k tokenized sentences:
wget <URL>toy-ende.tar.gz
tar xf toy-ende.tar.gz
cd toy-ende
The data consists of parallel source (src) and target (tgt) data containing one sentence per line with tokens separated by a space:
src-train.txt
tgt-train.txt
src-val.txt
tgt-val.txt
Validation files are used to evaluate the convergence of the training.

Step 2: Build Vocab

We need to build a YAML configuration file to specify the data that will be used:
======================
# toy_en_de.yaml to build vocab
## Where the samples will be written
save_data: toy-ende/run/example
## Where the vocab(s) will be written
src_vocab: toy-ende/run/example.vocab.src
tgt_vocab: toy-ende/run/example.vocab.tgt
# Prevent overwriting existing files in the folder
overwrite: False
# Corpus opts:
data:
corpus_1:
path_src: toy-ende/src-train.txt
path_tgt: toy-ende/tgt-train.txt
valid:
path_src: toy-ende/src-val.txt
path_tgt: toy-ende/tgt-val.txt
=====================
From this configuration, we can build the vocab(s), that will be necessary to train the model:
onmt_build_vocab -config vocab_toy_en_de.yaml -n_sample 10000

Step 3: Train the model
To train a model, we need to add the following to the YAML configuration file:
the vocabulary path(s) that will be used: can be that generated by onmt_build_vocab;

training specific parameters.
=======================
# toy_en_de.yaml for training NMT

# Vocabulary files that were just created
src_vocab: toy-ende/run/example.vocab.src
tgt_vocab: toy-ende/run/example.vocab.tgt

# Train on a single CPU
world_size: 1

# Train on a single GPU
# gpu_ranks: [0]

# Where to save the checkpoints
save_model: toy-ende/run/model
save_checkpoint_steps: 500
train_steps: 1000
valid_steps: 500
=======================

Then you can simply run:
onmt_train -config train_toy_en_de.yaml

This configuration will run the default model, which consists of a 2-layer LSTM with 500 hidden units on both the encoder and decoder. It will run on a single GPU (world_size 1 & gpu_ranks [0]).

Step 3: Translate
onmt_translate -model toy-ende/run/model_step_1000.pt -src toy-ende/src-test.txt -output toy-ende/pred_text.txt -verbose

Now you have a model which you can use to predict on new data. We do this by running beam search. This will output predictions into toy-ende/pred_text.txt.

=====================
B) ERROR Analysis

Input :

Output: 
