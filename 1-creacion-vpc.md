<h1>Creación de  una VPC con Cloud Shell</h1>
<h2>Objetivos</h2>
Crear una red VPC en modo personalizado (labnet) mediante la interfaz de comandos para tener control manual sobre los rangos y la segmentación.

Configurar una subred específica dentro de la red VPC personalizada utilizando rangos de direcciones IP definidos.

Visualizar y auditar las redes y subredes existentes en el proyecto para verificar que la configuración se haya aplicado correctamente y garantizar que el entorno de pruebas esté listo para investigaciones de seguridad.

<h2>Revisión de entorno</h2>

gcloud auth list

Con el comando anterior veremos que que cuenta esta activa en este momento ya que aveces peude haber má de una cuenta activa como por ejemplo una cuneta de pruebas y otra corpoartiva  en el caso de que no esto en la cuenta que querramos usar podeos usar  el siguinete comando

gcloud config set account [ACCOUNT]

Con el podemos canbiara a la ala cuneta que si queremos utilizar 

gcloud config list project

Por ultimo para ver la lista de proyectos se ocuapa este comando si no sabemos exactamente el id del proyecto del cual queremos trabajar canbiarlo manualmnete y ver el id de los proeyectos además el id de proyecto con ele que estemos trabajando se mostrarra en todo momento en  la terminal 

<h2>Diagrama de Arquitectura</h2>
<img width="1192" height="686" alt="image" src="https://github.com/user-attachments/assets/1b917102-a8e2-4cfd-b1f1-cdef8fe31d05" />

<h2>Creación de la Red VPC</h2>
gcloud compute networks create labnet --subnet-mode=custom

<img width="1420" height="407" alt="image" src="https://github.com/user-attachments/assets/7296bcd6-9d17-4514-b0d4-82d8782a0f34" />

Compute :Especificamos el grupo d erecuros general qu equeremos utilizar 
networks:Especificamos el sub grupo especifico  con el que en este caso vamsoa  acreara la VPC
Create: Especificamos que vamos hacer con ese subgrupo especifico (networks)
labnet: indicamos el nombre de la VPC
custom: Indicamos que crre  que no creee ninguna subred  el otro modood es auto pero en este otro  se crearian subreres en todas las regiones 

<h2>Notas adicionales no incluidas  por defceto en el laboratorio</h2>
<h3>En la CLI de google nos indica una vez ejecutado el comando:</h3>

1. Confirmación de creación y detalles de la red
Created [[https://www.googleapis.com/.../labnet](https://www.googleapis.com/.../labnet)]: Indica que la red llamada labnet se creó de manera correcta en tu proyecto temporal.

NAME: labnet: El nombre asignado a tu red virtual.

SUBNET_MODE: CUSTOM: Confirma que la red se creó en modo personalizado, lo que significa que tú debes crear y definir manualmente las subredes y sus rangos de IP (tal como lo harás en la siguiente tarea del laboratorio).

BGP_ROUTING_MODE: REGIONAL: El modo de enrutamiento dinámico para el intercambio de rutas (BGP) al no especificar este parámetro se configuro por defecto 

IPV4_RANGE / GATEWAY_IPV4 / INTERNAL_IPV6_RANGE:Como se puede observa al ser una red de modo personalizado, no se autogeneran subredes ni rangos de direcciones IP globales de manera automática.

2. Mensaje informativo de seguridad y conectividad
"Instances on this network will not be reachable until firewall rules are created..."

Aislamiento por defecto: Google Cloud nos notifica  que cualquier máquina virtual (VM) que conectemos  a esta red no tendrá acceso ni podrá recibir tráfico hasta que se creen reglas de firewall explícitas que lo permitan.

Ejemplos de comandos sugeridos: El termirminar la terminal nos sugiere  ejemplos de cómo podemos  abrir puertos o permitir tráfico (como SSH, RDP o ICMP) usando comandos de gcloud compute firewall-rules create, recordándote cómo funciona el control de acceso en redes nuevas.

<h2>Creación de una Subred</h2>
gcloud compute networks subnets create labnet-sub \
   --network labnet \
   --region "REGION" \
   --range 10.0.0.0/28

<img width="1260" height="281" alt="image" src="https://github.com/user-attachments/assets/2acfa3de-6f03-4723-9c30-9dd38bec75b2" />

--range=10.10.0.0/28 

En el apartado de rango definimos  el espacio de direccionamiento IP privado asignado a esta subred. Un prefijo /28 significa que los primeros 28 bits están enmascarados para la red, dejando 4 bits disponibles para hosts ( 32 - 28 = 4 ). Esto otorga matemáticamente un total de 16 direcciones IP teóricas ( = 16 ).



<h2>Notas adicionales no incluidas  por defceto en el laboratorio</h2>
STACK_TYPE: IPV4_ONLY
Esto significa que al crear la subred sólo habilitaste direcciones IPv4 (con el rango 10.0.0.0/28). Al no haber activado la opción de IPv6 (doble pila o dual-stack), Google Cloud deja los campos de prefijos IPv6 en blanco porque no reservó ningún bloque IPv6 para esa subred.


TRATAMIENTO Y RESERVA DE IPS 

EN GCP Google Cloud Cloud reserva automáticamente 5 direcciones IP en cualquier subred para propósitos específicos del sistema: • • • • • .0 : Dirección base de la red. .1 : Puerta de enlace predeterminada (Default Gateway). .2 : Servidor DNS interno de Google. .3 : IP reservada para futuros usos de infraestructura de la plataforma. .15 (en este bloque /28): Dirección de Broadcast (transmisión). Resultado práctico: De las 16 IPs teóricas, únicamente 11 direcciones IP quedan usables para asignar a interfaces de máquinas virtuales, balanceadores o contenedores. Finalmente, la bandera --region=us-east1 determina la localización geográfica donde residirá físicamente el plano de datos de esa subred (en este caso, Carolina del Sur, EE. UU.). Cabe recordar que en GCP, la VPC es un recurso global, mientras que la subred es estrictamente un recurso regional. 4.

NTERNAL_IPV6_PREFIX (Prefijo IPv6 Interno)

Es el bloque de direcciones IPv6 privadas reservado para esa subred nos permite que las  máquinas virtuales se comuniquen entre sí dentro de tu red privada en la nube usando el protocolo IPv6, sin exponerse a internet.

EXTERNAL_IPV6_PREFIX (Prefijo IPv6 Externo)

Es el bloque de direcciones IPv6 públicas reservadas para esa subred asigna direcciones IPv6 con salida para internet para que los  servidores puedan navegar hacia afuera usando IPv6.

<h2>Comandos de verificacion de inspección</h2>
Una vez ejecutada la configuración, el analista debe auditar el entorno para certificar que el estado real de la infraestructura coincide plenamente con los planos de diseño.
gcloud compute networks list
A) Listar Redes Existentes gcloud compute networks list Propósito: Interroga a la API del proyecto y despliega en pantalla una tabla consolidada con todas las redes VPC activas. En un entorno de laboratorio estándar, este comando retornará dos registros: la red nativa por defecto ( default ) y la nueva red estructurada

<img width="1100" height="269" alt="image" src="https://github.com/user-attachments/assets/80811b73-0fce-4348-8f19-aa7d2e87df94" />
 La primera red es la red predertaminad y la seguda e sla que creamos 

gcloud compute networks subnets list --network=labnet
( B) Listar Subredes con Filtro de Red labnet ). gcloud compute networks subnets list --network=labnet Propósito y Mecanismo: Si se ejecutase únicamente subnets list , la CLI traería de vuelta cientos de subredes globales correspondientes a todas las redes del proyecto, saturando la terminal. Al añadir la bandera de filtrado --network=labnet , se aplica una restricción estricta en el lado del servidor, mostrando exclusivamente la información de labnet-sub , confirmando su rango IP ( correspondiente ( us-east1 ).
<img width="1241" height="194" alt="image" src="https://github.com/user-attachments/assets/5efdedc3-4ec6-47b7-bea8-b5c6985f2ff5" />







