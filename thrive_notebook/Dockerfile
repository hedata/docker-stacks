FROM jupyter/minimal-notebook

MAINTAINER Thrive Project <office@thrive.to>

USER root

# R pre-requisites
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    fonts-dejavu \
    gfortran \
    gcc \
    libopenblas-base && apt-get clean

USER jovyan

# Install Python 3 packages
RUN conda install --yes \
    'ipywidgets=4.0*' \
    'pandas=0.17*' \
    'matplotlib=1.4*' \
    'scipy=0.16*' \
    'seaborn=0.6*' \
    'scikit-learn=0.16*' \
    'scikit-image=0.11*' \
    'sympy=0.7*' \
    'cython=0.22*' \
    'patsy=0.4*' \
    'statsmodels=0.6*' \
    'cloudpickle=0.1*' \
    'dill=0.2*' \
    'numba=0.22*' \
    'bokeh=0.10*' \
    'nose=1.3*'  \
    && conda clean -yt
#Install Thrive specific packages
RUN conda install  --yes -c https://conda.anaconda.org/melund toyplot \
    && conda install  --yes -c https://conda.anaconda.org/pymc pymc \
    && conda install  --yes -c https://conda.anaconda.org/r rpy2 \
    && conda install --yes -c https://conda.anaconda.org/asmeurer beautiful-soup \
    && conda clean -yt
RUN pip install scikits.bootstrap
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

USER root
COPY jupyter_notebook_config.py /home/jovyan/.jupyter/
RUN chown -R jovyan:users /home/jovyan/.jupyter

USER jovyan
#copy our files
ADD fig_code /home/jovyan/work/fig_code
