
FROM mcr.microsoft.com/devcontainers/base:ubuntu

ENV DEBIAN_FRONTEND=noninteractive
ENV SHELL=/usr/bin/zsh
ENV HUGO_VERSION 0.145.0

RUN apt-get update -y && apt-get install -y \
wget \
&& apt-get autoremove -y \
&& apt-get clean -y \
&& rm -rf /var/lib/apt/lists/*

RUN wget https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.deb && \
    dpkg -i hugo_extended_${HUGO_VERSION}_linux-amd64.deb && \
    rm hugo_extended_${HUGO_VERSION}_linux-amd64.deb

RUN mkdir -p /site

WORKDIR /site

ENV HUGO_CACHEDIR=/cache
ENV PATH="/var/hugo/bin:$PATH"