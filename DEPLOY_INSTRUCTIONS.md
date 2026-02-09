# 🚀 Desplegar Evolution API en Oracle Cloud

## Opción 1: Clonar desde GitHub (Recomendado)

### Paso 1: Conectar al servidor
```bash
ssh usuario@TU_IP_SERVIDOR
```

### Paso 2: Clonar el repositorio
```bash
cd ~
git clone -b production-2.3.7 https://github.com/BravaLab/evolution-service.git
cd evolution-service
```

### Paso 3: Crear archivo .env
```bash
nano .env
```

**Pegar este contenido:**
```bash
# ========================================
# EVOLUTION API - CONFIGURACIÓN PRODUCCIÓN
# ========================================

# ===== SERVER CONFIGURATION =====
SERVER_NAME=evolution
SERVER_TYPE=http
SERVER_PORT=8080
SERVER_URL=http://TU_IP_PUBLICA:8080

# ===== AUTHENTICATION =====
AUTHENTICATION_API_KEY=5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f

# ===== DATABASE - POSTGRESQL =====
DATABASE_PROVIDER=postgresql
DATABASE_CONNECTION_URI=postgresql://evolution_user:evolution_secure_pass_2026@evolution-postgres:5432/evolution_db?schema=evolution_api
DATABASE_CONNECTION_CLIENT_NAME=crm_drones_production

POSTGRES_DATABASE=evolution_db
POSTGRES_USERNAME=evolution_user
POSTGRES_PASSWORD=evolution_secure_pass_2026

DATABASE_SAVE_DATA_INSTANCE=true
DATABASE_SAVE_DATA_NEW_MESSAGE=true
DATABASE_SAVE_MESSAGE_UPDATE=true
DATABASE_SAVE_DATA_CONTACTS=true
DATABASE_SAVE_DATA_CHATS=true
DATABASE_SAVE_DATA_LABELS=true
DATABASE_SAVE_DATA_HISTORIC=true
DATABASE_SAVE_IS_ON_WHATSAPP=true
DATABASE_SAVE_IS_ON_WHATSAPP_DAYS=7
DATABASE_DELETE_MESSAGE=true

# ===== REDIS CACHE =====
CACHE_REDIS_ENABLED=true
CACHE_REDIS_URI=redis://evolution-redis:6379
CACHE_REDIS_PREFIX_KEY=evolution_crm
CACHE_REDIS_SAVE_INSTANCES=false
CACHE_LOCAL_ENABLED=false

# ===== CORS =====
CORS_ORIGIN=*
CORS_METHODS=GET,POST,PUT,DELETE
CORS_CREDENTIALS=true

# ===== LOGGING =====
LOG_LEVEL=ERROR,WARN,DEBUG,INFO,LOG,VERBOSE,DARK,WEBHOOKS
LOG_COLOR=true
LOG_BAILEYS=error

# ===== INSTANCE MANAGEMENT =====
DEL_INSTANCE=false
DEL_TEMP_INSTANCES=false

# ===== RABBITMQ (DISABLED) =====
RABBITMQ_ENABLED=false

# ===== WEBSOCKET =====
WEBSOCKET_ENABLED=true

# ===== QRCODE SETTINGS =====
QRCODE_LIMIT=30
QRCODE_COLOR=#198754

# ===== TELEMETRY =====
TELEMETRY_ENABLED=false

# ===== PROMETHEUS METRICS (DISABLED) =====
PROMETHEUS_METRICS=false

# ===== EVENT EMITTER =====
EVENT_EMITTER_MAX_LISTENERS=50
```

**IMPORTANTE:** Cambiar `TU_IP_PUBLICA` por la IP real de tu servidor.

**Guardar:** `Ctrl+O`, `Enter`, `Ctrl+X`

### Paso 4: Abrir puertos en firewall
```bash
# Abrir puertos necesarios
sudo iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
sudo iptables -I INPUT -p tcp --dport 3000 -j ACCEPT
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

### Paso 5: Iniciar servicios
```bash
docker-compose up -d
```

### Paso 6: Verificar instalación
```bash
# Ver estado
docker-compose ps

# Ver logs
docker-compose logs -f evolution_api

# Probar API
curl http://localhost:8080
```

---

## Opción 2: Copiar archivos con SCP (Alternativa)

### Desde tu Mac:
```bash
# Comprimir el proyecto
cd /Users/sergioantunez/Desktop
tar -czf evolution-service.tar.gz evolution-service/

# Copiar al servidor
scp evolution-service.tar.gz usuario@TU_IP_SERVIDOR:~/

# Conectar al servidor
ssh usuario@TU_IP_SERVIDOR

# Descomprimir
cd ~
tar -xzf evolution-service.tar.gz
cd evolution-service

# Editar .env (cambiar TU_IP_PUBLICA)
nano .env

# Iniciar servicios
docker-compose up -d
```

---

## ⚠️ No Olvides: Configurar Firewall en Oracle Cloud

**En Oracle Cloud Console:**
1. Ve a: Instancia > Subnet > Security List
2. Agregar Ingress Rules:

   **Regla 1 - Puerto 8080:**
   - Source CIDR: `0.0.0.0/0`
   - IP Protocol: `TCP`
   - Destination Port Range: `8080`

   **Regla 2 - Puerto 3000:**
   - Source CIDR: `0.0.0.0/0`
   - IP Protocol: `TCP`
   - Destination Port Range: `3000`

---

## ✅ Verificación Final

```bash
# 1. Contenedores corriendo
docker-compose ps

# 2. API responde
curl http://localhost:8080

# 3. Desde tu Mac
curl http://TU_IP_PUBLICA:8080

# 4. Ver logs
docker-compose logs -f
```

---

## 🎯 Acceso Final

- **Evolution API:** http://TU_IP_PUBLICA:8080
- **Manager:** http://TU_IP_PUBLICA:3000
- **Docs:** http://TU_IP_PUBLICA:8080/manager

---

## 🔑 Credenciales

**API Key:**
```
5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f
```

**PostgreSQL:**
- Usuario: `evolution_user`
- Contraseña: `evolution_secure_pass_2026`
- Database: `evolution_db`

---

## 📚 Siguiente Paso

Después de verificar que Evolution API funciona, ir a:
**INSTALL_ORACLE_CLOUD.md** - Sección "Integración con n8n"
