FROM jupyter/minimal-notebook

MAINTAINER Thrive Project <office@thrive.to>

USER root

# R pre-requisites
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    fonts-dejavu \
    gfortran \
    gcc \
    libopenblas-base \
    libxml2-dev \
    libxslt1-dev \
    libav-tools && apt-get clean

USER jovyan

# Install Python 3 packages
RUN conda install --quiet --yes \
        'ipywidgets=4.0.3*' \
        'pandas=0.17*' \
        'numexpr=2.5*' \
        'matplotlib=1.5*' \
        'scipy=0.17*' \
        'seaborn=0.7*' \
        'scikit-learn=0.17*' \
        'scikit-image=0.11*' \
        'sympy=0.7*' \
        'cython=0.23*' \
        'patsy=0.4*' \
        'statsmodels=0.6*' \
        'cloudpickle=0.1*' \
        'dill=0.2*' \
        'numba=0.23*' \
        'bokeh=0.11*' \
        'h5py=2.5*' \
        && conda clean -yt

# disabled since we are using old 4.03 version - no extensions working atm RUN jupyter nbextension enable --py widgetsnbextension --sys-prefix

#Install Thrive specific packages
RUN conda install  --yes -c https://conda.anaconda.org/melund toyplot \
    && conda install -c https://conda.binstar.org/pymc pymc \
    && conda install  --yes -c https://conda.anaconda.org/r rpy2 \
    && conda clean -yt
RUN pip install beautifulsoup4
RUN pip install scikits.bootstrap
RUN pip install plotly
# R packages including IRKernel which gets installed globally.
RUN conda config --add channels r
RUN conda install --yes \
    'rpy2=2.7*' \
    'r-base=3.2*' \
    'r-irkernel=0.5*' \
    'r-plyr=1.8*' \
    'r-devtools=1.9*' \
    'r-dplyr=0.4*' \
    'r-ggplot2=1.0*' \
    'r-tidyr=0.3*' \
    'r-shiny=0.12*' \
    'r-rmarkdown=0.8*' \
    'r-forecast=5.8*' \
    'r-stringr=0.6*' \
    'r-rsqlite=1.0*' \
    'r-reshape2=1.4*' \
    'r-nycflights13=0.1*' \
    'r-caret=6.0*' \
    'r-rcurl=1.95*' \
    'r-data.table=1.9*' \
    'r-randomforest=4.6*' && conda clean -yt
#install r specific packages
#setup R configs
RUN echo "r <- getOption('repos'); r['CRAN'] <- 'http://cran.r-project.org'; options(repos = r);" > ~/.Rprofile
RUN Rscript -e "install.packages('effects')"
RUN Rscript -e "install.packages('e1071')"
RUN Rscript -e "install.packages('cluster')"
RUN Rscript -e "install.packages('vcd')"

#install tensorflow
RUN pip install  --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.9.0rc0-cp35-cp35m-linux_x86_64.whl

USER root
COPY jupyter_notebook_config.py /home/jovyan/.jupyter/
RUN chown -R jovyan:users /home/jovyan/.jupyter

USER jovyan
#copy our files
ADD fig_code /home/jovyan/work/fig_code
