################################################################################
# Dockerfile that builds 'yanwk/comfyui-boot:cu121-megapak'
# A streamlined version using PyTorch's official CUDA 12.1 image.
################################################################################

FROM pytorch/pytorch:2.1.2-cuda12.1-cudnn8-devel

LABEL maintainer="Icedcocon <code@icedcocon.fun>"

# Set the environment variable to avoid timezone prompt
ENV DEBIAN_FRONTEND=noninteractive

# Install tzdata and set timezone to Shanghai non-interactively
RUN apt-get update && apt-get install -y tzdata \
    && ln -fs /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && dpkg-reconfigure --frontend noninteractive tzdata


################################################################################
# System utilities and dependencies

RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    vim \
    ninja-build \
    aria2 \
    ffmpeg \
    libgl1-mesa-glx \
    libglib2.0-0 \
    curl \
    && rm -rf /var/lib/apt/lists/*

################################################################################
# Python Packages

# Upgrade pip, setuptools, and install the required Python packages
RUN pip install --upgrade pip wheel setuptools \
    && pip install \
        xformers==0.0.20 \
        torchvision==0.16.2 \
        torchaudio==2.1.2 \
        beautifulsoup4 \
        GitPython \
        joblib \
        matplotlib \
        numba \
        numpy \
        onnx \
        opencv-python-headless \
        pandas \
        rich \
        scikit-image \
        scikit-learn \
        scipy \
        tqdm \
        pycparser \
        python-etcd \
        pyOpenSSL \
        PySocks \
        requests \
        soupsieve \
        sympy \
        typing_extensions \
        --no-deps

# Install EchoMimic requirement.

RUN --mount=type=cache,target=/root/.cache/pip \
    pip list \
    && pip install --upgrade pip wheel setuptools --root-user-action=ignore \
    && curl -o requirements.txt https://raw.githubusercontent.com/smthemex/ComfyUI_EchoMimic/main/requirements.txt \
    && pip install -r requirements.txt --no-cache-dir \
    && rm -f requirements.txt \
    && pip install opencv-python facenet_pytorch --no-cache-dir --no-deps \
    && pip list

################################################################################
# Copy scripts

# # Install additional dependencies for ComfyUI and custom nodes

COPY runner-scripts/.  /runner-scripts/

COPY builder-scripts/generate-pak5.sh  /builder-scripts/generate-pak5.sh
COPY builder-scripts/pak3.txt  /builder-scripts/pak3.txt

RUN pip install -r /builder-scripts/pak3.txt

COPY builder-scripts/pak5.txt  /builder-scripts/pak5.txt

RUN pip install -r /builder-scripts/pak5.txt

COPY builder-scripts/pak7.txt  /builder-scripts/pak7.txt

RUN pip install -r /builder-scripts/pak7.txt

RUN pip uninstall --yes onnxruntime-gpu \
    && pip --no-cache-dir install onnxruntime-gpu \
        --index-url https://aiinfra.pkgs.visualstudio.com/PublicPackages/_packaging/onnxruntime-cuda-12/pypi/simple/ \
    && pip install mediapipe \
    && pip list

COPY builder-scripts/pak9.txt  /builder-scripts/pak9.txt

RUN pip install -r /builder-scripts/pak9.txt


################################################################################

# Clean up any unnecessary files to reduce image size
RUN rm -rf /root/.cache /tmp/* /var/tmp/*

# Set up working environment
USER root
VOLUME /root
WORKDIR /root
EXPOSE 8188
ENV CLI_ARGS=""
CMD ["bash","/runner-scripts/entrypoint.sh"]
