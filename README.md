# 🛡️ AdGuard Home: DNS Sinkhole con Docker

Despliegue profesional de un servidor **AdGuard Home** utilizando **Docker Compose**. El sistema actúa como un servidor DNS intermedio (Sinkhole) para centralizar el filtrado de publicidad, rastreadores y malware en toda una red local, sin necesidad de instalar software en cada dispositivo cliente.

<img width="1466" height="773" alt="Screenshot 2026-02-02 at 10 17 50 a m" src="https://github.com/user-attachments/assets/ccfac5a8-b3c4-401a-b1cc-a075f60616d8" />


## 📋 Requisitos Previos

* **Sistema Operativo:** Ubuntu Server 20.04/22.04 LTS (o cualquier distribución Linux basada en Debian/RHEL).
* **Docker Engine & Docker Compose:** Instalados y configurados.
* **Privilegios:** Usuario con permisos `sudo` y agregado al grupo `docker`.
* **Red:** Dirección IP estática privada asignada al servidor (ej. `192.168.x.x`).

---

## 🛠️ Estructura del Proyecto (IaC)

Este proyecto sigue principios de **Infraestructura como Código**. Los datos volátiles se ignoran mediante `.gitignore` para mantener el repositorio limpio.

* `docker-compose.yml`: Orquestación del contenedor, mapeo de puertos y volúmenes.
* `.gitignore`: Exclusión de directorios de datos locales (`workdir/`, `confdir/`).

---

## 🚀 Guía de Instalación Paso a Paso

Sigue este procedimiento estricto para evitar conflictos de red en Linux.

### 1. Preparación del Host (Liberar Puerto 53)
Ubuntu utiliza por defecto `systemd-resolved` en el puerto 53, lo que impide que AdGuard funcione. Debemos desactivarlo:

1. Edita la configuración:
   ```bash
   sudo nano /etc/systemd/resolved.conf
   Descomenta y modifica la línea DNSStubListener:

Ini, TOML
DNSStubListener=no

Reinicia el servicio y repara el DNS local:

Bash
sudo systemctl restart systemd-resolved
sudo rm /etc/resolv.conf && sudo ln -s /run/systemd/resolve/resolv.conf /etc/resolv.conf
2. Despliegue del Contenedor
Crea el directorio del proyecto:

Bash
mkdir adguard-project && cd adguard-project
Genera el archivo docker-compose.yml con el siguiente contenido:

YAML
services:
  adguardhome:
    image: adguard/adguardhome
    container_name: adguardhome
    restart: unless-stopped
    volumes:
      - ./workdir:/opt/adguardhome/work
      - ./confdir:/opt/adguardhome/conf
    ports:
      - "53:53/tcp"
      - "53:53/udp"
      - "80:80/tcp"
      - "3000:3000/tcp"
Ejecuta el contenedor en segundo plano:

Bash
docker compose up -d
3. Configuración Inicial (Wizard)
Accede desde un navegador web a: http://[IP-DEL-SERVIDOR]:3000

Configura las interfaces de escucha:

Admin Web Interface: Puerto 80.

DNS Server: Puerto 53.

Crea las credenciales de administrador.

🔒 Capacidades del "Guardián": Configuración y Uso
AdGuard Home no es solo un bloqueador de anuncios; es una herramienta de gestión de red.

1. Bloqueo de Servicios (Productividad)
Permite restringir el acceso a plataformas enteras con un clic.

Ruta: Filtros > Servicios bloqueados.

Aplicación: Ideal para bloquear redes sociales (TikTok, Facebook, Instagram) en horarios de estudio o trabajo.

2. Control Parental y SafeSearch
Protección para menores y filtrado de contenido explícito.

Ruta: Configuración > Configuración de filtros.

Aplicación: Fuerza el modo "Seguro" en buscadores como Google, Bing y YouTube, anulando la configuración local del usuario.

3. Listas de Bloqueo (DNS Blocklists)
Gestión avanzada de tráfico no deseado.

Ruta: Filtros > Listas de bloqueos de DNS.

Aplicación: Implementación de listas OISD o StevenBlack para detener telemetría de Windows, rastreadores de móviles y Phishing.

4. Auditoría de Red (Query Log)
Ruta: Pestaña Registro de consultas.

Aplicación: Análisis forense de tráfico. Permite identificar dispositivos comprometidos (botnets) o aplicaciones que "llaman a casa" excesivamente.

💻 Escenarios de Implementación
Escenario A: Laboratorio Virtual (VM)
Entorno de pruebas usando VirtualBox/UTM.

Host: Máquina real (Mac/Windows).

Guest: Ubuntu Server (Docker).

Configuración: Cambiar el DNS de la tarjeta de red del Host apuntando a la IP de la VM.

<img width="655" height="412" alt="Screenshot 2026-02-02 at 10 21 52 a m" src="https://github.com/user-attachments/assets/15ae8562-461e-4dc6-a401-73ef6c599fa9" />


Escenario B: Implementación Real (Hogar/Oficina)
Entorno de producción 24/7.

Hardware: Raspberry Pi o Mini PC dedicado conectado vía Ethernet.

Router: Acceder a la configuración WAN/LAN del ISP.

Configuración: Establecer la IP del servidor AdGuard como DNS Primario. Se recomienda usar 8.8.8.8 como Secundario (Failover).


👤 Autor
Jan Cobian Ingeniero en sistemas computacionales
