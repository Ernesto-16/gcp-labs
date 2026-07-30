# Creación de una VPC con Cloud Shell en Google Cloud Platform

> **Entorno de Laboratorio:** Google Cloud Platform (GCP)  
> **Herramienta:** Google Cloud Shell CLI (`gcloud`)  
> **Objetivo:** Despliegue manual y auditoría de una red VPC personalizada para pruebas de seguridad.

---

##  Objetivos

- **Crear una red VPC personalizada:** Desplegar la red `labnet` en modo personalizado (*custom*) mediante la interfaz de comandos para tener control manual sobre los rangos y la segmentación.
- **Configurar una subred específica:** Definir una subred dentro de la red VPC personalizada utilizando rangos de direcciones IP definidos.
- **Visualizar y auditar redes y subredes:** Inspeccionar las redes y subredes existentes en el proyecto para verificar que la configuración se haya aplicado correctamente y garantizar que el entorno de pruebas esté listo para investigaciones de seguridad.

---

##  Revisión del Entorno

Antes de comenzar con la creación de recursos verificamos la cuenta y el proyecto activo en la sesión de Cloud Shell.

### 1. Verificar la cuenta activa

```bash
gcloud auth list
```

Con el comando anterior veremos qué cuenta está activa en este momento, ya que a veces puede haber más de una cuenta activa (por ejemplo, una cuenta de pruebas y otra corporativa). En el caso de que no estemos en la cuenta que queramos usar, podemos cambiarla con el siguiente comando:

```bash
gcloud config set account [ACCOUNT]
```

Con este comando podemos  cambiar a la cuenta que sí queremos utilizar.

### 2. Verificar el proyecto activo

```bash
gcloud config list project
```

Para ver la lista de proyectos ocupamos este comando   sirve si no sabemos exactamente el ID del proyecto en el cual queremos trabajar aunque también  podemos ver la lista de proyectos y cambiarlo manualmente mediante la consola en la parte superior y ver el ID de los proyectos. Además, el ID del proyecto con el que estemos trabajando se mostrará en todo momento en la terminal.

---

##  Diagrama de Arquitectura

![Diagrama de Arquitectura](https://github.com/user-attachments/assets/1b917102-a8e2-4cfd-b1f1-cdef8fe31d05)

---

##  Creación de la Red VPC

Comando para crear la red VPC en modo personalizado:

```bash
gcloud compute networks create labnet --subnet-mode=custom
```

![Creación de la Red VPC](https://github.com/user-attachments/assets/7296bcd6-9d17-4514-b0d4-82d8782a0f34)

###  Desglose del Comando

| Componente | Descripción |
| :--- | :--- |
| `compute` | Especificamos el grupo de recursos general que queremos utilizar. |
| `networks` | Especificamos el subgrupo específico con el que en este caso vamos a crear la VPC. |
| `create` | Especificamos qué vamos a hacer con ese subgrupo específico (`networks`). |
| `labnet` | Indicamos el nombre de la VPC. |
| `custom` | Indicamos que no cree ninguna subred automáticamente. El otro modo es `auto`, pero en ese modo se crearían subredes en todas las regiones. |

---

## Notas Adicionales (No incluidas por defecto en el laboratorio)

### En la CLI de Google nos indica una vez ejecutado el comando:

#### 1. Confirmación de creación y detalles de la red

- **`Created [https://www.googleapis.com/.../labnet]`**: Indica que la red llamada `labnet` se creó de manera correcta en tu proyecto temporal.
- **`NAME: labnet`**: El nombre asignado a tu red virtual.
- **`SUBNET_MODE: CUSTOM`**: Confirma que la red se creó en modo personalizado, lo que significa que podemos crear y definir manualmente las subredes y sus rangos de IP ( como en la siguiente tarea del laboratorio).
- **`BGP_ROUTING_MODE: REGIONAL`**: El modo de enrutamiento dinámico para el intercambio de rutas (BGP). Al no especificar este parámetro, se configuró por defecto.
- **`IPV4_RANGE` / `GATEWAY_IPV4` / `INTERNAL_IPV6_RANGE`**: Como se puede observar, al ser una red de modo personalizado, no se hacen subredes ni rangos de direcciones IP globales de manera automática.

#### 2. Mensaje informativo de seguridad y conectividad

> *"Instances on this network will not be reachable until firewall rules are created..."*

- **Aislamiento por defecto:** Google Cloud nos notifica que cualquier máquina virtual (VM) que conectemos a esta red no tendrá acceso ni podrá recibir tráfico hasta que se creen reglas de firewall explícitas que lo permitan.
- **Ejemplos de comandos sugeridos:** Al terminar, la terminal nos sugiere ejemplos de cómo podemos abrir puertos o permitir tráfico (como SSH, RDP o ICMP) usando comandos de `gcloud compute firewall-rules create`, para que recordemos  cómo funciona el control de acceso en redes nuevas.

---

##  Creación de una Subred

Ejecuta el siguiente comando para crear una subred dentro de la VPC `labnet`:

```bash
gcloud compute networks subnets create labnet-sub    --network labnet    --region "REGION"    --range 10.0.0.0/28
```

![Creación de Subred](https://github.com/user-attachments/assets/2acfa3de-6f03-4723-9c30-9dd38bec75b2)

###  Análisis del Rango IP (`--range 10.0.0.0/28`)

En el apartado de rango definimos el espacio de direccionamiento IP privado asignado a esta subred. Un prefijo `/28` significa que los primeros 28 bits están enmascarados para la red, dejando 4 bits disponibles para hosts ($32 - 28 = 4$). Esto otorga matemáticamente un total de 16 direcciones IP teóricas ($2^4 = 16$).

---

##  Notas Adicionales (No incluidas por defecto en el laboratorio)

- **`STACK_TYPE: IPV4_ONLY`**: Esto significa que al crear la subred sólo habilitaste direcciones IPv4 (con el rango `10.0.0.0/28`). Al no haber activado la opción de IPv6 (doble pila o dual-stack), Google Cloud deja los campos de prefijos IPv6 en blanco porque no reservó ningún bloque IPv6 para esa subred.

### Tratamiento y Reserva de IPs en GCP

En Google Cloud Platform, GCP reserva automáticamente **5 direcciones IP** en cualquier subred para propósitos específicos del sistema:

- **`.0`**: Dirección base de la red.
- **`.1`**: Puerta de enlace predeterminada (*Default Gateway*).
- **`.2`**: Servidor DNS interno de Google.
- **`.3`**: IP reservada para futuros usos de infraestructura de la plataforma.
- **`.15`** (en este bloque `/28`): Dirección de Broadcast (transmisión).

> **Resultado práctico:** De las 16 IPs teóricas, únicamente **11 direcciones IP quedan usables** para asignar a interfaces de máquinas virtuales, balanceadores o contenedores.

Finalmente, la bandera `--region=us-east1` determina la localización geográfica donde estará físicamente el plano de datos de esa subred (en este caso, Carolina del Sur, EE. UU.). Cabe recordar que en GCP, la VPC es un recurso global, mientras que la subred es estrictamente un recurso regional.

###  Prefijos IPv6

- **`INTERNAL_IPV6_PREFIX` (Prefijo IPv6 Interno):** Es el bloque de direcciones IPv6 privadas reservado para esa subred. Nos permite que las máquinas virtuales se comuniquen entre sí dentro de tu red privada en la nube usando el protocolo IPv6, sin exponerse a internet.
- **`EXTERNAL_IPV6_PREFIX` (Prefijo IPv6 Externo):** Es el bloque de direcciones IPv6 públicas reservadas para esa subred. Asigna direcciones IPv6 con salida para internet para que los servidores puedan navegar hacia afuera usando IPv6.

---

##  Comandos de Verificación e Inspección

Una vez ejecutada la configuración, auditamos el entorno para certificar que el estado real de la infraestructura coincide con el  diseño.

### A) Listar Redes Existentes

```bash
gcloud compute networks list
```

Interroga a la API del proyecto y despliega en pantalla una tabla consolidada con todas las redes VPC activas. En un entorno de laboratorio estándar, este comando retornará dos registros: la red  por defecto (`default`) y la nueva red  (`labnet`).

![Listar Redes Existentes](https://github.com/user-attachments/assets/80811b73-0fce-4348-8f19-aa7d2e87df94)

La primera red es la red predeterminada y la segunda es la que creamos.

### B) Listar Subredes con Filtro de Red `labnet`

```bash
gcloud compute networks subnets list --network=labnet
```
Si solo ponemos  `subnets list`, la CLI traería de vuelta cientos de subredes  correspondientes a todas las redes del proyecto, saturando la terminal. Al añadir el filtrado `--network=labnet`, se aplica una restricción estricta , mostrando exclusivamente la información de `labnet-sub`, confirmando su rango IP correspondiente (`us-east1`).

![Listar Subredes con Filtro](https://github.com/user-attachments/assets/5efdedc3-4ec6-47b7-bea8-b5c6985f2ff5)




