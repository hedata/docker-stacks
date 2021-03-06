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
RUN pip install --upgrade plotly==1.9.6
RUN pip install colorlover
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
RUN pip install  --ignore-installed --upgrade https://storage.googleapis.com/tensorflow/linux/cpu/tensorflow-0.10.0rc0-cp35-cp35m-linux_x86_64.whl

#fix r fun
RUN echo "jpeg 8*" >> /opt/conda/conda-meta/pinned
RUN conda update --all -y

USER root

# Spark dependencies
ENV APACHE_SPARK_VERSION 1.6.1
RUN apt-get -y update && \
    apt-get install -y --no-install-recommends openjdk-7-jre-headless && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
RUN cd /tmp && \
        wget -q http://d3kbcqa49mib13.cloudfront.net/spark-${APACHE_SPARK_VERSION}-bin-hadoop2.6.tgz && \
        echo "09f3b50676abc9b3d1895773d18976953ee76945afa72fa57e6473ce4e215970 *spark-${APACHE_SPARK_VERSION}-bin-hadoop2.6.tgz" | sha256sum -c - && \
        tar xzf spark-${APACHE_SPARK_VERSION}-bin-hadoop2.6.tgz -C /usr/local && \
        rm spark-${APACHE_SPARK_VERSION}-bin-hadoop2.6.tgz
RUN cd /usr/local && ln -s spark-${APACHE_SPARK_VERSION}-bin-hadoop2.6 spark

# Mesos dependencies
# Currently, Mesos is not available from Debian Jessie.
# So, we are installing it from Debian Wheezy. Once it
# becomes available for Debian Jessie. We should switch
# over to using that instead.
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv E56151BF && \
    DISTRO=debian && \
    CODENAME=wheezy && \
    echo "deb http://repos.mesosphere.io/${DISTRO} ${CODENAME} main" > /etc/apt/sources.list.d/mesosphere.list && \
    apt-get -y update && \
    apt-get --no-install-recommends -y --force-yes install mesos=0.22.1-1.0.debian78 && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Spark and Mesos config
ENV SPARK_HOME /usr/local/spark
ENV PYTHONPATH $SPARK_HOME/python:$SPARK_HOME/python/lib/py4j-0.9-src.zip
ENV MESOS_NATIVE_LIBRARY /usr/local/lib/libmesos.so
ENV SPARK_OPTS --driver-java-options=-Xms1024M --driver-java-options=-Xmx4096M --driver-java-options=-Dlog4j.logLevel=info

# RSpark config
ENV R_LIBS_USER $SPARK_HOME/R/lib

COPY jupyter_notebook_config.py /home/jovyan/.jupyter/
RUN chown -R jovyan:users /home/jovyan/.jupyter

USER jovyan

# Apache Toree kernel
RUN pip --no-cache-dir install toree==0.1.0.dev7
RUN jupyter toree install --user

#copy our files
ADD fig_code /home/jovyan/work/fig_code
