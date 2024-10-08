# A series of deep learning exercises and examples
GPU or TPU acceleration is a key step for getting fast enough feedback to be able to run experiments.
WSL with Ubuntu 24.04 could be an ideal environment for development, so the following snippets are aiming to set these up.

# CUDA and cuDNN install for WSL2
Download and install the folllowing packages
```shell
wget https://developer.download.nvidia.com/compute/cuda/repos/wsl-ubuntu/x86_64/cuda-wsl-ubuntu.pin
sudo mv cuda-wsl-ubuntu.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.6.1/local_installers/cuda-repo-wsl-ubuntu-12-6-local_12.6.1-1_amd64.deb
sudo dpkg -i cuda-repo-wsl-ubuntu-12-6-local_12.6.1-1_amd64.deb
sudo cp /var/cuda-repo-wsl-ubuntu-12-6-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-6
```

```shell
wget https://developer.download.nvidia.com/compute/cudnn/9.4.0/local_installers/cudnn-local-repo-ubuntu2404-9.4.0_1.0-1_amd64.deb
sudo dpkg -i cudnn-local-repo-ubuntu2404-9.4.0_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2404-9.4.0/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cudnn
```

# Appropriate Python version with [`pyenv`](https://github.com/pyenv/pyenv)
KerasNLP and some Tensorflow libraries **only** support Python 3.9.
```shell
pyenv install 3.9
pyenv global 3.9
python -m venv .venv
```

Install all dependencies
```shell
.venv/bin/pip install -r ./requirements.txt
```

# Verifying installation
## A simple way to check CUDA and cuDNN with Python using JAX
```python
import jax
jax.numpy.array(1.)
```

## cuDNN - troubleshooting
Check the library location on the machine
```shell
sudo find / -name 'libcudnn.so.*'
```

In some cases you need to set the `LD_LIBRARY_PATH`
```shell
export LD_LIBRARY_PATH=/usr/lib/x86_64-linux-gnu/libcudnn.so
```

Testing with `nvcc`
```shell
sudo apt install nvidia-cuda-toolkit
```
```shell
nvcc -V
```

## References
Packages and installation steps
- https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=WSL-Ubuntu&target_version=2.0&target_type=deb_local
- https://developer.nvidia.com/cudnn-downloads

CUDA on WSL2 full guide
- https://docs.nvidia.com/cuda/wsl-user-guide/index.html#getting-started-with-cuda-on-wsl-2

cuDNN guide
- https://docs.nvidia.com/deeplearning/cudnn/latest/installation/linux.html

Python integration snippets
- https://stackoverflow.com/questions/76229252/cudnn-error-when-running-jax-on-gpu-with-apptainer

JAX installation guide with local CUDA and cuDNN
- https://jax.readthedocs.io/en/latest/installation.html

# Docker on WSL2
Docker on WSL is required to use the CUDA container toolkit. For WSL the headless installation is preferrable over Docker Desktop.
Installation steps

```shell
curl -fsSL https://get.docker.com | bash
dockerd-rootless-setuptool.sh install
sudo apt-get install -y uidmap
```

Running docker rootless
```shell
PATH=/usr/bin:/sbin:/usr/sbin:$PATH dockerd-rootless.sh &
```

## References
- https://learn.microsoft.com/en-us/windows/wsl/tutorials/gpu-compute
- https://nickjanetakis.com/blog/install-docker-in-wsl-2-without-docker-desktop

# CUDA container toolkit
If Python libraries are not working out-of-the box - it could be a good alternative to verify if your system is working properly.
```shell
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
sudo apt-get update
sudo apt-get install -y nvidia-container-toolkit
```

Testing CUDA libraries with Docker
```shell
docker run --rm -it --gpus=all nvcr.io/nvidia/k8s/cuda-sample:nbody nbody -gpu -benchmark
```

## References
CUDA container toolkit
- https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html#installing-the-nvidia-container-toolkit

Docker GPU support
- https://docs.docker.com/desktop/gpu/

# Papers and Trainings
- https://www.techbooky.com/wp-content/uploads/2019/02/Better-Language-Models-and-Their-Implications.pdf
- https://arxiv.org/pdf/1706.03762
- https://stanford-cs324.github.io/winter2022/lectures/introduction/
- [The Illustrated GPT2](https://jalammar.github.io/illustrated-gpt2/)
- [The Illustrated Transformer](https://jalammar.github.io/illustrated-transformer/)
- [Sigmoid Visualization](https://jalammar.github.io/feedforward-neural-networks-visual-interactive/#sigmoid-visualization)
- [Google Cloud - intro to TPU](https://cloud.google.com/tpu/docs/intro-to-tpu)
- https://nlp.stanford.edu/projects/glove/
- [Special tokens](https://medium.com/@alexkubiesa/special-tokens-in-tensorflow-3c7718dcb0ef)
- [Hugging Face Course](https://huggingface.co/learn/nlp-course/chapter1/1)
- [Hugging Face tokenizers](https://github.com/huggingface/tokenizers)
- [RNN with description and tutorial](https://www.tensorflow.org/text/tutorials/text_generation)
- [How to fix the tutorial above?](https://github.com/tensorflow/text/issues/1268)
- [Another RNN](https://github.com/trekhleb/machine-learning-experiments/blob/master/experiments/text_generation_shakespeare_rnn/text_generation_shakespeare_rnn.ipynb)
- [RNN Guide](https://karpathy.github.io/2015/05/21/rnn-effectiveness/)
- [Tensorflow Keras RNN guide](https://www.tensorflow.org/guide/keras/working_with_rnns#introduction)
- [Paper (LSTMs single/multi-layered)](https://pdf.sciencedirectassets.com/280203/1-s2.0-S1877050918X0012X/1-s2.0-S187705091831439X/main.pdf)
- [Backpropagation](https://www.youtube.com/watch?v=Ilg3gGewQ5U)
- [Softmax](https://www.singlestore.com/blog/a-guide-to-softmax-activation-function/)

## Classification
- [BERT](https://www.tensorflow.org/text/tutorials/classify_text_with_bert)
- [Keras from scratch with 1D convolutional](https://keras.io/examples/nlp/text_classification_from_scratch/)
- [with RNN](https://www.tensorflow.org/text/tutorials/text_classification_rnn)

# Code Gemma
## Getting credentials
Visit the model at Kaggle.com and accept terms & conditions
https://www.kaggle.com/models/google/gemma?postConsentAction=explore

# Speeding things up
https://www.reddit.com/r/LocalLLaMA/comments/1b0kht9/gemma_finetuning_243_faster_uses_58_less_vram/
https://colab.research.google.com/drive/1vIrqH5uYDQwsJ4-OO3DErvuv4pBgVwk4?usp=sharing#scrollTo=QmUBVEnvCDJv

## TensorRT
https://docs.nvidia.com/deeplearning/tensorrt/archives/index.html#trt_7


# Google CoLab
- [Debugging](https://zohaib.me/debugging-in-google-collab-notebook/)

# Tools
- [What a good UI!](https://trekhleb.dev/machine-learning-experiments/#/experiments/TextGenerationShakespeareRNN)