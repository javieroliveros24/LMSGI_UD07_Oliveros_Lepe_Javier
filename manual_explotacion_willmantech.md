# Markdown Javier Oliveros Lepe
# Manual Técnico de Explotación - WillmanTech S.L.
**Cumplimiento Estándar Internacional ISO/IEC/IEEE 26514:2022** *Sistema de Gestión ERP/CRM en Entornos de Producción Dockerizados*

---

## 1. Arquitectura de Despliegue y Contenedores
El sistema se encuentra orquestado mediante contenedores Docker para garantizar el aislamiento de procesos y la inmutabilidad de la infraestructura.

### 1.1 Diagrama de Infraestructura Lógica
- **Contenedor Web/App (`willmantech-erp`):** Motor principal del ERP basado en la lógica de negocio, encargado del renderizado de informes QWeb (`report_invoice_willmantech.xml`).
- **Contenedor Base de Datos (`willmantech-db`):** Motor PostgreSQL que salvaguarda la persistencia relacional del modelo de datos de ventas y facturación.

### 1.2 Archivo de Orquestación (`docker-compose.yml`)
```yaml
version: '3.8'

services:
  willmantech-db:
    image: postgres:15-alpine
    container_name: willmantech-db
    environment:
      - POSTGRES_DB=willmantech_prod
      - POSTGRES_USER=willman_admin
      - POSTGRES_PASSWORD=Secured_Password_2026
    volumes:
      - database_cluster_data:/var/lib/postgresql/data
    networks:
      - willmantech-internal
    restart: always

  willmantech-erp:
    image: odoo:17.0
    container_name: willmantech-erp
    depends_on:
      - willmantech-db
    ports:
      - "8069:8069"
    volumes:
      - erp_custom_addons:/mnt/extra-addons
      - erp_filestore_data:/var/lib/odoo
    networks:
      - willmantech-internal
    restart: alwasys

volumes:
  database_cluster_data:
    driver: local
  erp_custom_addons:
    driver: local
  erp_filestore_data:
    driver: local

networks:
  willmantech-internal:
    driver: bridge