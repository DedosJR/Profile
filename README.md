<div align="center">

# 🏗️ Enterprise Infrastructure Lab

**Israel Flores · Ingeniero en TIC**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Israel_Flores-0077B5?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/israel-flores-it/)
[![Credly](https://img.shields.io/badge/Credly-Insignias-FF6B00?style=for-the-badge&logo=credly&logoColor=white)](https://www.credly.com/users/israel-flores.it)
[![Proxmox](https://img.shields.io/badge/Proxmox_VE-E57000?style=for-the-badge&logo=proxmox&logoColor=white)](https://www.proxmox.com)
[![pfSense](https://img.shields.io/badge/pfSense-212121?style=for-the-badge&logo=pfsense&logoColor=white)](https://www.pfsense.org)
[![Terraform](https://img.shields.io/badge/Terraform-7B42BC?style=for-the-badge&logo=terraform&logoColor=white)](https://www.terraform.io)
[![Ansible](https://img.shields.io/badge/Ansible-EE0000?style=for-the-badge&logo=ansible&logoColor=white)](https://www.ansible.com)

</div>

---

## 📋 Descripción del Proyecto

Este repositorio documenta la configuración y gestión de un entorno de infraestructura virtualizada tipo **Enterprise**. El laboratorio fue diseñado para simular escenarios de producción reales, integrando servicios de red, seguridad perimetral, gestión de identidad y automatización mediante código (IaC).

El objetivo principal es la **aplicación práctica** de tecnologías y metodologías utilizadas en entornos críticos de producción.

---

## 🏛️ Arquitectura General

```
┌─────────────────────────────────────────────────────────────┐
│                      Proxmox VE Node                        │
│                                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │ pfSense  │  │Fortigate │  │  Win Srv │  │  Linux   │   │
│  │  2.8.1   │  │ Firewall │  │    AD    │  │ Servers  │   │
│  └────┬─────┘  └──────────┘  └──────────┘  └──────────┘   │
│       │                                                     │
│  ┌────▼──────────────────────────────────────────────────┐  │
│  │           VLAN Segmentation                           │  │
│  │   [LAN]    [VLAN10 - Gestión]   [VLAN30 - Servicios] │  │
│  └───────────────────────────────────────────────────────┘  │
│                                                             │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐  ┌──────────┐   │
│  │ Ansible  │  │Terraform │  │Prometheus│  │ Grafana  │   │
│  │   IaC    │  │   IaC    │  │ Metrics  │  │Dashboard │   │
│  └──────────┘  └──────────┘  └──────────┘  └──────────┘   │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐   │
│  │          Contenedores: Docker / Kubernetes           │   │
│  └──────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

---

## ⚙️ Stack de Tecnologías

| Categoría | Tecnologías |
|---|---|
| 🖥️ **Virtualización** | Proxmox VE |
| 🔐 **Redes / Firewall** | pfSense 2.8.1, Fortigate, VLANs, TCP/IP, DNS, DHCP |
| 🪟 **Sistemas Operativos** | Linux (Debian / Ubuntu), Windows Server |
| ♾️ **IaC y Automatización** | Terraform, Ansible |
| 📊 **Monitoreo** | Prometheus, Grafana |
| 📦 **Contenedores** | Docker, Docker Compose, Kubernetes |

---

## 🧩 Servicios y Componentes

### 🔒 Redes y Seguridad

#### pfSense 2.8.1
- Gestión de rutas estáticas y dinámicas
- Segmentación de red mediante **VLANs** (LAN, VLAN10, VLAN30)
- Reglas de Firewall con filtrado de paquetes
- Servicios de DNS y DHCP por segmento de red

#### Fortigate
- Implementación de **políticas de seguridad perimetral**
- Inspección profunda de paquetes (DPI)
- Control de acceso basado en identidad

---

### 🪪 Gestión de Identidad

#### Windows Server — Active Directory
- Administración centralizada de usuarios y equipos
- Implementación de **GPOs** (Group Policy Objects)
- Control de acceso basado en roles (RBAC)
- Integración con servicios de red del laboratorio

---

### ♾️ Automatización e IaC

#### Ansible
- Automatización de configuraciones de servidores Linux y Windows
- Despliegue de parches y actualizaciones de seguridad
- Playbooks para aprovisionamiento de entornos reproducibles

#### Terraform
- Gestión declarativa de infraestructura virtualizada
- Aprovisionamiento de VMs en Proxmox VE
- Infraestructura como código versionable y auditable

---

### 📊 Observabilidad

#### Prometheus + Grafana
- Recolección y almacenamiento de métricas de hardware y servicios
- Dashboards en tiempo real para monitoreo proactivo
- Alertas configurables por umbrales de rendimiento
- Visualización de métricas de red, CPU, memoria y almacenamiento

---

### 📦 Contenedores

- Despliegue de servicios mediante **Docker Compose**
- Orquestación de cargas de trabajo con **Kubernetes**
- Gestión de imágenes y registros privados

---

## 🎯 Objetivos de Aprendizaje

Este entorno fue construido con el propósito de adquirir y aplicar competencias en:

- ✅ **Segmentación avanzada de redes** para entornos críticos
- ✅ **Despliegue de soluciones mediante código** (IaC con Terraform y Ansible)
- ✅ **Implementación de sistemas de monitoreo proactivo** para prevenir tiempos de inactividad
- ✅ **Administración de servicios** web y de streaming en entornos Linux
- ✅ **Seguridad perimetral** con firewalls de nivel empresarial
- ✅ **Orquestación de contenedores** con Docker y Kubernetes

---

## 📁 Estructura del Repositorio

```
enterprise-lab/
├── 📂 network/
│   ├── pfsense/          # Configuraciones de pfSense
│   └── vlans/            # Definición de VLANs
├── 📂 iac/
│   ├── terraform/        # Módulos y configuraciones de Terraform
│   └── ansible/          # Playbooks y roles de Ansible
├── 📂 monitoring/
│   ├── prometheus/       # Configuración de scraping y alertas
│   └── grafana/          # Dashboards exportados
├── 📂 containers/
│   ├── docker-compose/   # Archivos Compose por servicio
│   └── kubernetes/       # Manifiestos K8s
└── 📂 docs/              # Documentación adicional y diagramas
```

---

## 🚀 Primeros Pasos

### Prerrequisitos

- Nodo con **Proxmox VE** instalado
- Acceso a las ISOs de pfSense, Windows Server y distribuciones Linux
- Cliente SSH y navegador web para acceder a interfaces de administración

### Despliegue básico

```bash
# 1. Clonar el repositorio
git clone https://github.com/israel-flores/enterprise-lab.git
cd enterprise-lab

# 2. Revisar y adaptar variables de entorno
cp iac/terraform/terraform.tfvars.example iac/terraform/terraform.tfvars

# 3. Inicializar Terraform
cd iac/terraform
terraform init
terraform plan
terraform apply

# 4. Ejecutar playbooks de Ansible
cd iac/ansible
ansible-playbook -i inventory/hosts.yml playbooks/initial-setup.yml
```

---

## 📬 Contacto

**Israel Flores** — Ingeniero en Tecnologías de la Información y Comunicación

[![LinkedIn](https://img.shields.io/badge/Conectar_en_LinkedIn-0077B5?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/israel-flores-it/)
[![Credly](https://img.shields.io/badge/Ver_mis_insignias-FF6B00?style=flat-square&logo=credly&logoColor=white)](https://www.credly.com/users/israel-flores.it)

---

## 🏅 Certificaciones y Logros

<div align="center">

| Insignia | Certificación | Emisor |
|:---:|---|---|
| <a href="https://www.credly.com/badges/c13a7d05-7710-4482-af51-08f64b5a7074/public_url"><img src="https://images.credly.com/images/505080ad-3731-4b1d-98df-347655a45750/linkedin_thumb_image.png" alt="Google Cloud Cybersecurity" width="100"/></a> | **Google Cloud Cybersecurity Certificate** | Google Cloud |
| <a href="https://www.credly.com/badges/6596dd14-abd4-4c3d-acf6-cf68c0565313/public_url"><img src="https://images.credly.com/images/4dda8ae4-99ee-476c-bca3-6f0adbab42fe/linkedin_thumb_image.png" alt="Google Cloud Computing Foundations" width="100"/></a> | **Google Cloud Computing Foundations Certificate** | Google Cloud |
| <a href="https://www.credly.com/badges/fab108d0-9bdb-49d3-a50f-9caa365459bb/public_url"><img src="https://images.credly.com/images/af8c6b4e-fc31-47c4-8dcb-eb7a2065dc5b/linkedin_thumb_I2CS__1_.png" alt="Introduction to Cybersecurity" width="100"/></a> | **Introduction to Cybersecurity** | Cisco |
| <a href="https://www.credly.com/badges/e4685259-6eba-4a02-85d1-5f03ab4f5992/public_url"><img src="https://images.credly.com/images/5bdd6a39-3e03-4444-9510-ecff80c9ce79/linkedin_thumb_image.png" alt="Networking Basics" width="100"/></a> | **Networking Basics** | Cisco |
| <a href="https://www.credly.com/badges/e03252cc-2f48-44bb-9923-0c74be5c4230/public_url"><img src="https://images.credly.com/images/1d3340b2-71cd-4b78-a395-94c67dd462aa/linkedin_thumb_blob" alt="English for IT: Advice and Time" width="100"/></a> | **English for IT: Advice and Time** | Cisco |
| <a href="https://www.credly.com/badges/a14f87c1-cb75-415d-84ab-de4d56082348/public_url"><img src="https://images.credly.com/images/4f76c627-c180-49ae-a5a0-742885eef581/linkedin_thumb_Working_in_a_Digital_World-_Professional_Skills.png" alt="Working in a Digital World" width="100"/></a> | **Working in a Digital World: Professional Skills** | IBM SkillsBuild |

👉 Ver todas las credenciales en [credly.com/users/israel-flores.it](https://www.credly.com/users/israel-flores.it)

</div>

---

<div align="center">

*Laboratorio diseñado para la práctica profesional de infraestructura enterprise.*

</div>
# Profile
