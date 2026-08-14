# Despliegue y Administración de Servidor Dedicado Linux (Minecraft Bedrock Edition)

## 📌 Descripción del Proyecto
Implementación, configuración y resolución de incidencias en el despliegue de un servidor dedicado de Minecraft Bedrock sobre un entorno virtualizado **Ubuntu Server 24.04 LTS** en **Hyper-V**. 

El objetivo principal de este laboratorio fue configurar un entorno de servidor funcional, resolver bloqueos de resolución de nombres a nivel de red (DNS) y garantizar la ejecución persistente del proceso en segundo plano (24/7).

---

## 🛠️ Tecnologías y Herramientas Utilizadas
* **Sistema Operativo Target:** Ubuntu Server (Linux)
* **Hypervisor:** Microsoft Hyper-V
* **Protocolos y Redes:** TCP/UDP, DNS (`systemd-resolved`), IPv4
* **Utilidades CLI:** `curl`, `wget`, `nano`, `systemctl`, `screen`, `unzip`
* **Servicio:** Minecraft Bedrock Dedicated Server (v1.21.20.03)

---

## 🚀 Arquitectura y Proceso de Despliegue

### 1. Preparación del Directorio de Trabajo
Creación de la estructura de carpetas aislada para el servicio:
```bash
mkdir ~/minecraft && cd ~/minecraft

```

### 2. Gestión de Persistencia de Servicio
Uso de multiplexadores de terminal (screen) para poder desacoplar el proceso de la sesión activa de SSH/Shell y permitir ejecución continua en segundo plano:
# Creación de sesión dedicada
screen -S minecraft

# Ejecución de la aplicación con bibliotecas compartidas vinculadas
LD_LIBRARY_PATH=. ./bedrock_server



## ⚡ Troubleshooting y Resolución de Incidencias
Durante la fase de aprovisionamiento se presentaron diversos fallos técnicos que requirieron diagnóstico y remediación a nivel de sistema y red:

### ⚙️ Incidencia 1: Fallo de Resolución DNS (curl: (6) Could not resolve host)
Problema: Al intentar descargar los binarios oficiales desde el dominio del proveedor, el sistema no podía traducir la URL a una dirección IP válida, abortando la petición.

### Diagnóstico: Se ejecutó una prueba de conectividad por IP directa (ping 8.8.8.8), confirmando un 0% de pérdida de paquetes. Esto aisló la falla exclusivamente al resolutor DNS interno del sistema operativo (systemd-resolved).

### Solución: Se sobreescribió la configuración en /etc/resolv.conf apuntando directamente a los servidores DNS públicos primarios y secundarios de Google (8.8.8.8 y 8.8.4.4):

Se verificó la resolución exitosa mediante ping google.com



## ⚙️ Incidencia 2: Endpoints Deprecados en CDN / Azure (minecraft.azureedge.net)
Problema: Los scripts tradicionales y enlaces heredados apuntaban a los antiguos mirrors en Azure CDN (azureedge.net), devolviendo errores de conexión o resoluciones fallidas debido al retiro de dichos endpoints por parte del desarrollador (Mojang).

### Solución: Se identificó la nueva estructura de distribución sobre www.minecraft.net/bedrockdedicatedserver/ y se actualizaron las rutas de descarga a la versión estable actual (1.21.20.03).


## ⚙️ Incidencia 3: Bloqueo de Peticiones Automatizadas por User-Agent
Problema: Las peticiones directas vía wget o curl sin encabezados estándar de navegación eran congeladas (awaiting response...) o resultaban en descargas corruptas de archivos .zip de muy bajo peso (~370 KB), correspondientes a páginas HTML de bloqueo/error de CDN.

### Solución: Se forzó la emulación de encabezados de un navegador cliente legítimo (User-Agent) en la petición curl con redireccionamiento explícito (-L):

curl -A "Mozilla/5.0 (Windows NT 10.0; Win64; x64)" -L -o bedrock-server.zip [https://www.minecraft.net/bedrockdedicatedserver/bin-linux/bedrock-server-1.21.20.03.zip](https://www.minecraft.net/bedrockdedicatedserver/bin-linux/bedrock-server-1.21.20.03.zip)

Resultado: Descarga íntegra del paquete del servidor (~52MB)


## ✅ Resultados
### Servidor Operativo: Escuchando conexiones entrantes en los puertos UDP 19132 (IPv4) y 19133 (IPv6).

### Persistencia: Capacidad de gestión del proceso mediante desacople de terminales sin interrupción del servicio.

### Documentación de Entorno: Laboratorio preparado para migración transparente a la nube (ej. Oracle Cloud / AWS / VPS local).
