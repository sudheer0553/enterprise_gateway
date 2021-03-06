This image installs the Jupyter server extensions [NB2KG](https://github.com/jupyter-incubator/nb2kg) 
and [Jupyter Lab](https://github.com/jupyterlab/jupyterlab) 
 on top of image [jupyter/minimal-notebook](https://github.com/jupyter/docker-stacks/tree/master/minimal-notebook).

# What it Gives You
This image is configured to be run against [Jupyter Enterprise Gateway](http://jupyter-enterprise-gateway.readthedocs.io/en/latest/) 
instances, although running against Jupyter Kernel Gateway instances should be fine.  It enables the ability to target
either form of gateway using either `Jupyter Notebook` or `Jupyter Lab`.

It is built using the latest `minimal-notebook` image and includes the `Jupyter Lab` extension that can be optionally 
invoked.  The tag of the image corresponds to the `NB2KG` release that is included in the image.


# Basic Use
If the gateway server is remote, the following command can be used to direct NB2KG to that gateway...

`docker run -t --rm -p 8888:8888 -e GATEWAY_HOST=<gateway-hostname> -v <host-notebook-directory>:/home/jovyan/work elyra/nb2kg:<tag>`

This will configure the `KG_URL` to `http://${GATEWAY_HOST}:8888` with KERNEL_USERNAME set to `jovyan`.

# Alternative Uses
If you have an `elyra/enterprise-gateway` container running on the same host, or would like to run mulitple notebook instances against the same gateway, the ports of the NB2KG can be adjusted as follows:

`docker run -t --rm -p 9002:9002 -e NB_PORT=9002 -e GATEWAY_HOST=<gateway-hostname> -v <host-notebook-directory>:/home/jovyan/work elyra/nb2kg`

This maps port `9002` of the container to host port `9002` and instructs the container to use `9002` as the notebook port. The use of `-e NB_PORT` is only necessary if running on the same host as the gateway server. If that's not the, then only `-p 9002:8888` is required to support multiple NB2KG instances on the same host.

You can then run multiple notebook sessions on the same host by using different port mappings.  In addition, additional users can be emulated by setting the `KG_HTTP_USER` environment variable.

`docker run -t --rm -p 9003:8888 -e GATEWAY_HOST=<gateway-hostname> -e KG_HTTP_USER=bob -v <host-notebook-directory>:/home/jovyan/work elyra/nb2kg`

Here, the notebook will be available at port `9003` on the host and the `KERNEL_USERNAME` variable will be set to `bob`. Note that in this case, `-e NB_PORT` is not used since the gateway is not on the same host (in this example).

### Jupyter Lab 
The jupyter lab extension can be utilized by adding `lab` as the last argument to the `docker run` command.

`docker run -t --rm -p 8888:8888 -e GATEWAY_HOST=<gateway-hostname> -v <host-notebook-directory>:/home/jovyan/work elyra/nb2kg:<tag> lab`

To confirm the container is running as Jupyter Lab, the log output should be prefixed with a timestamp containing 
`LabApp` vs. `NotebookApp` for the default case.
```
[I 16:04:55.467 LabApp] The Jupyter Notebook is running at: http://0.0.0.0:8888/?token=209f7c7dfdfd9ebe9635ed5577a3df204b625a4040225b51
[I 16:04:55.468 LabApp] Use Control-C to stop this server and shut down all kernels (twice to skip confirmation).
```

**Note:** the container can be launched into the bash shell by providing a command entry that is neither `lab` nor 
`notebook` (the default).