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

