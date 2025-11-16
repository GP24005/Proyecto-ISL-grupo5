# Proyecto Final - Introducción al Software Libre

**Universidad de El Salvador** 
Facultad Multidisciplinaria de Occidente 
Departamento de Ingeniería y Arquitectura 
Ingeniería en Desarrollo de Software 
**Ciclo II – 2025**
#Docente: Ing. Graciela Albaluz

#Integrantes:

## JASON ALEXANDER MOLINA ORTIZ
## RODRRIGO ERNESTO GARCIA PORTILLO
## BRANDON EMMANUEL MORALES BOLAÑOS
## CINDY ARIANA REYES MOLINA

#Objetivo

Aplicar los conocimientos adquiridos en la asignatura *Introducción al Software Libre* para implementar un servidor Linux automatizado mediante tecnologías de contenedores (Docker), integrando prácticas de administración de sistemas, control de versiones y virtualización.

## Estructura del Proyecto

```
/proyecto/
├── datos/       # Archivos de configuración (grupo: soporte)
├── web/         # Archivos del sitio web (grupo: web)
├── scripts/     # Scripts de automatización
├── capturas/    # Evidencias del proyecto
└── README.md    # Documentación del proyecto
```


## 1. Preparación del Entorno Servidor

## 1.1 Administración Básica del Sistema

- Hostname configurado como: `servidor-grupo5`
- Usuarios creados:
  - `adminsys` (con privilegios sudo)
  - `tecnico` (pertenece al grupo `soporte`)
  - `visitante` (pertenece al grupo `web`)
- Grupos creados: `soporte`, `web`

```bash
sudo hostnamectl set-hostname servidor-grupoX
sudo useradd -m -G sudo adminsys
sudo useradd -m tecnico
sudo useradd -m visitante
sudo groupadd soporte
sudo groupadd web
sudo usermod -aG soporte tecnico
sudo usermod -aG web visitante
```

## 1.2 Estructura de Directorios y Permisos

```bash
sudo mkdir -p /proyecto/{datos,web,scripts,capturas}
sudo chown :soporte /proyecto/datos
sudo chown :web /proyecto/web
sudo chmod 2775 /proyecto/datos /proyecto/web
```


## 2. Automatización y Monitoreo

## 2.1 Script de Monitoreo

Archivo: `/proyecto/scripts/reporte_sistema.sh`

```bash
#!/bin/bash
echo "Fecha y hora: $(date)"
echo "Hostname: $(hostname)"
echo "Usuarios conectados: $(who | wc -l)"
echo "Espacio libre en disco: $(df -h / | awk 'NR==2 {print $4}')"
echo "Memoria RAM disponible: $(free -h | awk '/Mem:/ {print $4}')"
echo "Contenedores Docker activos: $(docker ps -q | wc -l)"
```

```bash
sudo chmod +x /proyecto/scripts/reporte_sistema.sh
```

### 2.2 Automatización con Cron

```bash
sudo mkdir -p /var/log/proyecto
(crontab -l 2>/dev/null; echo "*/30 * * * * /proyecto/scripts/reporte_sistema.sh >> /var/log/proyecto/reporte_sistema.log") | crontab -
```

Verificación:

```bash
tail /var/log/proyecto/reporte_sistema.log
```


## 3. Control de Versiones

## 3.1 Repositorio Git Local y Remoto

```bash
cd /proyecto/
git init
git add .
git commit -m "Primer commit: estructura base del proyecto"
git remote add origin https://github.com/GP24005/Proyecto-ISL-grupo5
git push -u origin master
```


## 4. Docker

## 4.1 Instalación y Configuración

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker
sudo usermod -aG docker adminsys
sudo usermod -aG docker tecnico
```

Verificación:

```bash
docker --version
```

### 4.2 Verificación Inicial

```bash
docker run hello-world
docker ps -a
```

## 5. Servidor Web Containerizado

## 5.1 Contenedor Nginx Básico

1. Crear archivo `/proyecto/web/index.html` con contenido personalizado:



2. Ejecutar contenedor Nginx:

```bash
docker run -d \
  --name nginx-grupo5 \
  -p 8080:80 \
  -v /proyecto/web/:/usr/share/nginx/html/ \
  nginx
```

### 5.2 Verificación del Servicio Web

- Acceder desde navegador: [http://localhost:8080](http://localhost:8080)
- Verificar logs del contenedor:

```bash
docker logs nginx-grupo5
```

---

## ✅ Estado Actual

- [x] Hostname configurado
- [x] Usuarios y grupos creados
- [x] Directorios y permisos asignados
- [x] Script de monitoreo funcional
- [x] Cronjob activo
- [x] Repositorio Git local y remoto configurado
- [x] Docker instalado y verificado
- [x] Contenedor Nginx básico desplegado y accesible
