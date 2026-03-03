# Base oficial estable
FROM node:22-bullseye

# Argumentos para usuario no-root
ARG USERNAME=root
ARG USER_UID=1000
ARG USER_GID=1001

# Crear grupo y usuario
RUN groupadd --gid ${USER_GID} ${USERNAME} || groupmod -g ${USER_GID} ${USERNAME} || true \
    && useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USERNAME} || usermod -u ${USER_UID} -g ${USER_GID} ${USERNAME} || true \
    && apt-get update \
    && apt-get install -y \
        git \
        curl \
        sudo \
        build-essential \
    && echo "${USERNAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Crear workspace y asignar permisos
RUN mkdir -p /workspace \
    && chown -R ${USER_UID}:${USER_GID} /workspace

# Definir directorio de trabajo
WORKDIR /workspace

# Cambiar a usuario no-root
USER ${USERNAME}

# Mantener contenedor vivo (devcontainer override)
CMD ["sleep", "infinity"]