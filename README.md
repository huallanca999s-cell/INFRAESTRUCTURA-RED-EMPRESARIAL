# DISEÑO E IMPLEMENTACIÓN DE INFRAESTRUCTURA DE RED EMPRESARIAL

## Descripción y Contexto del Proyecto
El presente proyecto abarca el diseño, segmentación e implementación de una solución de infraestructura de red de alta disponibilidad para una sede corporativa de la empresa **NovaTech Solutions S.A.**, ubicada en Lima, Perú. La organización opera en un edificio de tres niveles que alberga sus diversas áreas administrativas, operativas y de desarrollo, además de un Centro de Datos centralizado.

---
## Arquitectura de Red (Modelo Jerárquico de 3 Niveles)

La solución de infraestructura implementada para **NovaTech Solutions S.A.** adopta una arquitectura de **tres capas** (Acceso, Distribución y Núcleo), lo que proporciona mayor escalabilidad, flexibilidad, redundancia y una mejor segmentación de la red en comparación con un diseño de dos niveles.

<p align="center">
  <img src="TOPOLOGIA%20DE%20RED/Topologia3.jpg" alt="Topología de Red de Tres Niveles" width="600">
</p>

### Estructura del Escenario Empresarial:
* **Capa de Acceso (*Access Layer*):** Conformada por conmutadores de acceso (`SW-VENTAS`, `SW-ATENCION`, `SW-DESARROLLO`, `SW-CALIDAD`, `SW-ADMIN`, `SW-TI`, `SW-DATACENTER`) que interconectan directamente a los dispositivos finales (PCs, impresoras, servidores y puntos de acceso inalámbrico).
* **Capa de Distribución (*Distribution Layer*):** Conformada por conmutadores multicapa (`MLS1`, `MLS2`), los cuales agregan el tráfico proveniente de la capa de acceso, ejecutan el enrutamiento inter-VLAN y aplican políticas de seguridad.
* **Capa Central (*Core Layer*):** Conformada por los routers principales (`R1`, `R2`), encargados de conmutar el tráfico a alta velocidad en el núcleo de la red y gestionar el enrutamiento mediante el protocolo **OSPF Área 0**.


Para cumplir con los estándares de endurecimiento (*hardening*) de la empresa, los dispositivos de red incluyen:
* Hostnames descriptivos, banners de acceso restringido (`#ACCESO RESTRINGIDO - NOVATECH SOLUTIONS S.A.#`) y cifrado global de contraseñas (`service password-encryption`).
* Gestión remota segura mediante **SSH v2** (`domain-name: novatech.com`, llaves RSA de 1024 bits, bloqueo tras 3 intentos fallidos por 120 segundos).
* Enrutamiento dinámico mediante **OSPF en el Área 0** y enrutamiento Inter-VLAN en la capa de distribución.

---

## Equipos y Tecnologías Aplicadas
* **Simulador:** Cisco Packet Tracer
* **Dispositivos:** Routers Cisco (2911), Multilayer Switches (3650), Switches de Acceso, Access Points, Servidores de Red, PCs, Laptops e Impresoras.
* **Protocolos y Tecnologías:**
  * **Capa 2:** VLANs (802.1Q), Enlaces Troncales (Trunking), Port-Security.
  * **Capa 3:** VLSM, Subnetting `/30` para enlaces P2P, Inter-VLAN Routing, Enrutamiento Dinámico (OSPF Área 0) y Redundancia de Gateway (HSRP).
  * **Servicios de Red:** DHCP Server, DNS, HTTP/HTTPS, Email, FTP, TFTP, SSH v2 para gestión remota.

---

## Architecture & Topología
Diagrama general de la infraestructura de red empresarial:

![Topología de la red](TOPOLOGIA%20DE%20RED/DISE%C3%91O_RED_EMPRESARIAL.png)

---

## Esquema de Direccionamiento IP y Segmentación

### 1. Enlaces Punto a Punto (P2P - Subredes /30)
Para interconectar Routers de Borde (R1, R2) y Switches Multicapa (MLS1, MLS2) se reservaron subredes `/30` (4 direcciones, 2 utilizables):

| Enlace | Red | Equipo 1 (IP) | Equipo 2 (IP) |
| :--- | :--- | :--- | :--- |
| **R1 – MLS1** | `10.10.0.0/30` | R1 (`10.10.0.1`) | MLS1 (`10.10.0.2`) |
| **R1 – MLS2** | `10.10.0.4/30` | R1 (`10.10.0.5`) | MLS2 (`10.10.0.6`) |
| **R2 – MLS1** | `10.10.0.8/30` | R2 (`10.10.0.9`) | MLS1 (`10.10.0.10`) |
| **R2 – MLS2** | `10.10.0.12/30` | R2 (`10.10.0.13`) | MLS2 (`10.10.0.14`) |
| **MLS1 – MLS2** | `10.10.0.16/30` | MLS1 (`10.10.0.17`) | MLS2 (`10.10.0.18`) |
| **R1 – R2** | `10.10.0.20/30` | R1 (`10.10.0.21`) | R2 (`10.10.0.22`) |

---

### 2. Segmentación por VLANs y Departamentos
Segmentación del tráfico de red por áreas operativas y red inalámbrica corporativa:

| VLAN | Departamento / Área | Subred | Gateway |
| :---: | :--- | :--- | :--- |
| **10** | Ventas | `10.10.10.0/24` | `10.10.10.1` |
| **20** | Atención al Cliente | `10.10.20.0/24` | `10.10.20.1` |
| **30** | Desarrollo | `10.10.30.0/24` | `10.10.30.1` |
| **40** | Calidad | `10.10.40.0/24` | `10.10.40.1` |
| **50** | Administración | `10.10.50.0/24` | `10.10.50.1` |
| **60** | TI | `10.10.60.0/24` | `10.10.60.1` |
| **70** | WiFi Corporativo | `10.10.70.0/24` | `10.10.70.1` |
| **80** | Data Center / Servidores | `10.10.80.0/24` | `10.10.80.1` |
| **99** | Gestión / Administración de Red | `10.10.99.0/24` | `10.10.99.1` |

---

### 3. Servidores de Red (VLAN 80 - Data Center)
Configuración estática de los servicios centralizados de la empresa:

| Servidor | Servicio | Dirección IP | Máscara | Gateway | DNS Server |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **SRV-DHCP** | Asignación Dinámica de IP | `10.10.80.10` | `255.255.255.0` | `10.10.80.1` | `10.10.80.11` |
| **SRV-DNS** | Resolución de Nombres | `10.10.80.11` | `255.255.255.0` | `10.10.80.1` | `10.10.80.11` |
| **SRV-WEB** | Servidor HTTP / HTTPS | `10.10.80.12` | `255.255.255.0` | `10.10.80.1` | `10.10.80.11` |
| **SRV-MAIL** | Correo Electrónico Corporativo | `10.10.80.13` | `255.255.255.0` | `10.10.80.1` | `10.10.80.11` |
| **SRV-FTP** | Transferencia de Archivos | `10.10.80.14` | `255.255.255.0` | `10.10.80.1` | `10.10.80.11` |
| **SRV-TFTP** | Respaldo de IOS / Config. | `10.10.80.15` | `255.255.255.0` | `10.10.80.1` | `10.10.80.11` |

---

## Pruebas de Conectividad y Funcionamiento

### 1. Pruebas de Ping (ICMP)
Verificación del enrutamiento inter-VLAN y conectividad hacia la infraestructura:

![Prueba Ping](PRUEBAS%20DE%20CONECTIVIDAD/PRUEBA_PING.png)

### 2. Acceso a Servicios Web
Verificación del servicio HTTP desde los hosts clientes navegando hacia el servidor web corporativo:

![Prueba Web](PRUEBAS%20DE%20CONECTIVIDAD/PRUEBA_WEB.png)

---

## Estructura del Repositorio
```text
├── CONFIGURACION/              # Script de configuración CLI de switches y routers
├── PRUEBAS DE CONECTIVIDAD/    # Evidencias de pruebas ping y servicios web
├── TOPOLOGIA DE RED/           # Esquema gráfico de la topología
├── .gitignore                  # Exclusión de archivos pesados
└── README.md                   # Documentación principal del proyecto
```
---

**Autor:** Erick Isaias Huallanca Perez  
**Contacto:** [LinkedIn](https://www.linkedin.com/in/erick-isaias-huallanca-perez-925b7632b) | [huallanca999s@gmail.com](mailto:huallanca999s@gmail.com)
