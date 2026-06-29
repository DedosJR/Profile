# Despliegue de Monitoreo en Windows Server

> **Objetivo:** Implementar `windows_exporter` para exportar métricas de sistema operativo hacia una plataforma de monitoreo (Prometheus + Grafana).

---

## Tabla de Contenidos

- [A. Instalación del Agente](#a-instalación-del-agente)
- [B. Configuración de Red (Firewall)](#b-configuración-de-red-firewall)
- [C. Estado de Verificación](#c-estado-de-verificación)
- [D. Resolución de Problemas en Grafana](#d-resolución-de-problemas-en-grafana)

---

## A. Instalación del Agente

Se utilizó el paquete MSI de **windows_exporter v0.29.0** mediante PowerShell.

### 1. Descarga y ejecución silenciosa

```powershell
Start-Process msiexec.exe -ArgumentList "/i C:\Temp\windows_exporter.msi /quiet ENABLED_COLLECTORS=cpu,memory,os,logical_disk" -Wait
```

> **Collectors habilitados:** `cpu`, `memory`, `os`, `logical_disk`

### 2. Validación

El servicio queda registrado como `windows_exporter` en el administrador de servicios de Windows.

```powershell
# Verificar que el servicio esté corriendo
Get-Service -Name "windows_exporter"
```

Salida esperada:

```
Status   Name               DisplayName
------   ----               -----------
Running  windows_exporter   windows_exporter
```

---

## B. Configuración de Red (Firewall)

Por defecto, el servicio solo escucha conexiones locales. Se aplicó una regla de entrada (**Inbound**) para permitir el tráfico desde el segmento de red:

```powershell
New-NetFirewallRule `
  -DisplayName "Allow Windows Exporter" `
  -Direction Inbound `
  -LocalPort 9182 `
  -Protocol TCP `
  -Action Allow
```

> **Puerto por defecto:** `9182/TCP`

---

## C. Estado de Verificación

| Endpoint | Estado |
|---|---|
| `http://localhost:9182/metrics` | ✅ Confirmado operativo (local) |
| `http://192.168.x.x:9182/metrics` | ✅ Confirmado operativo (remoto) |

---

## D. Resolución de Problemas en Grafana

Si Grafana no muestra gráficas, revisar los siguientes 3 puntos en el servidor de Prometheus/Grafana.

### Punto 1 — Configuración de Prometheus (`prometheus.yml`)

Asegurarse de haber añadido la IP del servidor Windows al archivo de configuración:

```yaml
scrape_configs:
  - job_name: 'windows_servers'
    static_configs:
      - targets: ['192.168.x.x:9182']
```

> ⚠️ **Después de cambiar este archivo, se debe reiniciar Prometheus.**

### Punto 2 — Verificación en Prometheus

Acceder al servidor Prometheus en el puerto `9090` y navegar a **Status → Targets**:

| Estado | Significado |
|---|---|
| 🟢 Verde (UP) | Prometheus conecta correctamente con el exporter |
| 🔴 Rojo (DOWN) | Prometheus no logra conectar — revisar red/firewall |

### Punto 3 — Dashboards de Grafana

Asegurarse de estar usando un dashboard compatible con `windows_exporter`, ya que las métricas de Linux (`node_exporter`) **no son compatibles** con las de Windows.

**Dashboards recomendados** (importar por ID en Grafana):

| ID | Nombre |
|---|---|
| `10467` | Windows Exporter Dashboard |
| `14694` | Windows Node - Community |

Para importar un dashboard: **Dashboards → Import → ingresar el ID → Load**.

---

## Referencias

- [windows_exporter — Repositorio oficial](https://github.com/prometheus-community/windows_exporter)
- [Grafana Dashboard 10467](https://grafana.com/grafana/dashboards/10467)
- [Grafana Dashboard 14694](https://grafana.com/grafana/dashboards/14694)

---

*Documento generado el 28 de junio de 2026*