# Despliegue y administración de un servidor web Apache2 en Ubuntu Server

**Proyecto académico de ASIR | Administración de sistemas, redes y fundamentos de seguridad web**

## Resumen

En este laboratorio desplegué un sitio web para la empresa ficticia **TecnoWeb Solutions** utilizando **Ubuntu Server 26.04.1 LTS** y **Apache2** en una máquina virtual de VMware Workstation Pro. Configuré el acceso desde un equipo Windows mediante un nombre de dominio local, habilité HTTP y HTTPS con un certificado autofirmado y consulté los registros de Apache para comprobar solicitudes y diagnosticar errores.

> **Alcance:** laboratorio académico en red local. El dominio `www.tecnoweb.local` no es un sitio público. El certificado autofirmado permite probar TLS, pero no ofrece confianza automática en el navegador.

## Tecnologías

- Ubuntu Server 26.04.1 LTS
- VMware Workstation Pro (máquina virtual de 3 GB RAM, 1 CPU, 20 GB de disco y red NAT)
- Apache HTTP Server (`apache2`)
- HTTP, HTTPS/TLS, TCP/IP
- SCP sobre SSH para transferir archivos desde Windows
- Virtual Hosts, archivo `hosts`, OpenSSL y registros de Apache

## Objetivos

1. Preparar una máquina virtual Ubuntu Server y comprobar su configuración de red.
2. Instalar Apache2 y verificar el estado del servicio y su escucha en el puerto 80.
3. Transferir y publicar una página web en `/var/www/html`.
4. Configurar un Virtual Host y el nombre local `www.tecnoweb.local`.
5. Habilitar HTTPS mediante un certificado autofirmado y comprobar la escucha en el puerto 443.
6. Consultar registros de acceso y errores y comprobar el registro de una solicitud HTTP 404.

## Desarrollo

### 1. Preparación del entorno

Creé una máquina virtual Ubuntu Server en VMware Workstation Pro, con conectividad NAT. Revisé la versión del sistema, el nombre del equipo y las interfaces de red:

```bash
cat /etc/os-release
hostname
ip a
```

### 2. Instalación y administración de Apache2

Actualicé la información de paquetes, instalé Apache2 y comprobé su estado, el inicio automático y la escucha HTTP:

```bash
sudo apt update
sudo apt install apache2
sudo systemctl status apache2
systemctl is-enabled apache2
sudo ss -tlnp | grep ':80'
```

También practiqué detener e iniciar el servicio utilizando `systemctl`.

### 3. Publicación del sitio web

Transferí los archivos de la web desde Windows a Ubuntu Server mediante **SCP** y los ubiqué en `/var/www/html`.

Durante el despliegue apareció un error **403 Forbidden** relacionado con el acceso a los archivos/directorios del sitio. Revisé y ajusté permisos hasta poder mostrar la página. **Nota documental:** en el informe original se escribió `chown 755`; el comando habitual para establecer permisos numéricos es `chmod 755`. El comando exacto ejecutado debe verificarse en las evidencias originales antes de documentarlo como tal.

### 4. Dominio local y Virtual Host HTTP

Configuré la resolución local de `www.tecnoweb.local` mediante el archivo `hosts` y creé un Virtual Host en:

```text
/etc/apache2/sites-available/tecnoweb.conf
```

La configuración incluía `ServerName`, `DocumentRoot` y archivos de registro específicos de accesos y errores. Habilité el sitio con:

```bash
sudo a2ensite tecnoweb.conf
```

Comprobé desde Windows el acceso a la web mediante HTTP y el nombre de dominio local.

### 5. Habilitación de HTTPS

Activé el módulo SSL, comprobé la sintaxis de configuración y la carga del módulo:

```bash
sudo a2enmod ssl
sudo apache2ctl configtest
sudo apache2ctl -M | grep ssl
```

Preparé `/etc/apache2/ssl` para el certificado y la clave privada de un **certificado autofirmado generado con OpenSSL**. También configuré un Virtual Host HTTPS, recargué Apache y comprobé que atendía conexiones en el puerto TCP 443. La web se verificó desde un navegador Windows.

**Nota de seguridad:** no se incluyen certificados privados ni claves en este repositorio. Un certificado autofirmado no es automáticamente confiable para el navegador.

### 6. Registros y diagnóstico

Consulté los archivos de registro ubicados en `/var/log/apache2/`, incluidos los registros separados de acceso y errores para HTTP y HTTPS. Para examinar las últimas peticiones HTTPS utilicé:

```bash
sudo tail -n 10 /var/log/apache2/tecnoweb_ssl_access.log
```

Realicé una solicitud deliberada a una página inexistente:

```text
https://www.tecnoweb.local/noexiste.html
```

Apache devolvió **404 Not Found** y localicé la petición en el log de acceso. El laboratorio también incluyó un incidente **403 Forbidden** relacionado con permisos durante la publicación del sitio.

## Evidencias del laboratorio

Las capturas seleccionadas se incorporarán en la carpeta `capturas/`, tras revisar los datos visibles y las autorizaciones necesarias:

- Preparación de la máquina virtual y comprobaciones iniciales.
- Estado y escucha de Apache2 en el puerto 80.
- Sitio web publicado y accesible desde Windows por HTTP.
- Virtual Host y comprobaciones de HTTPS en el puerto 443.
- Comprobación de registros y respuesta 404.

## Aprendizajes

Este proyecto reforzó mis conocimientos sobre administración de Linux, despliegue de servicios web, resolución local de nombres, Virtual Hosts, transferencia segura de archivos, certificados TLS de laboratorio y análisis de logs para diagnosticar incidencias.

## Autor

**Eduardo Medialdea Dominguez**  
Estudiante de Administración de Sistemas Informáticos en Red (ASIR), ILERNA.

---

*Documentación propia basada en una práctica académica. No se publican datos privados, claves ni material docente sin autorización.*
