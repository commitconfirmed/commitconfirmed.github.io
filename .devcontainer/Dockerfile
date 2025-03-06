
FROM mcr.microsoft.com/devcontainers/base:ubuntu

ENV DEBIAN_FRONTEND=noninteractive
ENV SHELL=/usr/bin/zsh
ENV HUGO_VERSION 0.144.2

RUN apt-get update -y && apt-get install -y \
wget git curl build-essential ca-certificates \
&& apt-get autoremove -y \
&& apt-get clean -y \
&& rm -rf /var/lib/apt/lists/*

# Download and install Hugo
RUN wget https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_${HUGO_VERSION}_Linux-64bit.deb && \
    dpkg -i hugo_${HUGO_VERSION}_Linux-64bit.deb && \
    rm hugo_${HUGO_VERSION}_Linux-64bit.deb

# Set the working directory
#WORKDIR /site

# Copy the site files
#COPY . /site

# Build the Hugo site
RUN hugo

# Expose the port Hugo server runs on
EXPOSE 1313

# Start the Hugo server
CMD ["hugo", "server", "--bind", "0.0.0.0"]