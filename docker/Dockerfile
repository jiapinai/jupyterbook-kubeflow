FROM nvidia/cuda:9.2-cudnn7-devel

USER root
ENV DEBIAN_FRONTEND=noninteractive \
	LANG=C.UTF-8 \
	LC_ALL=C.UTF-8 \
	PATH=/opt/conda/bin:$PATH \
	NOTEBOOK_DIR=/home/jovyan \
	NOTEBOOK_IP=0.0.0.0 \
	NOTEBOOK_PORT=8888 \
	NB_PREFIX=''


RUN apt-get update -qq \
 && apt-get upgrade -y \
 && apt-get install --no-install-recommends -y \
	curl grep sed dpkg wget bzip2 ca-certificates vim \
	libglib2.0-0 libxext6 libsm6 libxrender1 \
	git mercurial subversion \
	# opencv3
	libgtk2.0-0 \
# anaconda install
 && echo 'export PATH=/opt/conda/bin:$PATH' > /etc/profile.d/conda.sh \
 #&& ANACONDA_INSTALL_SCRIPT=`curl https://repo.continuum.io/archive/ | grep '>Anaconda3.*-Linux-x86_64.sh<' | sed -e 's/.*>\(.*.sh\)<\/.*/\1/g' | sort -r | head -1` \
 && ANACONDA_INSTALL_SCRIPT='Anaconda3-5.2.0-Linux-x86_64.sh' \
 && wget --quiet https://repo.continuum.io/archive/$ANACONDA_INSTALL_SCRIPT -O ~/anaconda.sh \
 && /bin/bash ~/anaconda.sh -b -p /opt/conda \
 && rm ~/anaconda.sh \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*


# ModuleNotFoundError: No module named 'conda'
# https://github.com/conda/conda/issues/9004
# conda install conda=4.6.14
# conda update --all
RUN conda --version && conda update --all && conda --version && conda install -y --quiet \
	jupyter \
	Pillow \
 && conda config --add channels conda-forge \
	# opencv3 install
 && conda install -c menpo opencv3 \
 && conda clean -tipsy

# Install tensorflow-gpu
RUN pip install apache-beam tensorflow-gpu==1.14.0

# tini install
ENV TINI_VERSION=v0.18.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /usr/bin/tini
RUN chmod +x /usr/bin/tini

# pytorch install
RUN conda install -y --quiet \
	numpy pyyaml mkl mkl-include setuptools cmake cffi typing \
 && conda install -y --quiet -c mingfeima \
	mkldnn \
 && conda install -y --quiet -c pytorch \
	torchvision \
	magma-cuda91 \
	cuda91 \
	'pytorch=1.4.0'

# Install Kale python package globally
# Install Kale JupyterLab extension
# pip install kfp --upgrade --user
# conda install -c conda-forge nodejs
RUN     conda install -y --quiet -c conda-forge nodejs && \
	echo 'python' && python --version && \
	echo 'conda' && conda --version && \
	echo 'jupyter' && jupyter --version && \
	echo 'node' && node --version && \
	echo 'npm' && npm --version && \
	echo 'notebook' && jupyter notebook --version && \
	pip install kubeflow-kale kfp

#Jupyter Notebook代码自动补全
# -i https://pypi.mirrors.ustc.edu.cn/simple
RUN pip install  \
	jupyter_contrib_nbextensions jupyter_nbextensions_configurator autopep8 && \
	jupyter contrib nbextension install && \
	jupyter nbextensions_configurator enable
ADD conf/notebook.json /root/.jupyter/nbconfig/notebook.json

RUN pip install jupyterlab==1.2.6 && \
	jupyter labextension install kubeflow-kale-launcher

ENTRYPOINT [ "/usr/bin/tini", "--" ]

# https://www.kubeflow.org/docs/notebooks/custom-notebook/
CMD jupyter notebook \
	--config=/root/.jupyter/jupyter_notebook_config.json \
	--notebook-dir=${NOTEBOOK_DIR} \
	--ip=${NOTEBOOK_IP} \
	--port=${NOTEBOOK_PORT} \
	--NotebookApp.token='' \
	--NotebookApp.password='' \
	--NotebookApp.allow_origin='*' \
	--NotebookApp.base_url=${NB_PREFIX} \
	--no-browser \
	--allow-root
