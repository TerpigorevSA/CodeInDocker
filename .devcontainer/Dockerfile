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
    build-essential \
    pkg-config \
    libssl-dev \
    && rm -rf /var/lib/apt/lists/*

# Устанавливаем .NET 8 SDK (официальный способ от Microsoft)
RUN wget https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb \
    && dpkg -i packages-microsoft-prod.deb \
    && rm packages-microsoft-prod.deb \
    && apt-get update && apt-get install -y dotnet-sdk-8.0

# Устанавливаем Node.js 20 LTS
RUN curl -fsSL https://deb.nodesource.com/setup_20.x | bash - \
    && apt-get install -y nodejs

# Устанавливаем VS Code Server с проверками
RUN echo "Скачиваем VS Code Server..." && \
#    wget https://update.code.visualstudio.com/latest/server-linux-x64/stable -O /tmp/vscode.tar.gz && \
    curl -fsSLo /tmp/vscode.tar.gz https://code.visualstudio.com/sha/download?build=stable&os=linux-x64 \
    if [ ! -f /tmp/vscode.tar.gz ]; then echo "Ошибка: файл не скачан"; exit 1; fi && \
    echo "Файл успешно скачан (/tmp/vscode.tar.gz)"

RUN echo "Создаем папку /opt/vscode-server..." && \
    mkdir -p /opt/vscode-server && \
    if [ ! -d "/opt/vscode-server" ]; then echo "Ошибка: папка не создана"; exit 1; fi && \
    echo "Папка создана (/opt/vscode-server)"

RUN echo "Распаковываем архив..." && \
    tar -xzf /tmp/vscode.tar.gz -C /opt/vscode-server --strip-components=1 && \
    if [ $? -ne 0 ]; then echo "Ошибка распаковки"; exit 1; fi && \
    if [ -z "$(ls -A /opt/vscode-server)" ]; then echo "Ошибка: папка пустая"; exit 1; fi && \
    echo "Архив успешно распакован"

RUN echo "Проверяем содержимое папки..." && \
    ls -la /opt/vscode-server/bin && \
    if [ ! -f "/opt/vscode-server/bin/code" ]; then echo "Ошибка: бинарник code не найден"; exit 1; fi && \
    echo "Бинарник code найден"

RUN echo "Создаем симлинк..." && \
    ln -s /opt/vscode-server/bin/code /usr/local/bin/code && \
    if [ ! -L "/usr/local/bin/code" ]; then echo "Ошибка: симлинк не создан"; exit 1; fi && \
    echo "Симлинк создан (/usr/local/bin/code → /opt/vscode-server/bin/code)"

RUN echo "Проверяем версию VS Code Server..." && \
    /opt/vscode-server/bin/code --version && \
    if [ $? -ne 0 ]; then echo "Ошибка: проверка версии не удалась"; exit 1; fi && \
    echo "VS Code Server установлен успешно"

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
