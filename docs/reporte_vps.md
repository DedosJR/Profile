# Infraestructura VPS: Monitoreo y Automatización

Documentación técnica del stack de observabilidad y automatización implementado en el VPS, junto con la guía rápida de comandos de uso diario.

## Tabla de contenidos

- [1. Implementación del sistema de monitoreo](#1-implementación-del-sistema-de-monitoreo)
- [2. Implementación de automatización (Ansible)](#2-implementación-de-automatización-ansible)
- [3. Registro de errores y resoluciones](#3-registro-de-errores-y-resoluciones)
- [4. Estructura de proyectos (referencia)](#4-estructura-de-proyectos-referencia)
- [5. Recomendaciones de mantenimiento](#5-recomendaciones-de-mantenimiento)
- [6. Guía rápida de comandos](#6-guía-rápida-de-comandos)
- [7. Tips de oro para el VPS](#7-tips-de-oro-para-el-vps)
- [8. Resumen de lo logrado](#8-resumen-de-lo-logrado)

---

## 1. Implementación del sistema de monitoreo

Se implementó un stack de observabilidad utilizando Docker para el monitoreo de recursos del sistema.

**Componentes:**

- **Prometheus:** servidor central de recolección de métricas.
- **Node Exporter:** agente recolector de métricas del host.
- **Grafana:** interfaz de visualización de datos.
- **Alertmanager:** gestor de alertas configurado para notificaciones en Telegram.

**Resultado:** dashboard funcional en Grafana y sistema de alertas configurado con umbrales críticos (CPU > 80%, RAM > 85%, Disco > 85%).

## 2. Implementación de automatización (Ansible)

Se estableció una conexión segura entre la máquina local y el VPS mediante SSH (autenticación basada en claves).

- **Funcionalidad:** automatización de tareas de mantenimiento (limpieza de logs) y gestión de respaldos.
- **Resultados:** capacidad de ejecutar tareas administrativas (ping, estado de contenedores, limpieza de logs) de forma remota y centralizada, sin intervención manual.

## 3. Registro de errores y resoluciones

| Problema detectado | Causa raíz | Resolución |
| --- | --- | --- |
| `connection refused` (Prometheus) | `localhost` dentro del contenedor no apunta al host. | Uso de `node-exporter:9100` (nombre de servicio) en la red interna de Docker. |
| `context deadline exceeded` | Bloqueo por firewall externo. | Migración a red interna de Docker, evitando exposición de puertos al exterior. |
| Docker no arranca (`exit-code 1`) | Error de sintaxis en `daemon.json` (se copió código Ansible por error). | Reconstrucción del archivo `daemon.json` con sintaxis JSON válida. |
| `unexpected '.'` en Ansible | Conflicto con las llaves `{{ }}` de Jinja2 en el comando Docker. | Escape de caracteres usando `{{ '{{' }}` o envolviendo en comillas dobles. |

## 4. Estructura de proyectos (referencia)

**`docker-compose.yml`**

- **Estrategia:** uso de red interna de Docker (bridge) para comunicación entre servicios.
- **Optimización:** configuración de `log-opts` (50MB / 3 archivos) para prevenir desbordamiento de disco.

**Playbooks de Ansible (`/iac/ansible/`)**

- `limpiar_logs.yml`: ejecuta `journalctl --vacuum-time=7d` para liberar espacio.
- `backup_azuracast.yml`: comprime `/var/azuracast` mediante el módulo `archive`.
- `status_docker.yml`: consulta el estado de contenedores en tiempo real.

## 5. Recomendaciones de mantenimiento

- **Backup semanal:** ejecutar `ansible-playbook -i hosts.ini backup_azuracast.yml` cada semana.
- **Monitoreo de logs:** revisar semanalmente el uso de disco con `df -h` para asegurar que la rotación de logs de Docker sea efectiva.
- **Actualizaciones:** se puede usar un playbook adicional con el módulo `apt` para actualizar el SO automáticamente cuando se desee.

## 6. Guía rápida de comandos

### 6.1 Conectividad (el "pulso" del servidor)

Para verificar que Ansible tiene control total:

```bash
# Probar conexión básica
ansible vps -i hosts.ini -m ping

# Ver estado rápido de contenedores Docker
ansible vps -i hosts.ini -a "docker ps --format '{{.Names}}: {{.Status}}'"
```

### 6.2 Mantenimiento y limpieza

Ejecuta estos cuando sientas que el servidor está lento o el disco lleno:

```bash
# Limpiar logs de sistema y Docker
ansible-playbook -i hosts.ini limpiar_logs.yml

# Crear backup de Azuracast
ansible-playbook -i hosts.ini backup_azuracast.yml
```

### 6.3 Recuperación ante fallos (modo emergencia)

Si algún servicio Docker no responde, usa estos comandos:

```bash
# Reiniciar Docker completamente
ansible vps -i hosts.ini -b -a "systemctl restart docker"

# Verificar logs del servicio Docker si algo sale mal
ansible vps -i hosts.ini -a "journalctl -u docker.service --no-pager | tail -n 20"
```

## 7. Tips de oro para el VPS

- **Orden en la carpeta:** mantén siempre tus archivos `hosts.ini`, `*.yml` y el archivo de inventario en una única carpeta llamada `/iac` o `/ansible`.
- **Permisos de root:** si un comando falla con `Permission Denied`, agrega el parámetro `-b` (become) a tu comando de Ansible. Ejemplo:

  ```bash
  ansible vps -i hosts.ini -b -a "comando_con_sudo"
  ```

- **Seguridad:** recuerda que tu `bot_token` de Telegram es privado. Si subes estos archivos a un repositorio (como GitHub), **asegúrate de excluirlos o borrarlos antes** (agrégalos a `.gitignore`).

## 8. Resumen de lo logrado

- **Monitoreo:** Prometheus, Grafana y Alertmanager vigilando la salud del servidor.
- **Automatización:** Ansible controlando mantenimiento, logs y respaldos.
- **Seguridad:** alerta instantánea vía Telegram al iniciar sesión SSH.
- **Resiliencia:** capacidad de recuperar el estado del sistema en segundos.
