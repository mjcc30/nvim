# Docker file for base Nvcode image.
#
# @author Maxime Cordeiro

# from source :
# Git: https://github.com/git/git/archive/refs/tags/v2.31.1.tar.gz
# NodeJS: https://deb.nodesource.com/setup_current.x
# npm: https://www.npmjs.com/install.sh
# pyenv: https://pyenv.run
# pip: https://bootstrap.pypa.io/get-pip.py
# rust: https://static.rust-lang.org/rustup.sh
# Go https://dl.google.com/go/go1.14.2.linux-amd64.tar.gz
# tree-sitter: https://github.com/tree-sitter/tree-sitter
# lua http://www.lua.org/ftp/lua-5.3.5.tar.gz
# luarocks https://luarocks.org/releases/luarocks-3.3.1.tar.gz
# CPAN http://search.cpan.org/CPAN/authors/id/A/AN/ANDK/CPAN-2.28.tar.gziphttp://search.cpan.org/CPAN/authors/id/A/AN/ANDK/CPAN-2.28.tar.gz
# composer https://getcomposer.org/installer
# phpactor https://github.com/phpactor/phpactor.git
# neovim https://github.com/neovim/neovim
# nvcode https://github.com/ChristianChiarulli/nvcode.git


# Debian image as base (unstable for newest software).
FROM debian:sid

# Set image locale.
RUN apt-get clean && apt-get update && apt-get install -y locales dialog apt-utils
RUN locale-gen en_US.UTF-8 
     

# Set default timezone.
ENV TZ=Europe/Paris

# Set env Rust
ENV RUSTUP_HOME=/root/rustup \
    CARGO_HOME=/root/cargo \
    PATH=/root/cargo/bin:$PATH \
    RUST_SRC_PATH=/root/rustup/toolchains/stable-x86_64-unknown-linux-gnu/lib/rustlib/src/rust/src \
    USER=root

ENV NVCODE_BASE=/usr/local/share/nvcode
ENV NVCODE_USER=$HOME/.local/share/nvcode
ENV URL=https://github.com/ChristianChiarulli/nvcode.git

ENV SHELL=/bin/bash \
	NVCODE_CONFIG=${NVCODE_BASE}/nvim/init.lua \
	XDG_CONFIG_HOME=$HOME/.config/nvcode \
	XDG_DATA_HOME=${NVCODE_USER}/data \
	XDG_CACHE_HOME=${NVCODE_USER}/cache \
	XDG_CONFIG_DIRS=/usr/share:/usr/local/share:/usr/local/share/nvcode 

ENV DISPLAY=:0
# Expose some ports to host by default.
EXPOSE 8080 8081 8082 8083 8084 8085

# built-in packages
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update \
    && apt-get  upgrade -y \
    && apt-get autoclean -y \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/*

# Update repositories and install software:
# 1. wget
# 2. curl.
# 3. fzf for fast file search.
# 4. ripgrep for fast text occurrence search.
# 5. xclip for clipboard handling.
# 6. tzdata to set default container timezone
# 7. ranger
# 8. pandoc
# 9. golang
# 10. lua5.3
RUN apt-get -y install wget curl fzf ripgrep xclip xsel ruby neofetch tzdata ranger pandoc golang lua5.3 cmake make python g++ libtool-bin libgettextpo-dev zlib1g-dev libxtst-dev ninja-build  libjpeg-dev

# install Git from source
RUN apt-get install -y dh-autoreconf libcurl4-gnutls-dev libexpat1-dev \
  gettext libz-dev libssl-dev asciidoc xmlto docbook2x install-info 
RUN curl -sL https://github.com/git/git/archive/refs/tags/v2.31.1.tar.gz | tar zx \
	&& cd git-* \
	&& make configure \
	&& ./configure --prefix=/usr \
    && make all doc info \
    && make install install-doc install-html install-info

RUN git config --global --unset http.proxy

RUN echo "alias clipboard='xclip -sel clip'" >> ~/.bashrc && export DISPLAY=:0

# Install pyenv from source
# somtimes git clone crash 
RUN apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev llvm libncurses5-dev libncursesw5-dev \
xz-utils tk-dev libffi-dev liblzma-dev
RUN curl https://pyenv.run | bash 
	

# Install Pip from source
RUN apt-get -y install python3 python3-pip python3-testresources \
python3-apt python3-distutils python-dev python3-dev python3-venv python3-distutils \
    && curl -O https://bootstrap.pypa.io/get-pip.py \
    && python3 get-pip.py --user \
    && export PATH=.local/bin:$PATH \
    && pip install --upgrade pip \
    && rm get-pip.py

# install rust from sources 
RUN curl -f -L https://static.rust-lang.org/rustup.sh -O \
 && sh rustup.sh -y --no-modify-path --profile minimal \
 && rustup default stable \
 && rustup component add rustfmt \
 && rm rustup.sh

# Install NodeJS from source
RUN curl -sL https://deb.nodesource.com/setup_current.x | bash - \
    && apt-get -y install nodejs \
    && curl -qL https://www.npmjs.com/install.sh | sh
    # &&  npm install -g npm@7.8.0
# INSTALATION WORK BUT alacritty command NOT WORKING 
# install alacritty from source
# RUN apt-get install -y cmake pkg-config libfreetype6-dev libfontconfig1-dev libxcursor-dev \
# libxcb-xfixes0-dev libxi-dev 
# RUN git clone https://github.com/alacritty/alacritty.git \
#     && cd alacritty \
#     && cargo build --release \
#     && cd target/release \
#     && ln -s ~/alacritty/target/release/alacritty /usr/bin/alacritty \
# 	&& cd ../.. \
#     && mkdir -p /usr/local/share/man/man1 \
#     && gzip -c extra/alacritty.man | tee /usr/local/share/man/man1/alacritty.1.gz > /dev/null \
#     && mkdir -p ~/.bash_completion \
#     && cp extra/completions/alacritty.bash ~/.bash_completion/alacritty \
#     && echo "source ~/.bash_completion/alacritty" >> ~/.bashrc

# Install Go from sources
RUN wget -c https://dl.google.com/go/go1.14.2.linux-amd64.tar.gz -O - | tar -xz -C /usr/local \
    && echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc \
    && echo 'export PATH=/root/.local/bin:$PATH' >> ~/.bashrc

# Install tree-sitter from sources
RUN git clone https://github.com/tree-sitter/tree-sitter \
    && cd tree-sitter \
    && cargo build --release \
    && cd target/release \
    && ln -s tree-sitter /usr/bin/tree-sitter 

# Install lua from sources
RUN curl -R -O http://www.lua.org/ftp/lua-5.3.5.tar.gz \
    && tar -zxf lua-5.3.5.tar.gz \
    && cd lua-5.3.5 \
    && make linux test \
    && make install \
	&& cd .. && rm -f lua-5.3.5.tar.gz

# Install luarocks from source
RUN wget https://luarocks.org/releases/luarocks-3.3.1.tar.gz \
    && tar zxpf luarocks-3.3.1.tar.gz \
    && cd luarocks-3.3.1 \
    && ./configure --with-lua-include=/usr/local/include \
    && make install

# Install ruby
RUN apt-get install -y gem ruby-dev \
    && gem install neovim \
    && gem install bundler

# lua extrapackages for Neovim.
RUN luarocks install --server=https://luarocks.org/dev luaformatter

# Python 3 extrapackages for Neovim.
RUN pip3 install pynvim
RUN pip3 install neovim-remote
RUN pip3 install fd 
RUN pip3 install flake8
RUN pip3 install yapf
RUN pip3 install ueberzug
RUN pip3 install argparse 
RUN pip3 install ninja 

# install CPAN from source
RUN apt-get install -y perl \
    && wget http://search.cpan.org/CPAN/authors/id/A/AN/ANDK/CPAN-2.28.tar.gz  \
    && tar xfz CPAN-2.28.tar.gz \
    && cd CPAN-2.28 \
    && perl Makefile.PL \
    && make && make test \
    && make install 

# Install Dart
RUN apt-get install -y apt-transport-https \
    && sh -c 'wget -qO- https://dl-ssl.google.com/linux/linux_signing_key.pub | apt-key add -' \
    && sh -c 'wget -qO- https://storage.googleapis.com/download.dartlang.org/linux/debian/dart_stable.list > /etc/apt/sources.list.d/dart_stable.list' \
    && apt update \
    && apt install dart \
    && export PATH="$PATH:/usr/lib/dart/bin"

# Install phpactor and composer from source
RUN apt-get install -y php libapache2-mod-php php-mbstring php-xmlrpc php-soap php-gd php-xml php-cli php-zip php-curl
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/composer \
    && chmod +x /usr/local/bin/composer \
    && exec bash \
	&& composer -v
RUN git clone https://github.com/phpactor/phpactor.git \
    && cd phpactor \
    && composer update \
    && composer install \
    && ln -s ~/phpactor/bin/phpactor /usr/bin/phpactor


# NodeJS extrapackages for Neovim.
RUN npm i -g neovim
RUN npm install -g prettier
RUN npm install -g graphql-language-service-cli

# Go extrapackages for Neovim.
RUN go get github.com/jesseduffield/lazygit
RUN go get github.com/jesseduffield/lazydocker
RUN go get github.com/jesseduffield/lazynpm

# Install neovim from sources
RUN git clone https://github.com/neovim/neovim \
    && cd neovim \
    && make CMAKE_BUILD_TYPE=Release install \
	&& which nvim > /dev/null && echo "nvim installed, moving on..." || make CMAKE_BUILD_TYPE=Release install \
    && which nvim > /dev/null && echo "nvim installed, moving on..." || make CMAKE_BUILD_TYPE=Release install

# Create directory for NVCode configuration files.
RUN mkdir -p /usr/local/share/nvcode/nvim

# Clone NVCode
RUN git clone https://github.com/ChristianChiarulli/nvcode.git /usr/local/share/nvcode/nvim \
    && cd /usr/local/share/nvcode/nvim \ 
    && sed -i "s/~\/\.config\/nvim/\/usr\/local\/share\/nvcode\/nvim/" init.lua

#  Link NVCode
RUN echo "#!/bin/bash " > /usr/local/share/nvcode/nvim/utils/bin/nv \
    && echo "export XDG_CONFIG_HOME=~/.config/nvcode" >> /usr/local/share/nvcode/nvim/utils/bin/nv \
    && echo "export XDG_DATA_HOME=~/.local/share/nvcode/data" >> /usr/local/share/nvcode/nvim/utils/bin/nv \
    && echo "export XDG_CACHE_HOME=~/.local/share/nvcode/cache" >> /usr/local/share/nvcode/nvim/utils/bin/nv \
    && echo "export XDG_CONFIG_DIRS=/usr/share:/usr/local/share:/usr/local/share/nvcode" >> /usr/local/share/nvcode/nvim/utils/bin/nv \
    && echo "nvim -u /usr/local/share/nvcode/nvim/init.lua \$@" >> /usr/local/share/nvcode/nvim/utils/bin/nv
RUN ln -s /usr/local/share/nvcode/nvim/utils/bin/nv /usr/local/bin/nv

# Install Plugins
RUN echo "require('packer').install()" >> init.lua
RUN touch /usr/local/share/nvcode/nvim/.packer_sync

# Neovim spell check dictionaries.
RUN mkdir -p ~/.local/share/nvim/site/spell
RUN wget -q -P ~/.local/share/nvim/site/spell/ http://ftp.vim.org/vim/runtime/spell/uk.utf-8.spl

# Create directory for projects (there should be mounted from host).
RUN mkdir -p /root/workspace

# Set default location after container startup.
WORKDIR /root/workspace

RUN apt-get update \
    && apt-get  upgrade -y \
    && apt-get autoclean -y \
    && apt-get autoremove -y \
    && rm -rf /var/lib/apt/lists/* /tmp/* /usr/share/doc/* /usr/share/info/* /var/tmp/*
