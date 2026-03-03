# Citas - Sistema de Gestión de Citas para Barberías

Aplicación móvil y API para gestionar citas en barberías y negocios similares.

## Estructura del Proyecto

```
Citas/
├── .devcontainer/
│   ├── Dockerfile          # Imagen de desarrollo
│   ├── devcontainer.json   # Configuración de VS Code Dev Container
│   └── docker-compose.yml  # Orquestación de servicios
├── backend/                # API NestJS
│   ├── src/
│   ├── Dockerfile          # Imagen de producción (API)
│   └── package.json
├── frontend/               # App móvil Expo/React Native
│   ├── App.tsx
│   ├── Dockerfile          # Imagen de producción (Frontend)
│   └── package.json
└── README.md
```

## Requisitos

- Docker
- Docker Compose
- VS Code con extensión **Dev Containers**

## Iniciar Desarrollo

### Opción 1: Usar VS Code Dev Container (Recomendado)

1. Abre la carpeta del proyecto en VS Code
2. Pulsa **F1** o **Ctrl+Shift+P** (Cmd+Shift+P en Mac)
3. Escribe: `Dev Containers: Reopen in Container`
4. Presiona Enter

**¿Qué sucede?**
- VS Code construye la imagen Docker desde `.devcontainer/Dockerfile`
- Se inician los servicios definidos en `docker-compose.yml` (app + db)
- Se ejecuta el `postCreateCommand`:
  ```bash
  cd /workspace/backend && npm install
  cd /workspace/frontend && npm install
  ```
- Las dependencias de backend y frontend se instalan automáticamente
- Se abre una terminal integrada en el contenedor

### Opción 2: Línea de Comandos

```bash
# Construir contenedor
docker compose -f .devcontainer/docker-compose.yml build

# Levantar servicios
docker compose -f .devcontainer/docker-compose.yml up

# En otra terminal, instalar dependencias manualmente
docker exec -it citas_app bash
cd /workspace/backend && npm install
cd /workspace/frontend && npm install
```

## Configuración de Variables de Entorno

### Backend (.env)
```bash
cp backend/.env.example backend/.env
```

Contenido esperado:
```
DB_HOST=db
DB_PORT=3306
DB_USER=citas_user
DB_PASSWORD=citas_password
DB_NAME=citas
NODE_ENV=development
```

### Frontend (.env)
```bash
cp frontend/.env.example frontend/.env
```

Contenido esperado:
```
EXPO_PUBLIC_API_URL=http://localhost:3000
NODE_ENV=development
```

## Ejecutar Servicios

### Terminal 1: Backend (NestJS)
```bash
cd /workspace/backend
npm run dev
```
- API disponible en: `http://localhost:3000`
- Health check: `http://localhost:3000/health`

### Terminal 2: Frontend (Expo/React Native)
```bash
cd /workspace/frontend
npm start
```
- Expo disponible en: `http://localhost:8081`
- Presiona:
  - `i` para abrir en iOS
  - `a` para abrir en Android
  - `w` para abrir en web

## Puertos

| Servicio | Puerto | Descripción |
|----------|--------|-------------|
| Backend (NestJS) | 3000 | API REST |
| Frontend (Expo) | 8081 | Servidor de desarrollo web |
| Database (MySQL) | 3306 | Base de datos |

## Estructura de Dockerfiles

### .devcontainer/Dockerfile
- **Propósito**: Contenedor de desarrollo con todas las herramientas necesarias
- **No instala dependencias** durante la construcción (se hace en postCreateCommand)
- **Usuario no-root**: `vscode` para seguridad

### backend/Dockerfile
- **Propósito**: Producción - API NestJS
- **Expone**: Puerto 3000
- **Health check**: Verifica `/health` endpoint

### frontend/Dockerfile
- **Propósito**: Producción - Servidor Expo/React Native
- **Expone**: Puerto 8081
- **Apps soportadas**: iOS, Android, Web

## Comandos Útiles

```bash
# Ver logs del contenedor
docker compose -f .devcontainer/docker-compose.yml logs -f app

# Acceder al bash del contenedor
docker exec -it citas_app bash

# Reiniciar servicios
docker compose -f .devcontainer/docker-compose.yml restart

# Detener todo
docker compose -f .devcontainer/docker-compose.yml down

# Limpiar volúmenes (cuidado: borra datos)
docker compose -f .devcontainer/docker-compose.yml down -v
```

## Desarrollo

### Agregar dependencias

#### Backend
```bash
cd backend
npm install nombre-paquete
```

#### Frontend
```bash
cd frontend
npm install nombre-paquete
```

### Crear migraciones (si usas TypeORM)

```bash
cd backend
npm run typeorm migration:generate src/migrations/NombreMigracion
npm run typeorm migration:run
```

## Troubleshooting

### Puerto 3306 ya está en uso
```bash
# Ver qué está usando el puerto
lsof -i :3306

# O cambio el puerto en docker-compose.yml
```

### npm install falla
```bash
# Limpiar cache de npm
npm cache clean --force

# Reconstruir contenedor
docker compose -f .devcontainer/docker-compose.yml build --no-cache
```

### Base de datos no se conecta
```bash
# Verificar health del servicio db
docker compose -f .devcontainer/docker-compose.yml ps

# Ver logs de mysql
docker compose -f .devcontainer/docker-compose.yml logs db
```

## Documentación Adicional

- [NestJS](https://docs.nestjs.com/)
- [Expo](https://docs.expo.dev/)
- [React Native](https://reactnative.dev/)
- [Docker Development](https://code.visualstudio.com/docs/devcontainers/containers)
