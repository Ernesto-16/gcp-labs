# Implementación y Auditoría de Reglas de Firewall en VPC

**Basado en el laboratorio:** *Accede a un firewall y crea una regla (Google Cloud Skills Boost)*

> **Entorno:** Google Cloud Platform (GCP)  
> **Herramientas:** VPC Network, Cloud Firewall, Compute Engine, Cloud Logging (Explorador de registros).  

## Objetivos

- **Configurar protección perimetral:** Crear e implementar reglas de firewall (permitir y denegar) en una red de Nube Privada Virtual (VPC) para controlar el tráfico entrante.
- **Gestionar recursos mediante etiquetas:** Aplicar etiquetas de red (*network tags*, ej. `http-server`) para dirigir reglas de seguridad a instancias de máquinas virtuales específicas sin afectar a toda la red.
- **Generar y validar tráfico de red:** Probar la conectividad de un servidor web Apache accediendo a su dirección IP externa desde un navegador.
- **Analizar telemetría y registros de seguridad:** Utilizar el *Explorador de registros (Log Explorer)* de Google Cloud para inspeccionar los **Registros de flujo de VPC (VPC Flow Logs)** y los **Registros de Firewall**, verificando direcciones IP de origen/destino (como la IP interna `10.1.3.2`), puertos (80 y 22) y el estado de las conexiones (permitidas o denegadas).
---
##  Diagrama de Arquitectura de Seguridad
<img width="1494" height="793" alt="image" src="https://github.com/user-attachments/assets/37ed7af5-873a-41ea-b1ef-decb1d7ede17" />

## Implemnetación de Firewall
Ocupamos una red privada ya incluida por defecto en el laboratorio (vpc-net), asignamos una 
<img width="1194" height="477" alt="image" src="https://github.com/user-attachments/assets/cbd05159-a696-4c40-aa3e-2834fe28c4a6" />
## NOtas del laboratorio pero con analogias
. Prioridad (Priority)
Qué es: El orden de mando del guardia.

Cómo funciona: Se mide con números del 0 al 65,535.

Cuanto más bajo sea el número, más autoridad tiene la regla (es como el rango militar: un general con rango 1 manda más que un soldado con rango 1000).

Ejemplo: Si tienes una regla con prioridad 100 que dice "Bloquear todo" y otra regla con prioridad 1000 que dice "Permitir internet", el guardia obedecerá primero la de prioridad 100 porque los números más bajos van primero. El valor por defecto siempre es 1000 y en este caso esta regla no le gana ninguna otra

## Que pasa si tiene la misma prioridad?
En primer lugar preferentemnete no deve pasar pero en esos casos:

Deny (Rechazar) le gana a Allow (Permitir):
Si una regla con prioridad 1000 dice Permitir tráfico y otra regla también con prioridad 1000 dice Bloquear tráfico (para el mismo puerto y origen), Google Cloud siempre prioriza el bloqueo por seguridad. La conexión será rechazada automáticamente.

Orden alfabético del nombre de la regla:
Si ambas reglas tienen la misma prioridad y hacen exactamente lo mismo (las dos son de tipo Allow o las dos son de tipo Deny), Google Cloud las ordena y evalúa basándose en el nombre de la regla en orden alfabético (de la A a la Z). La regla cuyo nombre vaya primero alfabéticamente tomará la decisión.
Dirección del tráfico (Direction of traffic)
Es en donde de va a fijara el guardia parra aplicar la seguridad

Opciones:

Entrada (Ingress): Tráfico que viene de fuera (o de otra red) e intenta entrar hacia tus servidores virtuales (VMs). Es la que usaste en tu laboratorio para bloquear o permitir la web.

Salida (Egress): Tráfico que generan tus servidores virtuales para salir hacia internet o hacia otras redes



Acción en caso de coincidencia (Action on match)
Qué es: La orden directa que toma el guardia si alguien cumple con la regla.

Opciones:

Permitir (Allow): Deja pasar el tráfico sin problemas.

Rechazar (Deny): Bloquea el tráfico de inmediato (como la regla deny-http que hiciste).

Destinos y Etiquetas de destino (Targets)
Qué es: A qué servidor o servidores dentro de tu red se le va a aplicar esta ley. No siempre quieres proteger a todas las máquinas por igual.

Opciones:

Todas las instancias: La regla aplica para absolutamente todas las VMs de la red.

Etiquetas de destino especificadas (Network Tags): Aquí es donde entra tu etiqueta http-server. Le dices al guardia: "Esta regla solo aplica para las máquinas que lleven puesto el gafete (etiqueta) de servidor web". Si una VM no tiene esa etiqueta, el guardia la ignora.

Una vez teniendo las configuraciónes de el firewall como se mostro en la tabla anterior procedemos a implemnetarlas 
<img width="1183" height="853" alt="image" src="https://github.com/user-attachments/assets/9ba09298-9a32-442d-9d07-ad9872357f93" />
### Notas adicionales sin incluir en el laboratorio
TCP (El ordenado y seguro):

Cómo es: Establece conexión previa, verifica que todo llegue bien, ordena los paquetes y reintenta enviar los datos si algo falla (como te expliqué con el ejemplo del correo postal).

Cuándo usarlo: Cuando la precisión y la integridad de los datos son más importantes que la velocidad. Si se pierde un solo byte, la aplicación falla o se corrompe.
UDP (El rápido y sin confirmación):

Cómo es: Envía los datos a toda velocidad sin verificar si la otra computadora los recibió, sin importarle el orden y sin pedir reintentos. Si un paquete se pierde en el camino, se ignora y se sigue adelante.

Se ocupa mucho en Bases de datos y pagínas web

Cuándo usarlo: Cuando la velocidad y el tiempo real son más importantes que si se pierde un paquetito pequeño.

Casos de uso reales:

Streaming de video y llamadas en vivo (Zoom, Meet, Twitch): Si en una videollamada se pierde un fotograma por millésima de segundo, es mejor ignorarlo y seguir con el video en vivo que pausar toda la transmisión para pedir que reenvíen ese pedacito

### Puertos
80:Ocupumos este puerto para aprender que es el HTTP pero en realidad en la seguridad  es mejor ocupara el 443 el HTTPS ya que cifra los datos
22:En cuanto este puerto si es seguro este sis cifra la información y es para conexiones remotas seguras 
## Anlogia de puertos 
Son como los codigos de los policias en este caso cuando dicen puerto 22 ya sabes es el ssh y e sparra el trasporte seguro d einfomacion remota y es asi de esta manera sociativa para tener un estandar en numeros 
<img width="1280" height="822" alt="image" src="https://github.com/user-attachments/assets/efe14f34-2904-4aa7-9f5c-4dedc2c1352d" />

## Comporbaciín d ela creaccion d ela regla
<img width="1280" height="754" alt="image" src="https://github.com/user-attachments/assets/0c179e83-81df-47d7-b04d-6967194c580b" />
ingresamos a ala pagina por medio d ela ip externa d ela vm
<img width="1280" height="745" alt="image" src="https://github.com/user-attachments/assets/3ef52d8b-fbbd-4b1c-a8cd-512148125b9f" />
para generar esoso registros
<img width="851" height="540" alt="image" src="https://github.com/user-attachments/assets/fa568c27-491d-4b51-8826-a491a36e8a82" />

## Log explorrer
Una vez dentro en resultamos seleccionamos subredes , en nombre del registro, seleccionamos compute.googleapis.com/vpc_flows y ingresamos el comando 
jsonPayload.connection.src_ip=YOUR_IP  quedando como en la imagen epro con la ip de nosostros externa

<img width="1280" height="857" alt="image" src="https://github.com/user-attachments/assets/0a68c165-4670-4fc7-ac93-8b2767c04116" />
Podemos obtener el registro 
<img width="1158" height="452" alt="image" src="https://github.com/user-attachments/assets/7b68b353-3b34-470b-8bc7-e8fbae1e72d0" />
Después de analizar los detalles de esta entrada de registro, deberías notar que se permitió el tráfico de red que generaste (en el puerto 80 de HTTP) debido a la regla de firewall allow-http-ssh que creaste anteriormente. Esta regla permitió el tráfico entrante en los puertos 80 y 22 esto los sabemos porque que no haya ningun apartado que diga denegado
Al entrar ala registro
<img width="578" height="196" alt="image" src="https://github.com/user-attachments/assets/ad648b83-1e70-460b-b754-72e2313c9c2e" />

dest_ip: es la dirección IP de destino del servidor web.
dest_port: es el número del puerto de destino del servidor web, que es el puerto 80 de HTTP.
protocol: el protocolo es 6, que es el protocolo de IANA para el tráfico de TCP.
src_ip: es la dirección IP de origen de tu computadora.
src_port: es el número de puerto de origen que se asignó a tu computadora. De acuerdo con los estándares de la Internet Assigned Numbers Authority (IANA), suele ser un número de puerto aleatorio entre 49152 y 65535.

## Regla nueva para negar el ingreso a ala pagina 
<img width="1199" height="513" alt="image" src="https://github.com/user-attachments/assets/ff477747-e429-46d1-b8cb-12cb91453c98" />
Con la información anterior
<img width="1042" height="843" alt="image" src="https://github.com/user-attachments/assets/e5f22abb-cd53-4258-883b-f19e650148a3" />
Ingresamso nuevamente por la ip externa  de la vm viendo la negacion d eingreso
<img width="607" height="174" alt="image" src="https://github.com/user-attachments/assets/3bc053d9-e758-49cd-9040-0cc02def06f6" />
#Confirmación final en log explorrer
<img width="745" height="180" alt="image" src="https://github.com/user-attachments/assets/605271d9-5202-498a-a2d8-e1ca39752652" />
ahora aprecce el denegado confirmado la nueva regla








