# PRACTICA_IAW

# Práctica 1: Servidores, Proxies y Certificados
## Nimbus Docs - Portal Documental Seguro Multi-Marca

**Autor:** Jaime Muñoz Vías  
**Curso:** 2026/2027  
**Entorno de despliegue:** Docker & Docker Compose  

---

## 1. Diseño y Decisiones Técnicas (Fase 1)

### 1.1 Dominios Ficticios Seleccionados
Se han definido dos dominios ficticios representativos para simular la arquitectura multi-marca de la compañía:
* **`alpha-docs.lan`**: Portal documental público/operativo de la marca principal.
* **`beta-docs.lan`**: Portal interno y de gestión restringida.

> **Justificación de Originalidad:** Se eligen dominios `.lan` dedicados a servicios documentales específicos para evitar usar valores por defecto del temario de clase (`sitioa.iaw.local`, `app.iaw.local`, etc.), garantizando nombres descriptivos e independientes.

### 1.2 Módulo Multiprocesamiento (MPM) de Apache
* **MPM Seleccionado:** `Event` (configurado en la imagen oficial `httpd:2.4`).
* **Justificación:** El MPM `Event` es una evolución de `Worker` que optimiza la gestión de hilos mediante el uso de un hilo dedicado a gestionar las conexiones en estado *Keep-Alive*. Esto evita mantener hilos bloqueados esperando peticiones del cliente, lo que resulta ideal para arquitecturas detrás de un proxy inverso donde las conexiones entrantes desde Nginx son asíncronas y de alta concurrencia.

### 1.3 Virtual Host Catch-All (Tráfico Huérfano)
Se ha creado un VirtualHost por defecto (`000-default.conf` / `default-catchall.conf`) configurado con `ServerName _default_` asociándolo a un directorio neutro (`/var/www/html/default`).
* **Justificación de Seguridad:** En caso de que un usuario o bot realice una petición HTTP/HTTPS mediante la dirección IP del servidor o utilizando un cabecera `Host` no reconocida/maliciosa, Apache no servirá por defecto el contenido de la primera marca (`alpha-docs.lan`), evitando fugas de información accidental y sirviendo una respuesta neutra controlada.

---

## 2. Configuración e Infraestructura

### 2.1 Estructura del Proyecto
```text
nimbus-docs/
├── docker-compose.yml
├── apache/
│   ├── Dockerfile
│   ├── conf/
│   │   ├── alpha-docs.conf
│   │   ├── beta-docs.conf
│   │   └── default-catchall.conf
│   └── html/
│       ├── alpha/index.html
│       ├── beta/index.html
│       ├── beta/config-db.json  (Fichero sensible protegido)
│       └── default/index.html
└── nginx/
    ├── nginx.conf
    ├── conf.d/
    │   └── nimbus-proxy.conf
    └── ssl/
        ├── nimbus.crt
        └── nimbus.key
