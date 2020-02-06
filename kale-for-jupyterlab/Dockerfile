FROM registry.cn-hangzhou.aliyuncs.com/kubeflow-ai/jupyterbook-kubeflow
ENV PUBLIC_URL='/nni/'
# https://github.com/kubeflow-kale/jupyterlab-kubeflow-kale
CMD jupyter lab \
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
