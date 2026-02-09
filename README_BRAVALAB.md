# 🚁 Evolution API - WhatsApp Integration for CRM_Drones

[![Evolution API](https://img.shields.io/badge/Evolution%20API-v2.3.7-green)](https://github.com/EvolutionAPI/evolution-api)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-blue)](https://www.postgresql.org/)
[![Redis](https://img.shields.io/badge/Redis-latest-red)](https://redis.io/)
[![Docker](https://img.shields.io/badge/Docker-Compose-blue)](https://docs.docker.com/compose/)

## 📋 Descripción

Implementación de **Evolution API v2.3.7** para el proyecto **CRM_Drones**, sistema CRM especializado para venta de drones industriales con integración de WhatsApp.

Este repositorio contiene la configuración optimizada para deployment en **Oracle Cloud** usando **Docker Compose**, con PostgreSQL 15 y Redis.

## 🎯 Propósito

Proporcionar una API REST robusta y escalable para:
- ✅ Gestión de múltiples instancias de WhatsApp
- ✅ Automatización de mensajes vía n8n
- ✅ Integración con CRM para seguimiento de leads
- ✅ Soporte para envío de cotizaciones y catálogos de drones
- ✅ Sistema de notificaciones para demos y capacitaciones

## 🏗️ Stack Tecnológico

```yaml
Services:
  - Evolution API v2.3.7   # WhatsApp Business API
  - PostgreSQL 15          # Base de datos principal
  - Redis latest           # Cache y sessions
  - Manager Frontend       # UI de administración
```

## 🚀 Características

- **Multi-tenant:** Soporte para múltiples instancias WhatsApp
- **Webhooks:** Integración con n8n y otros sistemas
- **Persistencia:** PostgreSQL para datos críticos
- **Cache:** Redis para mejor rendimiento
- **UI Manager:** Interfaz web de administración
- **Docker Compose:** Deploy simplificado
- **API REST:** Documentación Swagger integrada

## 📦 Contenido del Repositorio

```
evolution-service/
├── .env.example               # Template de variables de entorno
├── docker-compose.yaml        # Orquestación de servicios
├── INSTALL_ORACLE_CLOUD.md    # Guía de instalación completa
├── README_BRAVALAB.md         # Este archivo
├── src/                       # Código fuente (TypeScript)
├── prisma/                    # Schemas de base de datos
└── Docker/                    # Configuraciones Docker
```

## ⚡ Instalación Rápida

### Prerrequisitos
- Docker 20.10+
- Docker Compose 2.0+
- Servidor con Ubuntu/Debian
- 4GB RAM mínimo
- Puertos 8080 y 3000 disponibles

### Deployment

```bash
# 1. Clonar repositorio
git clone https://github.com/BravaLab/evolution-service.git
cd evolution-service

# 2. Configurar variables de entorno
cp .env.example .env
nano .env  # Editar SERVER_URL con tu IP pública

# 3. Iniciar servicios
docker-compose up -d

# 4. Verificar
docker-compose ps
curl http://localhost:8080
```

### Acceso

- **API:** `http://TU_IP:8080`
- **Manager:** `http://TU_IP:3000`
- **Docs:** `http://TU_IP:8080/manager`

## 📖 Documentación Completa

Para instrucciones detalladas de instalación en Oracle Cloud, ver:
**[INSTALL_ORACLE_CLOUD.md](./INSTALL_ORACLE_CLOUD.md)**

Incluye:
- ✅ Configuración de firewall
- ✅ Integración con n8n
- ✅ Comandos útiles
- ✅ Troubleshooting
- ✅ Backups y seguridad
- ✅ Monitoreo

## 🔗 Integración con n8n

```bash
# Crear instancia WhatsApp
curl -X POST http://TU_IP:8080/instance/create \
  -H "Content-Type: application/json" \
  -H "apikey: YOUR_API_KEY" \
  -d '{
    "instanceName": "whatsapp_crm_drones",
    "qrcode": true
  }'

# Conectar WhatsApp (escanear QR)
curl http://TU_IP:8080/instance/connect/whatsapp_crm_drones \
  -H "apikey: YOUR_API_KEY"
```

## 🔐 Seguridad

- ⚠️ **NO** subir archivo `.env` al repositorio
- ⚠️ Cambiar `AUTHENTICATION_API_KEY` por defecto
- ⚠️ Usar HTTPS en producción (Nginx + Let's Encrypt)
- ⚠️ Restringir acceso a puertos desde firewall

## 🛠️ Comandos Útiles

```bash
# Ver logs
docker-compose logs -f

# Reiniciar servicio
docker-compose restart evolution_api

# Backup de PostgreSQL
docker exec evolution_postgres pg_dump -U evolution_user evolution_db > backup.sql

# Actualizar
docker-compose pull && docker-compose up -d
```

## 📊 Monitoreo

```bash
# Estado de contenedores
docker-compose ps

# Uso de recursos
docker stats

# Health check
curl http://localhost:8080
```

## 🔄 Actualización

```bash
docker-compose down
docker-compose pull
docker-compose up -d
```

## 🐛 Troubleshooting

Ver sección completa en [INSTALL_ORACLE_CLOUD.md](./INSTALL_ORACLE_CLOUD.md#-solución-de-problemas)

## 📝 Proyecto CRM_Drones

Este servicio forma parte del proyecto **CRM_Drones**, un sistema completo de gestión para venta de drones industriales que incluye:

- 🚁 Catálogo de drones
- 👥 Gestión de contactos/empresas
- 💰 Pipeline de ventas
- 📄 Cotizaciones con PDF
- 📅 Demos y capacitaciones
- 📊 Analytics y reportes
- 💬 **WhatsApp Business (este servicio)**

**Stack del proyecto completo:**
- Backend: Django 5.0 + DRF
- Frontend: React 18
- Database: PostgreSQL 15
- Cache: Redis
- Automation: n8n
- WhatsApp: Evolution API ← **ESTE REPO**
- Deployment: Oracle Cloud + Easypanel

## 🤝 Contribución

Este es un repositorio de deployment configurado para producción. Para contribuciones al código base de Evolution API, ver el [repositorio oficial](https://github.com/EvolutionAPI/evolution-api).

## 📄 Licencia

Evolution API está bajo licencia Apache 2.0. Ver [LICENSE](./LICENSE) para más detalles.

Este repositorio de configuración es parte del proyecto privado CRM_Drones.

## 🔗 Enlaces

- **Evolution API Oficial:** https://github.com/EvolutionAPI/evolution-api
- **Documentación:** https://doc.evolution-api.com/
- **Proyecto CRM_Drones:** (privado)

## ✅ Status

- ✅ Evolution API v2.3.7 configurado
- ✅ PostgreSQL 15 funcionando
- ✅ Redis configurado
- ✅ Docker Compose optimizado
- ✅ Guía de instalación completa
- ⏳ Pendiente: Deploy en Oracle Cloud
- ⏳ Pendiente: Integración con n8n
- ⏳ Pendiente: Conexión con CRM_Drones

---

**Desarrollado para:** CRM_Drones Project
**Fecha:** 2026-02-08
**Versión:** 2.3.7-production
