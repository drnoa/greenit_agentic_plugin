# Dokumentations-Container: Markdown, Diagramme, PDF-Export
FROM mcr.microsoft.com/devcontainers/base:bookworm

ARG NODE_VERSION=20

# Node.js für markdownlint / mermaid-cli
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get install -y --no-install-recommends nodejs \
    && rm -rf /var/lib/apt/lists/*

# Doku-Toolchain: pandoc (Konvertierung), graphviz (Diagramme),
# python3 (Hilfsskripte), poppler-utils (PDF-Text extrahieren)
RUN apt-get update && apt-get install -y --no-install-recommends \
        pandoc \
        graphviz \
        python3 \
        python3-pip \
        python3-venv \
        poppler-utils \
        jq \
        ripgrep \
    && rm -rf /var/lib/apt/lists/*

RUN npm install -g markdownlint-cli2 @mermaid-js/mermaid-cli

USER vscode
