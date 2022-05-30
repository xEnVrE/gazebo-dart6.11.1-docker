FROM ghcr.io/xenvre/gazebo-dart6.11.1-docker

# Use /bin/bash instead of /bin/sh
SHELL ["/bin/bash", "-c"]

# Non-interactive installation mode
ENV DEBIAN_FRONTEND=noninteractive

# Switch user
USER user

# Clone the robotology superbuild
WORKDIR /home/user
RUN git clone https://github.com/robotology/robotology-superbuild && \
    cd robotology-superbuild && \
    git checkout v2022.02.1

# Install dependencies
USER root
RUN apt update && \
    bash /home/user/robotology-superbuild/scripts/install_apt_dependencies.sh

# Switch user
USER user

WORKDIR /home/user/robotology-superbuild
RUN source /home/user/gazebo/install/local_setup.bash && \
    git config --global user.name "user" && \
    git config --global user.email "user@mail.com" && \
    mkdir build && cd build && cmake -DROBOTOLOGY_USES_PYTHON=ON ../ && \
    make

# Configure emacs
RUN echo "(setq-default indent-tabs-mode nil)" >> /home/user/.emacs.el && \
    echo "(setq-default tab-width 4)" >> /home/user/.emacs.el && \
    echo "(setq make-backup-files nil)" >> /home/user/.emacs.el && \
    echo "(setq auto-save-default nil)" >> /home/user/.emacs.el && \
    echo "(setq c-default-style \"linux\"" >> /home/user/.emacs.el && \
    echo "      c-basic-offset 4)" >> /home/user/.emacs.el && \
    echo "(global-subword-mode 1)" >> /home/user/.emacs.el && \
    echo "(add-hook 'before-save-hook 'delete-trailing-whitespace)" >> /home/user/.emacs.el && \
    echo "(custom-set-variables '(custom-enabled-themes '(tango-dark)))" >> /home/user/.emacs.el && \
    echo "(custom-set-faces)" >> /home/user/.emacs.elx

# Setup robotology superbuild
RUN echo "source /home/user/robotology-superbuild/build/install/share/robotology-superbuild/setup.sh" >> ~/.bashrc

# Launch bash from /home/user
WORKDIR /home/user
CMD ["bash"]
