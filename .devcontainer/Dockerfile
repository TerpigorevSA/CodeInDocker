# Базовый образ
FROM ubuntu:22.04

# Устанавливаем системные зависимости
RUN apt-get update && apt-get install -y \
    wget \
    curl \
    git \
    dotnet-sdk-8.0 \
    nodejs \
    npm \
    && rm -rf /var/lib/apt/lists/*

# Устанавливаем Node.js через nvm
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash \
    && export NVM_DIR="$HOME/.nvm" \
    && [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" \
    && nvm install --lts

# Создаем структуру папок для расширений
RUN mkdir -p \
    /vscode-persist/profiles/csharp/extensions \
    /vscode-persist/profiles/js-ts-react/extensions \
    && chmod -R 777 /vscode-persist

# Устанавливаем VS Code Server (альтернативный способ)
RUN curl -fsSL https://code.visualstudio.com/sha/download?build=stable&os=linux-x64 -o /tmp/vscode.tar.gz \
    && tar -xzf /tmp/vscode.tar.gz -C /opt \
    && mv /opt/VSCode-linux-x64 /opt/vscode-server \
    && ln -s /opt/vscode-server/bin/code /usr/local/bin/code

# Устанавливаем расширения для C#
RUN /opt/vscode-server/bin/code \
    --extensions-dir /vscode-persist/profiles/csharp/extensions \
    --install-extension ms-dotnettools.csharp \
    --install-extension ionide.ionide-fsharp

# Устанавливаем расширения для JS/TS/React
RUN /opt/vscode-server/bin/code \
    --extensions-dir /vscode-persist/profiles/js-ts-react/extensions \
    --install-extension dbaeumer.vscode-eslint \
    --install-extension esbenp.prettier-vscode \
    --install-extension firefox-devtools.vscode-firefox-debug

WORKDIR /workspace
