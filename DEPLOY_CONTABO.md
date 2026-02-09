# 🚀 Desplegar Evolution API en Servidor Contabo

**Servidor:** Contabo VPS St. Louis
**IP:** 212.28.180.40
**OS:** Ubuntu 24.04
**Fecha:** 2026-02-08

---

## 📋 Información del Servidor

```yaml
IP Pública: 212.28.180.40
IPv6: 2605:a140:2306:9765::1
Ubicación: St. Louis
Sistema: Ubuntu 24.04
Servicios actuales: n8n
```

---

## 🚀 Pasos de Instalación

### 1️⃣ Conectar al servidor

```bash
ssh root@212.28.180.40
```

### 2️⃣ Verificar Docker instalado

```bash
docker --version
docker-compose --version
```

Si no está instalado:
```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

### 3️⃣ Clonar el repositorio

```bash
cd /root
git clone -b production-2.3.7 https://github.com/BravaLab/evolution-service.git
cd evolution-service
```

### 4️⃣ Verificar archivo .env

El archivo `.env` ya debe tener la configuración correcta:

```bash
cat .env | grep SERVER_URL
# Debe mostrar: SERVER_URL=http://212.28.180.40:8080
```

Si necesitas crear el .env manualmente:
```bash
nano .env
```

Y pegar el contenido del `.env` que está en el repo (ya configurado).

### 5️⃣ Abrir puertos en firewall

```bash
# Verificar firewall
sudo ufw status

# Si está activo, abrir puertos necesarios
sudo ufw allow 8080/tcp comment 'Evolution API'
sudo ufw allow 3000/tcp comment 'Evolution Manager'

# Verificar
sudo ufw status numbered
```

### 6️⃣ Iniciar Evolution API

```bash
# Iniciar servicios en segundo plano
docker-compose up -d

# Ver logs en tiempo real
docker-compose logs -f

# Para salir de logs: Ctrl+C
```

### 7️⃣ Verificar instalación

```bash
# Ver estado de contenedores
docker-compose ps

# Debe mostrar:
# evolution_api       Up      0.0.0.0:8080->8080/tcp
# evolution_frontend  Up      0.0.0.0:3000->80/tcp
# evolution_redis     Up      6379/tcp
# evolution_postgres  Up      5432/tcp

# Probar API localmente
curl http://localhost:8080

# Ver logs específicos
docker-compose logs evolution_api
```

### 8️⃣ Probar desde fuera del servidor

Desde tu Mac:
```bash
curl http://212.28.180.40:8080
```

Desde navegador:
```
http://212.28.180.40:8080/manager
```

---

## 🌐 URLs de Acceso

- **Evolution API:** http://212.28.180.40:8080
- **Manager UI:** http://212.28.180.40:3000
- **Documentación API:** http://212.28.180.40:8080/manager

---

## 🔑 Credenciales

### API Key
```
5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f
```

### PostgreSQL
```yaml
Host: evolution-postgres (interno) / localhost:5432 (externo)
Database: evolution_db
Usuario: evolution_user
Contraseña: evolution_secure_pass_2026
```

### Redis
```yaml
Host: evolution-redis (interno) / localhost:6379 (externo)
Sin contraseña
```

---

## 🔗 Integración con n8n

### En n8n (que ya está en el mismo servidor):

**1. Crear credencial Evolution API:**
- Type: Header Auth
- Name: `apikey`
- Value: `5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f`

**2. Base URL en n8n:**
```
http://212.28.180.40:8080
```

O si n8n está en el mismo servidor:
```
http://localhost:8080
```

**3. Crear instancia WhatsApp:**
```bash
curl -X POST http://212.28.180.40:8080/instance/create \
  -H "Content-Type: application/json" \
  -H "apikey: 5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f" \
  -d '{
    "instanceName": "whatsapp_crm_drones",
    "qrcode": true
  }'
```

**4. Obtener QR Code para conectar WhatsApp:**
```bash
curl http://212.28.180.40:8080/instance/connect/whatsapp_crm_drones \
  -H "apikey: 5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f"
```

Copiar el QR que aparece y escanearlo con WhatsApp.

---

## 🔧 Comandos Útiles

### Gestión de servicios
```bash
# Ver logs en tiempo real
docker-compose logs -f evolution_api

# Reiniciar servicio
docker-compose restart evolution_api

# Detener todo
docker-compose down

# Iniciar todo
docker-compose up -d

# Ver uso de recursos
docker stats
```

### Actualizar Evolution API
```bash
cd /root/evolution-service
docker-compose down
git pull origin production-2.3.7
docker-compose pull
docker-compose up -d
docker-compose logs -f
```

### Backup PostgreSQL
```bash
# Crear backup
docker exec evolution_postgres pg_dump -U evolution_user evolution_db > backup_evolution_$(date +%Y%m%d).sql

# Restaurar backup
cat backup_evolution_20260208.sql | docker exec -i evolution_postgres psql -U evolution_user evolution_db
```

---

## 🐛 Troubleshooting

### Puerto ya en uso
```bash
# Ver qué está usando el puerto 8080
sudo netstat -tulpn | grep 8080

# Matar proceso si es necesario
sudo kill -9 <PID>
```

### Contenedor no inicia
```bash
# Ver logs detallados
docker-compose logs evolution_api

# Reiniciar PostgreSQL primero
docker-compose restart evolution-postgres
sleep 5
docker-compose restart evolution_api
```

### No puedo acceder desde fuera
```bash
# Verificar firewall
sudo ufw status

# Verificar que contenedor escucha en 0.0.0.0
docker-compose ps
netstat -tulpn | grep 8080
```

### Limpiar todo y empezar de nuevo
```bash
# ⚠️ CUIDADO: Esto elimina TODOS los datos
docker-compose down -v
docker-compose up -d
```

---

## 📊 Monitoreo

### Health Check
```bash
# API
curl http://localhost:8080

# PostgreSQL
docker exec evolution_postgres psql -U evolution_user -d evolution_db -c "SELECT 1;"

# Redis
docker exec evolution_redis redis-cli ping
```

### Ver recursos
```bash
# CPU, RAM de cada contenedor
docker stats

# Espacio en disco
df -h
docker system df
```

---

## ✅ Checklist Post-Instalación

- [ ] Conectado al servidor vía SSH
- [ ] Docker y Docker Compose instalados
- [ ] Repositorio clonado en `/root/evolution-service`
- [ ] Archivo `.env` verificado
- [ ] Puertos 8080 y 3000 abiertos en firewall
- [ ] Servicios iniciados con `docker-compose up -d`
- [ ] Todos los contenedores corriendo (4/4)
- [ ] API responde en http://212.28.180.40:8080
- [ ] Manager accesible en http://212.28.180.40:3000
- [ ] Instancia WhatsApp creada
- [ ] QR Code escaneado y conectado
- [ ] Integración con n8n probada
- [ ] Primer mensaje de prueba enviado

---

## 🎯 Siguiente Paso

Una vez Evolution API esté funcionando:
1. Configurar webhook en n8n
2. Crear flujos de automatización
3. Integrar con CRM_Drones

---

**Servidor:** Contabo VPS St. Louis (212.28.180.40)
**Proyecto:** CRM_Drones - Evolution API v2.3.7
**Fecha:** 2026-02-08
