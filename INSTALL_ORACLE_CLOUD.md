# 🚀 Evolution API - Instalación en Oracle Cloud

**Proyecto:** CRM_Drones WhatsApp Integration
**Versión:** 2.3.7
**Servidor:** Oracle Cloud
**Fecha:** 2026-02-08

---

## 📋 Requisitos Previos

- ✅ Servidor Oracle Cloud con Ubuntu/Debian
- ✅ Acceso SSH al servidor
- ✅ Docker y Docker Compose instalados
- ✅ Puertos 8080 y 3000 abiertos en firewall

---

## 🎯 Arquitectura del Stack

```
┌─────────────────────────────────────────┐
│  Evolution API v2.3.7                   │
├─────────────────────────────────────────┤
│                                         │
│  📱 Evolution API (puerto 8080)         │
│  🌐 Manager Frontend (puerto 3000)      │
│  🗄️  PostgreSQL 15 (puerto 5432)        │
│  ⚡ Redis (puerto 6379)                 │
│                                         │
└─────────────────────────────────────────┘
```

---

## 🔧 Pasos de Instalación

### 1. Conectar al Servidor

```bash
ssh usuario@TU_IP_SERVIDOR
```

### 2. Clonar el Repositorio

```bash
cd ~
git clone https://github.com/TU_USUARIO/evolution-service.git
cd evolution-service
```

### 3. Configurar Variables de Entorno

El archivo `.env` ya está configurado. **IMPORTANTE**: Editar las siguientes variables:

```bash
nano .env
```

**Cambiar:**
```bash
SERVER_URL=http://YOUR_PUBLIC_IP:8080
```

Por tu IP pública de Oracle Cloud.

**Guardar con:** `Ctrl+O`, `Enter`, `Ctrl+X`

### 4. Abrir Puertos en Firewall de Oracle Cloud

**En el servidor:**
```bash
# Abrir puerto 8080 (Evolution API)
sudo iptables -I INPUT -p tcp --dport 8080 -j ACCEPT

# Abrir puerto 3000 (Manager Frontend)
sudo iptables -I INPUT -p tcp --dport 3000 -j ACCEPT

# Guardar reglas
sudo iptables-save | sudo tee /etc/iptables/rules.v4
```

**En Oracle Cloud Console:**
1. Ve a: Instancia > Subnet > Security List
2. Agregar Ingress Rules:
   - **Puerto 8080:**
     - Source CIDR: `0.0.0.0/0`
     - IP Protocol: `TCP`
     - Destination Port Range: `8080`

   - **Puerto 3000:**
     - Source CIDR: `0.0.0.0/0`
     - IP Protocol: `TCP`
     - Destination Port Range: `3000`

### 5. Iniciar los Contenedores

```bash
# Iniciar en segundo plano
docker-compose up -d

# Ver logs en tiempo real
docker-compose logs -f
```

### 6. Verificar Instalación

```bash
# Ver estado de contenedores
docker-compose ps

# Deberías ver:
# evolution_api       Up
# evolution_frontend  Up
# evolution_redis     Up
# evolution_postgres  Up
```

**Probar la API:**
```bash
curl http://localhost:8080
```

---

## 🔑 Credenciales y Acceso

### API Key
```
5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f
```

**⚠️ IMPORTANTE: Guardar de forma segura**

### URLs de Acceso

- **Evolution API:** `http://TU_IP_PUBLICA:8080`
- **Manager Frontend:** `http://TU_IP_PUBLICA:3000`
- **Documentación API:** `http://TU_IP_PUBLICA:8080/manager`

### Base de Datos PostgreSQL

- **Host:** `evolution-postgres` (interno)
- **Puerto:** `5432`
- **Database:** `evolution_db`
- **Usuario:** `evolution_user`
- **Contraseña:** `evolution_secure_pass_2026`

---

## 📡 Integración con n8n

### Configuración en n8n:

1. **URL de Evolution API:**
   ```
   http://TU_IP_SERVIDOR:8080
   ```

2. **API Key:**
   ```
   5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f
   ```

3. **Crear instancia de WhatsApp:**
   ```bash
   curl -X POST http://TU_IP_PUBLICA:8080/instance/create \
     -H "Content-Type: application/json" \
     -H "apikey: 5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f" \
     -d '{
       "instanceName": "whatsapp_crm_drones",
       "qrcode": true
     }'
   ```

4. **Obtener QR Code:**
   ```bash
   curl http://TU_IP_PUBLICA:8080/instance/connect/whatsapp_crm_drones \
     -H "apikey: 5ca62c76219905dbb846701b8402c13be9f31703e130669c7ba524aac2f6247f"
   ```

   Escanea el QR con WhatsApp.

---

## 🔧 Comandos Útiles

### Gestión de Contenedores

```bash
# Ver logs
docker-compose logs -f evolution_api

# Reiniciar servicio específico
docker-compose restart evolution_api

# Detener todos los servicios
docker-compose down

# Detener y eliminar volúmenes (⚠️ CUIDADO: elimina datos)
docker-compose down -v

# Ver uso de recursos
docker stats
```

### Actualizar Evolution API

```bash
# Detener servicios
docker-compose down

# Actualizar imágenes
docker-compose pull

# Reiniciar
docker-compose up -d

# Verificar logs
docker-compose logs -f
```

### Backups

**Backup de PostgreSQL:**
```bash
# Crear backup
docker exec evolution_postgres pg_dump -U evolution_user evolution_db > backup_$(date +%Y%m%d).sql

# Restaurar backup
cat backup_20260208.sql | docker exec -i evolution_postgres psql -U evolution_user evolution_db
```

**Backup de volúmenes:**
```bash
# Backup de instancias
docker run --rm -v evolution_instances:/data -v $(pwd):/backup alpine tar czf /backup/instances_backup.tar.gz -C /data .

# Restaurar instancias
docker run --rm -v evolution_instances:/data -v $(pwd):/backup alpine tar xzf /backup/instances_backup.tar.gz -C /data
```

---

## 🐛 Solución de Problemas

### El contenedor no inicia

```bash
# Ver logs detallados
docker-compose logs evolution_api

# Verificar que el puerto no está en uso
sudo netstat -tulpn | grep 8080
```

### No puedo acceder desde fuera del servidor

1. **Verificar firewall del servidor:**
   ```bash
   sudo iptables -L -n | grep 8080
   ```

2. **Verificar Security List en Oracle Cloud Console**

3. **Verificar que el contenedor está escuchando:**
   ```bash
   docker-compose ps
   ```

### Error de conexión a PostgreSQL

```bash
# Reiniciar PostgreSQL
docker-compose restart evolution-postgres

# Ver logs de PostgreSQL
docker-compose logs evolution-postgres

# Verificar conexión desde el contenedor de API
docker exec -it evolution_api sh
# Dentro del contenedor:
ping evolution-postgres
```

### Redis no conecta

```bash
# Reiniciar Redis
docker-compose restart evolution_redis

# Ver logs
docker-compose logs evolution_redis

# Probar conexión
docker exec -it evolution_redis redis-cli ping
# Debe responder: PONG
```

---

## 📊 Monitoreo

### Ver Recursos

```bash
# Uso de recursos
docker stats

# Logs en tiempo real
docker-compose logs -f

# Logs de servicio específico
docker-compose logs -f evolution_api
```

### Health Check

```bash
# API
curl http://localhost:8080

# Redis
docker exec evolution_redis redis-cli ping

# PostgreSQL
docker exec evolution_postgres psql -U evolution_user -d evolution_db -c "SELECT 1;"
```

---

## 🔒 Seguridad

### Recomendaciones:

1. **Cambiar contraseñas por defecto** en producción
2. **Usar HTTPS** con Nginx como proxy reverso
3. **Restringir acceso a puertos** solo desde IPs conocidas
4. **Backups regulares** de base de datos
5. **Monitorear logs** para detectar actividad sospechosa
6. **Mantener actualizado** Evolution API

### Configurar SSL con Nginx (Opcional)

```bash
# Instalar Nginx
sudo apt install nginx certbot python3-certbot-nginx

# Configurar proxy reverso
sudo nano /etc/nginx/sites-available/evolution

# Contenido:
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:8080;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

# Activar configuración
sudo ln -s /etc/nginx/sites-available/evolution /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx

# Obtener certificado SSL
sudo certbot --nginx -d your-domain.com
```

---

## 📚 Documentación Adicional

- **Documentación oficial:** https://doc.evolution-api.com/
- **GitHub oficial:** https://github.com/EvolutionAPI/evolution-api
- **API Reference:** http://TU_IP:8080/manager

---

## ✅ Checklist de Instalación

- [ ] Servidor Oracle Cloud configurado
- [ ] Docker y Docker Compose instalados
- [ ] Repositorio clonado
- [ ] Archivo `.env` editado con IP pública
- [ ] Puertos 8080 y 3000 abiertos en firewall del servidor
- [ ] Ingress Rules creadas en Oracle Cloud Console
- [ ] Contenedores levantados con `docker-compose up -d`
- [ ] API responde en http://TU_IP:8080
- [ ] Manager accesible en http://TU_IP:3000
- [ ] API Key guardada de forma segura
- [ ] Prueba de creación de instancia WhatsApp exitosa
- [ ] Integración con n8n configurada

---

## 🎉 ¡Listo!

Evolution API debería estar funcionando correctamente. Ahora puedes:

1. Crear instancias de WhatsApp
2. Conectar con n8n
3. Integrar con tu CRM_Drones
4. Automatizar mensajes de WhatsApp

**¿Necesitas ayuda?** Revisa la documentación oficial o los logs con `docker-compose logs -f`
