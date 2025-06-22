FROM r-base:latest

# Set compiler settings
RUN mkdir $HOME/.R/
RUN echo "CXX17FLAGS=-O3 -march=native -mtune=native -fPIC" >> $HOME/.R/Makevars
RUN echo "CXX17=g++" >> $HOME/.R/Makevars

# Set R settings
RUN echo "options(mc.cores =  4)" >> $HOME/.Rprofile
RUN echo "Sys.setenv(MAKEFLAGS = '-j4')" >> $HOME/.Rprofile

# Add a user named codespace with home directory and bash shell
RUN useradd -m -s /bin/bash codespace

RUN apt-get update && apt-get install -y sudo \
    && echo "codespace ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers.d/nopasswd \
    && chmod 0440 /etc/sudoers.d/nopasswd

# Install system dependencies
RUN apt-get update -qq 
RUN apt-get -y install apt-utils libcurl4-openssl-dev libv8-dev libtbb-dev \
    build-essential python3 python3-pip pipx python3-dev libxml2-dev libfontconfig1-dev libcairo2-dev libtiff-dev git htop


# Install RStan
RUN R -e "install.packages('rstan', repos = c('https://stan-dev.r-universe.dev', getOption('repos')))"

# Install other R packages
RUN install2.r --error \
    coda \
    mvtnorm \
    remotes \
    dagitty \
    shape \
    languageserver \
    httpgd \
    brms

# Install R packages cmdstanr and rethinking from GitHub
RUN R -e "remotes::install_github('stan-dev/cmdstanr')"
RUN R -e "cmdstanr::install_cmdstan(cores = 4)"
RUN R -e "remotes::install_github('rmcelreath/rethinking')"

# Install Python package Radian (to use instead of std R console)
RUN pipx install radian
RUN pipx ensurepath

RUN echo "alias r='radian'" >> $HOME/.bashrc
USER codespace
