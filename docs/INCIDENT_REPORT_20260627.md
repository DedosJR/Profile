# Informe de Incidente: Problema de Notificaciones en Alertmanager

---

## 1. Información General

| Campo | Detalle |
|---|---|
| **Fecha del Incidente** | 27 de junio de 2026 |
| **Servicio Afectado** | Sistema de Monitoreo (Alertmanager / Prometheus) |
| **Nivel de Severidad** | 🔴 Alto — Falla en el canal de comunicación de alertas críticas |
| **Estado** | ✅ Resuelto |

---

## 2. Descripción del Problema

El contenedor de `Alertmanager` presentaba errores recurrentes en los logs, reportando `connection refused` al intentar conectar con un webhook configurado en `127.0.0.1:5001`.

A pesar de las modificaciones realizadas en el archivo de configuración en el host, los cambios **no se veían reflejados en el contenedor**, lo que impedía el envío de notificaciones a Telegram.

---

## 3. Análisis de Causa Raíz (RCA)

Se identificó que `docker-compose` estaba utilizando un **volumen anónimo persistente** (`731a6d0...`) creado automáticamente por instancias previas de la configuración.

Este volumen contenía una versión **obsoleta del archivo de configuración**, la cual prevalecía sobre el archivo editado en el host, ignorando los cambios manuales y bloqueando la implementación de la nueva configuración de Telegram.

```
┌─────────────────────────────────────────────────────────────┐
│  Host                        Contenedor Alertmanager        │
│                                                             │
│  alertmanager.yml  ──✘──>   /etc/alertmanager/config.yml   │
│  (modificado)               (montado desde volumen          │
│                              anónimo obsoleto 731a6d0...)   │
└─────────────────────────────────────────────────────────────┘
```

---

## 4. Resolución del Incidente

### Paso 1 — Limpieza de Entorno
Se eliminaron los contenedores activos y los **volúmenes anónimos huérfanos** que mantenían la configuración "envenenada".

### Paso 2 — Ajuste de Montaje
Se modificó el `docker-compose.yml` para utilizar un **bind mount explícito**, asegurando la sincronización directa entre el archivo local y el contenedor.

```yaml
# ❌ Antes (volumen anónimo — problemático)
volumes:
  - alertmanager_data:/etc/alertmanager/

# ✅ Después (bind mount explícito — correcto)
volumes:
  - /ruta/absoluta/alertmanager.yml:/etc/alertmanager/config.yml:ro
```

### Paso 3 — Forzado de Configuración
Se reinició el stack utilizando el parámetro `--force-recreate` para asegurar que el estado del contenedor fuera consistente con la nueva configuración.

### Paso 4 — Validación
Se confirmó mediante `docker inspect` que el montaje (`Mounts`) utiliza `bind` apuntando a la ruta absoluta del archivo en el host.

---

## 5. Historial de Comandos Clave (Bitácora)

```bash
# 1. Identificación del problema de montajes
sudo docker inspect alertmanager | grep -A 10 "Mounts"

# 2. Limpieza de volúmenes huérfanos (PRUNE)
sudo docker volume prune -f

# 3. Forzado de recreación del stack
sudo docker compose down -v
sudo docker compose up -d --force-recreate

# 4. Verificación de configuración cargada dentro del contenedor
sudo docker exec -it alertmanager cat /etc/alertmanager/config.yml

# 5. Prueba de carga (Test de estrés)
stress --cpu 8 --timeout 60
```

---

## 6. Lecciones Aprendidas (Mejores Prácticas ITIL)

### 🗂️ Gestión de Volúmenes
Evitar el uso de nombres de volúmenes anónimos en producción. Definir explícitamente los puntos de montaje en `docker-compose.yml` usando bind mounts con rutas absolutas.

### 🔍 Validación de Entorno
Siempre verificar los `Mounts` de un contenedor al detectar discrepancias entre la configuración en disco y el comportamiento del servicio.

```bash
# Verificación rápida de montajes
sudo docker inspect <contenedor> --format '{{ json .Mounts }}' | jq .
```

### 📋 Documentación
Mantener actualizada la bitácora de cambios para facilitar el troubleshooting ante futuros incidentes. Registrar cada cambio con fecha, autor y motivo.

---

*Documento generado el 28 de junio de 2026*
