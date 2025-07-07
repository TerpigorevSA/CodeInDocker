# Базовый образ Ubuntu 22.04
FROM ubuntu:22.04

# Устанавливаем системные зависимости
RUN apt-get update && apt-get install -y \
    wget \
    curl \
    git \
    tar \
    gzip \
#    python3 \
#    python3-pip \
#    build-essential \
#    pkg-config \
#    libssl-dev \
    && rm -rf /var/lib/apt/lists/*

# Устанавливаем .NET 8 SDK (официальный способ от Microsoft)
#RUN wget https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb \
#    && dpkg -i packages-microsoft-prod.deb \
#    && rm packages-microsoft-prod.deb \
#    && apt-get update && apt-get install -y dotnet-sdk-8.0

# Устанавливаем Node.js 20 LTS
#RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
#    && apt-get install -y nodejs

# Скачиваем VS Code Server
RUN mkdir -p /tmp && chmod 777 /tmp \
    && wget -q --tries=3 --waitretry=10 --no-check-certificate \
    https://update.code.visualstudio.com/latest/server-linux-x64/stable \
    -O /tmp/vscode-server.tar.gz \
    && test -f /tmp/vscode-server.tar.gz
RUN ls -l / 
#RUN ls -lLR --ignore='*' /tmp | grep -v '^l'
RUN ls -l /tmp

# Распаковываем
RUN mkdir -p /opt/vscode-server
RUN tar -xzf /tmp/vscode-server.tar.gz -C /opt/vscode-server --strip-components=1 
RUN ls -lLR --ignore='*' /opt/vscode-server | grep -v '^l'

# Создаем симлинк
RUN ln -s /opt/vscode-server/bin/code /usr/local/bin/code 

# Проверяем работоспособность
RUN /opt/vscode-server/bin/code --version >/dev/null

# Создаем структуру профилей
RUN mkdir -p \
    /vscode-persist/profiles/csharp/extensions \
    /vscode-persist/profiles/js-ts-react/extensions \
    && chown -R root:root /vscode-persist \
    && chmod -R 755 /vscode-persist

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

# Настройка рабочей директории
WORKDIR /workspace

# Команда запуска
CMD ["/opt/vscode-server/bin/code", "--no-sandbox", "--user-data-dir=/vscode-data", "--extensions-dir=/vscode-persist/extensions"]
