FROM ubuntu:22.04

# Avoid prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install basic dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    bison \
    flex \
    libgmp3-dev \
    libmpc-dev \
    libmpfr-dev \
    texinfo \
    wget \
    git \
    curl \
    qemu-system-x86 \
    ovmf \
    mtools \
    gnu-efi \
    nano \
    vim \
    && rm -rf /var/lib/apt/lists/*

# Create a non-root user
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME \
    && rm -rf /var/lib/apt/lists/*

# Build and install x86_64-elf cross-compiler
WORKDIR /tmp/cross-compiler

# Download binutils and gcc
RUN wget https://ftp.gnu.org/gnu/binutils/binutils-2.40.tar.gz \
    && wget https://ftp.gnu.org/gnu/gcc/gcc-13.2.0/gcc-13.2.0.tar.gz \
    && tar -xzf binutils-2.40.tar.gz \
    && tar -xzf gcc-13.2.0.tar.gz

# Set up environment for cross-compilation
ENV PREFIX="/usr/local"
ENV TARGET="x86_64-elf"
ENV PATH="$PREFIX/bin:$PATH"

# Build and install binutils
RUN mkdir build-binutils && cd build-binutils \
    && ../binutils-2.40/configure --target=$TARGET --prefix=$PREFIX --with-sysroot --disable-nls --disable-werror \
    && make -j$(nproc) \
    && make install

# Build and install gcc
RUN mkdir build-gcc && cd build-gcc \
    && ../gcc-13.2.0/configure --target=$TARGET --prefix=$PREFIX --disable-nls --enable-languages=c,c++ --without-headers \
    && make all-gcc -j$(nproc) \
    && make all-target-libgcc -j$(nproc) \
    && make install-gcc \
    && make install-target-libgcc

# Copy OVMF firmware to a location that's easy to access
RUN cp /usr/share/OVMF/OVMF_CODE.fd /usr/local/share/OVMF.fd

# Clean up
WORKDIR /
RUN rm -rf /tmp/cross-compiler

# Switch to non-root user
USER $USERNAME

# Set working directory
WORKDIR /workspace

# Verify the cross-compiler is installed
RUN x86_64-elf-gcc --version
