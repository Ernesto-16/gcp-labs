# Investigación de Incidentes de Seguridad y Auditoría de Recursos en GCP con Cloud Logging y BigQuery

> **Basado en el laboratorio:** *Analiza registros de auditoría con BigQuery (Google Cloud Skills Boost)*  
> **Entorno:** Google Cloud Platform (GCP)  
> **Herramientas:** Cloud Logging, BigQuery, Cloud Shell, gcloud CLI, Compute Engine, Cloud Storage, Cloud Audit Logs.

---

##  Objetivos 

* **Simulación de Eventos de Auditoría:** Generar actividad administrativa real (creación y eliminación de buckets e instancias de Compute Engine) mediante la CLI `gcloud` en Cloud Shell.
* **Exportación y Enrutamiento de Registros:** Configurar un receptor de enrutamiento (*sink*) en Cloud Logging para transmitir eventos de auditoría en tiempo real hacia BigQuery (`auditlogs_dataset`).
* **Análisis de Registros de Actividad:** Aplicar filtros en el Explorador de Registros por servicio (`storage.googleapis.com`) y método (`storage.buckets.delete`) para identificar la identidad del usuario (`principalEmail`).
* **Consultas SQL de Seguridad en BigQuery:** Ejecutar consultas SQL parametrizadas en BigQuery sobre las tablas de auditoría para identificar usuarios que eliminaron recursos en los últimos 7 días.

##  Arquitectura


## Creación de recursos
```bash

gcloud storage buckets create gs://$DEVSHELL_PROJECT_ID

echo "this is a sample file" > sample.txt

gcloud storage cp sample.txt gs://$DEVSHELL_PROJECT_ID

gcloud compute networks create mynetwork --subnet-mode=auto

export ZONE=$(gcloud compute project-info describe \
--format="value(commonInstanceMetadata.items[google-compute-default-zone])")

gcloud compute instances create default-us-vm \
--machine-type=e2-micro \
--zone=$ZONE --network=mynetwork

gcloud storage rm --recursive gs://$DEVSHELL_PROJECT_ID

```
###  Desglose del Comando no incluido en el laboratorio

| Componente | Descripción |
| :--- | :--- |
| `gcloud` | Es el comando principal para administrar, crear, modificar y borrar todos los recursos de Google Cloud. |
| `storage` | Especificamos el grupo de recursos general que queremos utilizar. |
| `buckets` | Especificamos el subgrupo específico con el que en este caso vamos a crear el bucket. |
| `create` | Especificamos qué vamos a hacer con ese subgrupo específico (`buceket`). |
| `gs://` | Es el protocolo que los servicios de Google Storage usan  para indicar a dónde va la petición. |
| `$DEVSHELL_PROJECT_ID` | Nos da automáticamente el ID del proyecto y le pone ese nombre al recurso en conjunto con los anteriores . |
| `echo "..." > sample.txt` | El operador > es un "redireccionador". En lugar de imprimir el texto en la pantalla, redirige esas palabras y las inyecta en un archivo nuevo llamado sample.txt dentro del disco duro local de tu consola. |
| `cp` | Copia el archivo local sample.txt y lo transfiere hacia el bucket que creaste en el paso anterior. |
| `gcloud compute networks create mynetwork --subnet-mode=auto` | Cambiamos de módulo y en este modulo compute interactúa con Compute Engine, el servicio de Google para crear redes y máquinas virtuales (servidores) después crea una nube privada virtual (VPC) y la llama mynetwork(es como comprar el router y los cables virtuales para conectar tus futuros servidores  y dentro de este comando también Crea automáticamente una subred con direcciones IP listas para usarse en cada una de tus regiones a nivel mundial. |
| `export ZONE=...` | El comando export crea una variable temporal en la memoria de la terminal llamada ZONE. |
| `$( ... )` | Todo lo que está entre el $  y los paréntesis se ejecuta y su resultado de texto se guarda dentro de la variable ZONE. |
| `project-info describe` | Obtiene toda la configuración global de tu proyecto. |
| `\` |   Operador para indicar continuación de instrucciones. |
| `--format="value(...)"` | Indicamos un formato especifico del que queremos el valor para que nos devuelva algo en especifico en este caso una zona |
| `commonInstanceMetadata (Metadatos comunes de las instancia)` |  Son las configuraciones globales que deben aplicarse por defecto a todas las computadoras (máquinas virtuales/instancias). |
| `.items[google-compute-default-zone]` | entrar dentro de esa sección items. y busque dentro google-compute-default-zone(En conjunto desde el comandod el formato obtenemos el valor de la zona) |
| `gcloud compute instances create default-us-vm` |  Con el grupo de recursos compute y subgrupo especifico instances crea una instancia llamada  default-us-vm. |
| `--machine-type=e2-micro` | Define el hardware del servidor. La familia e2-micro es una máquina muy pequeña, barata (suele entrar en la capa gratuita) que comparte procesador con otros usuarios y tiene solo 1 GB de RAM. |
| `--zone=$ZONE` | Define la ubicación física (zona que es un conjunto de servidores) donde se creará el servidor, con  el valor que guardamos en la variable $ZONE . |
| `--network=mynetwork` | La conectamos a al red que creamos |
| `rm` | borrar. |
| `--recursive` | Fuerza la eliminación en conjunto con el comando anterior de el archivo sample.txt primero, y luego destruye el bucket vacío. |
| 

<img width="1093" height="646" alt="image" src="https://github.com/user-attachments/assets/a63297b1-8b00-426f-b9d9-4ad20f0fe880" />
#Falta foto y explciacion del comando al entrar ala explorador d earchivos


