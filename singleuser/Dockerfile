# Version: 20180223
ARG DOCKER_NOTEBOOK_IMAGE
FROM $DOCKER_NOTEBOOK_IMAGE
ARG JUPYTERHUB_VERSION

RUN python3 -m pip install --no-cache jupyterhub==$JUPYTERHUB_VERSION

USER root

ENV DEBIAN_FRONTEND noninteractive

WORKDIR /home/jovyan/work

RUN ln -sf /bin/bash /bin/sh && \
    ln -s /bin/tar /bin/gtar

# install jupyterlab, jupyter_client
RUN echo "jovyan ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    conda install -y jupyterlab jupyter_client && \
    jupyter serverextension enable --py jupyterlab --sys-prefix

# Code below based on https://github.com/jenkinsci/docker/issues/543
# For addressing errors: "E: Unable to fetch some archives", "Hash Sum mismatch"
RUN mkdir -p /etc/apt/apt.conf.d/
COPY 99fixbadproxy /etc/apt/apt.conf.d/

# libav-tools for matplotlib anim
RUN apt-get update && \
    apt-get install -y software-properties-common && \
    add-apt-repository -y ppa:opencpu/jq --yes && \
    add-apt-repository ppa:ubuntugis/ppa && \
    apt-get update && \
    apt-get install -y gdal-bin libav-tools libgeos-dev nano \
      libffi-dev g++ libssl-dev libfreetype6-dev \
      python-dev libxml2-dev libxslt1-dev antiword unrtf poppler-utils pstotext tesseract-ocr \
      flac ffmpeg lame libmad0 libsox-fmt-mp3 sox libjpeg-dev swig \
      aptitude libmariadb-client-lgpl-dev fonts-dejavu gfortran gcc \
      libcairo2 mesa-common-dev libglu1-mesa-dev libudunits2-dev libgdal-dev \
      libjq-dev libv8-3.14-dev libprotobuf-dev protobuf-compiler libproj-dev \
          libgeos++-dev zip unzip && \
    export LD_LIBRARY_PATH=/lib:/usr/lib:/usr/local/lib && \
    aptitude install -y libmagick++-dev && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    chmod -R a+w /opt/conda/ && \
    chown -R root:users /opt/conda && \
    chmod g+s /opt/conda

USER $NB_USER

# R packages
# install R libraries and pre-requisites
# These need to go together in conda install: 'libspatialite', 'r-rsqlite', 'r-rgdal'
RUN conda install --quiet --yes \
    'r-base=3.3.2' \
    'r-irkernel=0.7*' \
    'r-plyr=1.8*' \
    'r-devtools=1.12*' \
    'r-tidyverse=1.0*' \
    'r-shiny=0.14*' \
    'r-rmarkdown=1.2*' \
    'r-forecast=7.3*' \
    'r-reshape2=1.4*' \
    'r-nycflights13=0.2*' \
    'r-caret=6.0*' \
    'r-rcurl=1.95*' \
    'r-crayon=1.3*' \
    'r-randomforest=4.6*' && \
    conda install -c conda-forge --yes \
    'libspatialite' \
    'r-rsqlite' \
    'r-rgdal' \
    'r-sf' && \
    conda install -c conda-forge -c brown-data-science -c r -c blaze -c bioconda --yes \
    'r-reticulate' \
    'r-sp' \
    'r-e1071' \
    'r-rpart' \
    'r-xml' \
    'r-quantmod' \
    'r-rvest' \
    'r-maps' \
    'r-htmlwidgets' \
    'r-visnetwork' \
    'r-igraph ' \
    'r-leaflet' \
    'r-tm' && \
    conda clean -tipsy
# devtools install 'viridis' and 'cividis' to avoid
RUN R -e "install.packages(c('text2vec','crayon'), dependencies=TRUE, repos='http://cran.rstudio.com/')" && \
    R -e "devtools::install_github('sjmgarnier/viridis')" && \
    R -e "devtools::install_github('marcosci/cividis')" && \
    R -e "install.packages(c('raster','rgeos'), dependencies=TRUE, repos='http://cran.rstudio.com/')" && \
    R -e "install.packages(c('geojsonio','protolite'),repos='http://cran.rstudio.com')"

# Install Python 3 packages
# Remove pyqt and qt pulled in for matplotlib since we're only ever going to
# use notebook-friendly backends in these images
# Unversion 'hdf5' and 'h5py' for 'rgdal' compatibility
# Unversion 'matplotlib' for 'libkml', 'rgdal' and 'r-sf' compatibility
# Specify pyqt version to avoid two choices
USER $NB_USER
RUN conda install --quiet -c conda-forge --yes \
    'nomkl' \
    'ipywidgets=7.0*' \
    'pandas=0.19*' \
    'numexpr=2.6*' \
    'matplotlib' \
    'scipy=0.19*' \
    'seaborn=0.7*' \
    'scikit-learn=0.19*' \
    'scikit-image=0.13*' \
    'sympy=1.0*' \
    'cython=0.28*' \
    'patsy=0.5*' \
    'statsmodels=0.8*' \
    'cloudpickle=0.5*' \
    'dill=0.2*' \
    'numba=0.38*' \
    'bokeh=0.12.9' \
    'sqlalchemy=1.2*' \
    'pyqt=5.6*' \
    'hdf5=1.10*' \
    'h5py=2.7*' \
    'vincent=0.4.*' \
    'beautifulsoup4=4.6.*' \
    'protobuf=3.*' \
    'numpy' \
    'icu' \
    'missingno' \
    'blaze' \
    'pandas-datareader' \
    'wrapt' \
    'textblob' \
    'jupyter_contrib_nbextensions' \
    'xlrd' \
    'jupyter_kernel_gateway' && \
    conda install -c conda-forge -c ioam -c bioconda -c anaconda --yes \
    'rpy2' \
    'pyproj' \
    'pillow' \
    'fiona' \
    'rasterio' \
    'geojson' \
    'pyshp' \
    'shapely' \
    'obspy' \
    'geopandas' \
    'mplleaflet' \
    'plotly' \
    'folium' \
    'holoviews' \
    'geoviews' \
    'xarray' \
    'dask' \
    'pyke' \
    'netcdf4' \
    'cf_units' \
    'setuptools' \
    'bkcharts' \
    'basemap' \
    'basemap-data-hires' \
    'networkx' \
    'cartopy' \
    'proj4' \
    'datashader' \
    'openpyxl' \
    'bqplot' \
    'qgrid' && \
    pip install --upgrade pip && \
    pip install cufflinks sklearn-pandas epipy googletrans hyper fuzzywuzzy python-Levenshtein \
      jupyterlab_geojson jupyterlab_widgets altair==2.0.0rc1 bqplot pylantern
RUN jupyter nbextension install https://rawgit.com/jfbercher/jupyter_nbTranslate/master/nbTranslate.zip --user && \
    jupyter nbextension enable nbTranslate/main && \
    conda remove --quiet --yes --force qt pyqt && \
    conda clean -tipsy

# Activate ipywidgets extension in the environment that runs the notebook server\
USER root
RUN jupyter nbextension enable --py widgetsnbextension --sys-prefix
ENV XDG_CACHE_HOME /home/$NB_USER/.cache/

USER $NB_USER
RUN conda install conda-build && \
    conda clean -tipsy

COPY work .

RUN sudo chmod a+x modules/*.sh && \
    sudo mkdir -p /srv/modules && \
    sudo mv modules/* /srv/modules/. && \
    sudo chmod a+w -R /home/jovyan/work/shared && \
    sudo chmod a+w -R /home/jovyan/work/geoserver && \
    sudo chmod a+w -R /home/jovyan/work/notebooks && \
    sudo chmod a+w /home/jovyan/work/notebooks/*.ipynb && \
    sudo chown -R jovyan:users /home/jovyan && \
    sudo chown -R jovyan:users /home/jovyan/work/shared && \
    sudo chown -R jovyan:users /home/jovyan/work/geoserver && \
    sudo chown -R jovyan:users /home/jovyan/work/notebooks && \
    mkdir -p /home/jovyan/.cache && \
    chmod -R a+w /home/jovyan/.cache && \
    chown -R jovyan:users /home/jovyan/.cache

WORKDIR /home/jovyan/work

# Uncomment the following lines to load the Julia kernel and Python, R and Julia modules
# You can also load the following inside the runnng Notebook single-user container through terminal
#RUN /srv/modules/install-scijava-kernel.sh
#RUN /srv/modules/restore-tutorial.sh
#RUN /srv/modules/install-python2-kernel.sh
#RUN /srv/modules/install-julia-kernel.sh
#RUN /srv/modules/load-julia-modules.sh

# Custom logo, create backup CSS file to enable restore if jupyter-themes overwrites it and the GEEKS logo
ARG LOGO_IMAGE
RUN echo $LOGO_IMAGE && \
    mkdir -p /home/jovyan/.jupyter/custom
ADD css/custom.css /home/jovyan/.jupyter/custom/custom.css
ADD css/$LOGO_IMAGE /home/jovyan/.jupyter/custom/non-default.png
RUN sudo chown jovyan:users /home/jovyan/.jupyter/custom/non-default.png && \
    cp /home/jovyan/.jupyter/custom/custom.css /home/jovyan/.jupyter/custom/custom.css.backup

# Address iopub data error message
ADD jupyter_notebook_config.py /etc/jupyter/jupyter_notebook_config.py

USER root

RUN conda update jupyterlab && \
    jupyter labextension install @jupyterlab/geojson-extension && \
    jupyter labextension install @jupyterlab/plotly-extension && \
    jupyter labextension install @jupyterlab/vega3-extension && \
    jupyter labextension install @jupyter-widgets/jupyterlab-manager && \
    jupyter labextension install jupyterlab_bokeh && \
    jupyter labextension install qgrid && \
    jupyter labextension install @jpmorganchase/perspective-jupyterlab


# restore password for sudo access
RUN conda clean -tipsy && \
    apt-get update && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* && \
    sed -i 's/ NOPASSWD://g' /etc/sudoers
    #mkdir -p /home/jovyan/.jupyter/lab/user-settings/@jupyterlab/google-drive/

#COPY ./drive.jupyterlab-settings /home/jovyan/.jupyter/lab/user-settings/@jupyterlab/google-drive/drive.jupyterlab-settings

USER $NB_USER
