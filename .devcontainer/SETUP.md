# 🚀 Flujo de Construcción con F1

## ¿Qué sucede cuando presionas F1 y seleccionas "Reopen in Container"?

### Paso 1: Construcción de Imágenes
```
.devcontainer/devcontainer.json 
    ↓
Lee: dev-compose file → docker-compose.yml
    ↓
docker compose build
    ├── Servicio 'app': Construye desde .devcontainer/Dockerfile
    │   ├── FROM node:18-bullseye
    │   ├── Crea usuario 'vscode'
    │   ├── Instala herramientas (git, curl)
    │   └── Copia todo el proyecto a /workspace
    │
    └── Servicio 'db': Usa imagen mysql:8.0 (sin construcción)
```

### Paso 2: Inicio de Contenedores
```
docker-compose up -d

├─ Contenedor 'citas_db'
│  └─ MySQL se inicia y ejecuta healthcheck
│
└─ Contenedor 'citas_app'
   ├─ Espera a que db esté healthy (depends_on condition)
   ├─ Monta volumen: proyecto local → /workspace
   ├─ Expone puertos: 3000, 8081
   └─ Ejecuta: CMD ["sleep", "infinity"] (espera comandos)
```

### Paso 3: Post-Create Hook ⭐
VS Code ejecuta automáticamente en el contenedor:

```bash
postCreateCommand: "cd /workspace/backend && npm install && cd /workspace/frontend && npm install"
```

**Timeline:**
```
1. cd /workspace/backend
   └→ npm install (instala dependencias de NestJS)

2. cd /workspace/frontend  
   └→ npm install (instala dependencias de Expo/React Native)

3. ✅ Completado - Ready for development
```

### Paso 4: VS Code Connect
```
VS Code
    ↓
Abre terminal en contenedor 'citas_app'
    ↓
Monta la carpeta /workspace
    ↓
Instala extensiones recomendadas
    ├─ ms-vscode.vscode-typescript-tslint-plugin
    ├─ dbaeumer.vscode-eslint
    ├─ esbenp.prettier-vscode
    └─ ms-azuretools.vscode-docker
```

## 📊 Estado Final

| Componente | Estado | Puerto | Ubicación |
|-----------|--------|--------|-----------|
| **Backend** | `npm install ✅` | 3000 | `/workspace/backend` |
| **Frontend** | `npm install ✅` | 8081 | `/workspace/frontend` |
| **MySQL** | `running ✅` | 3306 | Contenedor |
| **VS Code** | `connected ✅` | - | Dentro del contenedor |

## 🔧 Ahora Estás Listos Para:

```bash
# Terminal 1: Iniciar Backend
cd /workspace/backend
npm run dev

# Terminal 2: Iniciar Frontend
cd /workspace/frontend
npm start
```

## 📁 Estructura de Archivos Después del Build

```
/workspace/
├── backend/
│   ├── src/
│   │   ├── main.ts
│   │   ├── app.module.ts
│   │   ├── app.controller.ts
│   │   └── app.service.ts
│   ├── node_modules/        ← Instalado por postCreateCommand
│   ├── package.json
│   ├── tsconfig.json
│   └── Dockerfile
│
├── frontend/
│   ├── App.tsx
│   ├── index.js
│   ├── app.json
│   ├── node_modules/        ← Instalado por postCreateCommand
│   ├── package.json
│   ├── tsconfig.json
│   ├── metro.config.js
│   └── Dockerfile
│
├── .devcontainer/
│   ├── Dockerfile
│   ├── docker-compose.yml
│   └── devcontainer.json
│
└── .gitignore
```

## 🐛 Verificación Rápida

Para confirmar que todo funcionó correctamente, ejecuta en la terminal del contenedor:

```bash
# Verificar backend
cd /workspace/backend && npm list | head -20

# Verificar frontend  
cd /workspace/frontend && npm list | head -20

# Verificar MySQL
mysql -h db -u citas_user -pcitas_password -e "SELECT 1;"

# Verificar que puertos están disponibles
lsof -i :3000   # Backend
lsof -i :8081   # Frontend
lsof -i :3306   # MySQL
```

## ⚡ Flujo Visual Completo

```
F1 → "Reopen in Container"
  ↓
docker-compose.yml se evalúa
  ↓
Contruye Dockerfile de desarrollo
  ↓
Inicia servicios (app + db)
  ↓
Espera a que MySQL esté healthy
  ↓
VS Code se conecta al contenedor
  ↓
Ejecuta postCreateCommand
  ├─ cd /workspace/backend → npm install
  └─ cd /workspace/frontend → npm install
  ↓
✅ Listo para programar!
```

**Tiempo estimado:** 2-5 minutos (primera vez más lenta por descargas)
