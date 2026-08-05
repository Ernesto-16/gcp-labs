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
<img width="929" height="631" alt="image" src="https://github.com/user-attachments/assets/10bf23b7-2337-4efe-afca-f0216cc7ed67" />


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

### Filtro de  Registros de auditoría de Cloud
```bash
logName = ("projects/Project ID/logs/cloudaudit.googleapis.com%2Factivity")
```
Explicación de comando:
- **`logName =`**: Es el comando para filtrar exclusivamente por el "nombre del registro .
- **`projects/Project ID/logs/`**: Es una ruta dentro de Google Cloud. Le indica que busque dentro de los registros de un proyecto en particular (donde "Project ID" se reemplazaría por el ID real del proyecto) .
- **`cloudaudit.googleapis.com`**: Es el servicio exacto que generó el registro. En este caso, son los Cloud Audit Logs (Registros de auditoría).
- **`%2F`**: Es para evitar que la computadora se confunda y sepa cuándo un símbolo es "solo texto" y no una instrucción  ejemplos: Un espacio en blanco se disfraza como %20,El símbolo @ se disfraza como %40 y la barra diagonal (/) se disfraza como %2F.
- **`activity`**: Es la categoría del registro. Los registros de "Actividad de administración" graban cada vez que alguien crea, modifica o elimina un recurso.

  Falta foto de el explordor de regsistros

### Resultados

## Receptor de enrutamiento de registros.
Nos sirve para guardar por años registros (para auditorías legales) o para analisis  con herramientas de bases de datos se crea un "Sink" con una regla (ej. "Toma todos los registros de seguridad") y los envías automáticamente a destinos como BigQuery,Cloud Storage,Pub/Sub.
En este acso se envairan a BigQuery para consultas en lenguaje SQL.
<img width="1062" height="495" alt="image" src="https://github.com/user-attachments/assets/19dfb83c-cb76-4b8d-ad99-0ec7aa7cb871" />  

Comprabación de la creación del sink  

<img width="1280" height="409" alt="image" src="https://github.com/user-attachments/assets/ceb7ec30-719a-4520-a0bd-b759c9677217" />

## Regeración de máa acatividad atravéz de la Cloud Shell
```bash

gcloud storage buckets create  gs://$DEVSHELL_PROJECT_ID

gcloud storage buckets create  gs://$DEVSHELL_PROJECT_ID-test

echo "this is another sample file" > sample2.txt

gcloud storage cp sample.txt gs://$DEVSHELL_PROJECT_ID-test

export ZONE=$(gcloud compute project-info describe \
--format="value(commonInstanceMetadata.items[google-compute-default-zone])")

gcloud compute instances delete --zone=$ZONE \
--delete-disks=all default-us-vm

```
Explicación de los comandos
gcloud storage buckets create  gs://$DEVSHELL_PROJECT_ID
Este comando crea un nuevo espacio de almacenamiento (bucket) en Google Cloud. Utiliza la variable de entorno del ID de proyecto para el nombre del bucket.
gcloud storage buckets create  gs://$DEVSHELL_PROJECT_ID-test
Crea un segundo espacio de almacenamiento de manera idéntica al primero, pero con la terminación "-test" al nombre.

echo "this is another sample file" > sample2.txt
Esta línea crea un archivo de texto en la terminal llamado sample2.txt. El operador > toma la frase que está entre comillas y coloca en el archivoarchivo.

gcloud storage cp sample.txt gs://$DEVSHELL_PROJECT_ID-test
Toma el archivo sample.txt y lo copia hacia tu bucket de pruebas. 

export ZONE=$(gcloud compute project-info describe --format="value(commonInstanceMetadata.items[google-compute-default-zone])")
Consulta las configuraciones internas de tu proyecto para averiguar en qué ubicación física (zona) por defecto. Luego, extrae únicamente ese nombre (ej. us-central1-a) y lo guarda en la memoria de la terminal con el nombre de $ZONE.

gcloud compute instances delete --zone=$ZONE --delete-disks=all default-us-vm
Busca y destruye la máquina virtual (servidor) llamado default-us-vm ubicada en la zona que se obtivo en el en el coamndo anaterior .El parámetro --delete-disks=all sirbe para que  el disco duro asociado también se borre para evitar tener caragos adicionales a pesar de no usarlo.

gcloud storage rm --recursive  gs://$DEVSHELL_PROJECT_ID
Elimina el primer bucket. El parámetro --recursive (recursivo) sirve para  entrar al bucket y borrar todos los archivos que tenga adentro antes de destruir el bucket devido a que Google no permite borrar buckets que no estén vacíos.

gcloud storage rm --recursive gs://$DEVSHELL_PROJECT_ID-test
Elimina el primer bucket. El parámetro --recursive (recursivo)  al igual que el anterior pero ahora es al buket con con la terminación "-test"

Ahora si tomaramos todas las acciones anteriores co0mo un ataque podemos ver lo que paso d ela siguinete manera para ello oucpamos otra cuenta 

Verifcamos nuevamnete 
```bash
logName = ("projects/Project ID/logs/cloudaudit.googleapis.com%2Factivity
protoPayload.serviceName="storage.googleapis.com"
protoPayload.methodName="storage.buckets.delete"
```
Y podemos ver la eliminación de los recursos que se crearón 
posteriormnete al 
En el campo Editor de consultas, observa que se agregó la línea protoPayload.serviceName="storage.googleapis.com" al Compilador de consultas. Esto filtra tu consulta según entradas que coincidan con storage.googleapis.com

protoPayload 
Es  donde se  guardan los detalles de la auditoría de google

methodName(¿Qué hizo?)
Guarda el nombre técnico de la acción que se intentó hacer (storage.buckets.delete o compute.instances.insert).
Sirve para saber cuál era la intención,ayuda a distinguir si la persona solo entró a mirar la configuración del servidor, si intentó crear uno nuevo, o si  se  destruyo.

 serviceName (¿A qué afectó ?)
 Es que servicio de multiplies funciones fue afectado 
 
"storage.googleapis.com"
Se refiere a Cloud Storage (buckets)

"storage.buckets.delete"
Búsca específicamente la acción  de eliminar un bucket

resourceName(¿A qué afectó especificamnete?)

Es la ruta exacta del objeto que fue afectado.
Sirve para saber que recurso fue afectado por la acción .Por ejemplo si hay 20 buckets y alguien borró uno, este campo  dice  el nombre del disco duro específico que fue eliminado.

En conjunto vemos :
Linea 1 
Descarta automáticamente cualquier registro de red, de errores de sistema o de simple lectura.para ver solo los eventos donde alguien modificó o destruyó algo.
Linea 2

De todas las  actividades administrativas enontradas con el coamndo anterior, se centra solamente  en el servicio de Cloud Storage".Con esto descartamos cualquier modificación que se le haya hecho a bases de datos, redes o servidores virtuales.

Linea 3
De todo lo anterior  únicamente nos va dar ahora las  eliminaciones de buckest descartando si alguien creó un bucket nuevo o si le cambió los permisos

Al final tenemos la ruta exacta en canbio si solo ocupamos laa linea 3 sera más lento ya que no descartamos nada y es más  costoso por ello es buena practractiva buscara por capas hastala llegara  alao que queremos.

## SQL BIGQUERY
```bash
SELECT
  timestamp,
  resource.labels.instance_id,
  protopayload_auditlog.authenticationInfo.principalEmail,
  protopayload_auditlog.resourceName,
  protopayload_auditlog.methodName
FROM
`auditlogs_dataset.cloudaudit_googleapis_com_activity_*`
WHERE
  PARSE_DATE('%Y%m%d', _TABLE_SUFFIX) BETWEEN
  DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY) AND
  CURRENT_DATE()
  AND resource.type = "gce_instance"
  AND operation.first IS TRUE
  AND protopayload_auditlog.methodName = "v1.compute.instances.delete"
ORDER BY
  timestamp,
  resource.labels.instance_id
LIMIT
  1000;
```
bloque select- from= Con la palabra select como lo dise selecionamos lo que queremos ver al final en este caso:
timestamp: La fecha y hora exacta en la que se ejecutó la acción.
resource.labels.instance_id: El número de identificación único de la máquina virtual que fue afectada
...principalEmail: El "quién". Muestra el correo electrónico del usuario o la cuenta de servicio que ordenó la eliminación
...resourceName: La ruta completa de GCP hacia el recurso que fue eliminado (incluye el proyecto y la zona)
...methodName: El nombre del método de la API que se llamó.
`auditlogs_dataset.cloudaudit_googleapis_com_activity_*`: Apunta a tus registros de actividad administrativa (Admin Activity logs). El 
FROM (De dónde sacamos los datos)
(*):asterisco (*) al final  indica que estás consultando una tabla fragmentada (sharded table) dividida por días ya que google crea una tabal por dia y de esta forma las juntamso en 1 sola.
auditlogs_dataset: Es el "Dataset" (conjunto de datos) que es donde guardamos todos los registros de auditoría.
(.)separamso alcarpeta del arachivo.
cloudaudit_googleapis_com_activity_: Es el prefijo del nombre de la tabla. Google nombra así por defecto a todas las tablas que registran "actividades administrativas" (quién creó, modificó o eliminó algo).
PARSE_DATE toma ese texto y, usando el formato %Y%m%d (Año de 4 dígitos, Mes de 2, Día de 2), lo convierte en un objeto de fecha real


DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY) AND
  CURRENT_DATE():Calcula la fecha de hoy le résta 7 días y con BETWEEN busca los datos que esten entre  esa fecha y hoy 
  resource.type = "gce_instance": Al asignar que tipo de recurso descartamos  bases de datos, redes o almacenamiento.dejando solo las instancias de Compute Engine.
  _TABLE_SUFFIX: es una varible que recibe el vlor de las consultas frgmnetadas 
  
operation.first IS TRUE:Nos ayuda  a que la consulta nos devuelvva solo el priemr eventoq ue coincida
...methodName = "v1.compute.instances.delete": El gatillo exacto. Le dice a la consulta que solo busque acciones de borrado
ORDER BY timestamp, resource.labels.instance_id: con lsiguiente prioridad se ordena
Ordena todas las filas desde la más antigua hasta la más reciente. devido al uso de timestamp
prioridad 2:BigQuery usa el ID de la instancia. Si los tiempos son iguales, ordenará esas filas específicas por orden alfabético o numérico según su ID 
al final de  todo  el filtrado de toda la información que queremso limutamos los resulatdos a olo 1000 registros 

al finald e una forma epxlicita comos si hableramso al dataset pidiendole algo con palabras:

Ve a mi base de datos (auditlogs_dataset) y, usando el asterisco (*) como comodín, atrapa y junta todas las tablas diarias fragmentadas que comiencen con el nombre cloudaudit_googleapis_com_activity_.

Sin embargo, para no gastar dinero escaneando años de historial, revisa el texto exacto que el asterisco está reemplazando en el nombre de cada tabla usando la variable _TABLE_SUFFIX. Convierte ese texto en una fecha matemática (PARSE_DATE) y abre únicamente las tablas cuyo sufijo de fecha caiga entre los últimos 7 días y el día de hoy; al resto ni las toques.

Ahora sí, de las tablas que abriste, fíltrame los eventos donde el recurso afectado sea estrictamente una máquina virtual (gce_instance), donde la orden específica haya sido eliminarla (v1.compute.instances.delete), y asegúrate de capturar solo el registro inicial de esa operación (operation.first) para no mostrarme filas duplicadas.

De los eventos que pasen todos estos filtros, extráeme estos 5 datos: la fecha/hora exacta, el ID único de la máquina, el correo de quien ejecutó el borrado, la ruta completa del recurso en la nube y el nombre del método usado


ahora al ejecutar la siguiente consulta similar a la anterior
canbiando:
En el SELECT, cambia instance_id por bucket_name. En el WHERE, cambia gce_instance por gcs_bucket. Esta consulta ya no rastrea máquinas 
virtuales, ahora rastrea quién borró Buckets de Cloud Storage
En el WHERE, cambia el método a "storage.buckets.delete
operation.first IS TRUE ya que borrar un buket s más rpido
Todo elc omando dise :

Ve a mi base de datos (auditlogs_dataset) y, usando el asterisco (*) como comodín, atrapa y junta todas las tablas diarias fragmentadas que comiencen con el nombre cloudaudit_googleapis_com_activity_.

De nuevo, para no gastar dinero escaneando años de historial, revisa el texto exacto que el asterisco está reemplazando usando la variable _TABLE_SUFFIX. Convierte ese texto en una fecha matemática (PARSE_DATE) y abre únicamente las tablas cuyo sufijo caiga entre los últimos 7 días y el día de hoy.

De las tablas que sí abriste, fíltrame los eventos donde el recurso afectado sea estrictamente un Bucket de Cloud Storage (gcs_bucket) y donde la orden específica haya sido eliminarlo (storage.buckets.delete).

De los eventos que cumplan eso, extráeme estos 5 datos: la fecha/hora exacta, el nombre del bucket, el correo de quien lo borró, la ruta completa del recurso y el nombre del método usado.

Finalmente, entrégame esta lista ordenada cronológicamente por fecha. Si dos buckets se borraron exactamente al mismo tiempo, usa el nombre del bucket (bucket_name, no instance_id) para desempatar, y ponle un tope máximo de 1000 resultados en pantalla

lo anterior tomando enc uenta todo pero de manera simle solo
devuelve los usuarios que borraron buckets de Cloud Storage en los últimos 7 días

```bash
SELECT
  timestamp,
  resource.labels.bucket_name,
  protopayload_auditlog.authenticationInfo.principalEmail,
  protopayload_auditlog.resourceName,
  protopayload_auditlog.methodName
FROM
`auditlogs_dataset.cloudaudit_googleapis_com_activity_*`
WHERE
  PARSE_DATE('%Y%m%d', _TABLE_SUFFIX) BETWEEN
  DATE_SUB(CURRENT_DATE(), INTERVAL 7 DAY) AND
  CURRENT_DATE()
  AND resource.type = "gcs_bucket"
  AND protopayload_auditlog.methodName = "storage.buckets.delete"
LIMIT
  1000;
ORDER BY
  timestamp,
  resource.labels.bucket_name
LIMIT
  1000;
```

