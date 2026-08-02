# Implementación y Auditoría de Reglas de Firewall en VPC

**Basado en el laboratorio:** *Accede a un firewall y crea una regla (Google Cloud Skills Boost)*

> **Entorno:** Google Cloud Platform (GCP)  
> **Herramientas:** VPC Network, Cloud Firewall, Compute Engine, Cloud Logging (Explorador de registros).  

## Objetivos

*   **Configurar protección perimetral:** Crear e implementar reglas de firewall (permitir y denegar) en una red de Nube Privada Virtual (VPC) para controlar el tráfico entrante.
*   **Gestionar recursos mediante etiquetas:** Aplicar etiquetas de red (*network tags*, ej. `http-server`) para dirigir reglas de seguridad a instancias de máquinas virtuales específicas sin afectar a toda la red.
*   **Generar y validar tráfico de red:** Probar la conectividad de un servidor web Apache accediendo a su dirección IP externa desde un navegador.
*   **Analizar telemetría y registros de seguridad:** Utilizar el *Explorador de registros (Log Explorer)* de Google Cloud para inspeccionar los **Registros de flujo de VPC (VPC Flow Logs)** y los **Registros de Firewall**, verificando direcciones IP de origen/destino (como la IP interna `10.1.3.2`), puertos (80 y 22) y el estado de las conexiones (permitidas o denegadas).

---

## Diagrama de Arquitectura de Seguridad

<img width="1494" height="793" alt="image" src="https://github.com/user-attachments/assets/37ed7af5-873a-41ea-b1ef-decb1d7ede17" />

---

## Implementación de Firewall

Ocupamos una red privada ya incluida por defecto en el laboratorio (`vpc-net`).

<img width="1194" height="477" alt="image" src="https://github.com/user-attachments/assets/cbd05159-a696-4c40-aa3e-2834fe28c4a6" />

### Notas del laboratorio (Explicadas con analogías)

*   **Prioridad (Priority):**
    *   **Qué es:** El orden de mando del "guardia de seguridad".
    *   **Cómo funciona:** Se mide con números del 0 al 65,535. Cuanto más bajo sea el número, más autoridad tiene la regla (es como el rango militar: un general con rango 1 manda más que un soldado con rango 1000).
    *   **Ejemplo:** Si tienes una regla con prioridad 100 que dice "Bloquear todo" y otra regla con prioridad 1000 que dice "Permitir internet", el guardia obedecerá primero la de prioridad 100. El valor por defecto siempre es 1000.

*   **¿Qué pasa si tienen la misma prioridad?**
    *   Preferentemente no debe pasar, pero en esos casos se aplican dos reglas de desempate:
        1.  **Deny (Rechazar) le gana a Allow (Permitir):** Si una regla con prioridad 1000 dice Permitir y otra regla también con prioridad 1000 dice Bloquear (para el mismo puerto y origen), Google Cloud **siempre prioriza el bloqueo** por seguridad.
        2.  **Orden alfabético del nombre:** Si ambas reglas tienen la misma prioridad y hacen exactamente lo mismo, Google Cloud las evalúa basándose en el nombre de la regla en orden alfabético (de la A a la Z).

*   **Dirección del tráfico (Direction of traffic):**
    *   Es hacia dónde va a mirar el guardia para aplicar la seguridad.
    *   **Entrada (Ingress):** Tráfico que viene de fuera (o de otra red) que intenta entrar hacia tus servidores virtuales (VMs). Es la que usamos en este laboratorio.
    *   **Salida (Egress):** Tráfico que generan tus servidores virtuales para salir hacia internet o hacia otras redes.

*   **Acción en caso de coincidencia (Action on match):**
    *   **Qué es:** La orden que toma el guardia si se cumple con la regla.
    *   **Permitir (Allow):** Deja pasar el tráfico sin problemas.
    *   **Rechazar (Deny):** Bloquea el tráfico.

*   **Destinos y Etiquetas de destino (Targets):**
    *   **Qué es:** Es a qué servidor o servidores se les va a aplicar esta regla.
    *   **Todas las instancias:** La regla aplica para absolutamente todas las VMs.
    *   **Etiquetas de destino especificadas (Network Tags):** Le dices al guardia: *"Esta regla solo aplica para las máquinas que lleven puesto el gafete de servidor web"*. Aquí usamos la etiqueta `http-server`.

Una vez teniendo las configuraciones del firewall, procedemos a implementarlas:

<img width="1183" height="853" alt="image" src="https://github.com/user-attachments/assets/9ba09298-9a32-442d-9d07-ad9872357f93" />

---

### Conceptos Adicionales: Protocolos y Puertos

#### Protocolos
*   **TCP (El ordenado y seguro):**
    *   Establece conexión previa, verifica que todo llegue bien, ordena los paquetes y reintenta enviar los datos si algo falla.
    *   **Cuándo usarlo:** Cuando la precisión de los datos es más importante que la velocidad (ej. bases de datos, páginas web tradicionales, correos).
*   **UDP (El rápido y sin confirmación):**
    *   Envía los datos a toda velocidad sin verificar si la otra computadora los recibió y sin pedir reintentos.
    *   **Cuándo usarlo:** Cuando el tiempo real es más importante que perder un pequeño paquete.
    *   **Casos de uso reales:** Streaming de video y llamadas en vivo (Zoom, Meet, Twitch). *(Nota: Aunque algunas tecnologías web modernas como HTTP/3 empiezan a usar UDP, la web tradicional y las bases de datos dependen de TCP).*

#### Puertos y su Analogía
Los puertos son como los **códigos de radio de la policía**. Cuando dicen un número, ya sabes exactamente de qué se trata:
*   **Puerto 80:** Lo ocupamos para saber qué es el HTTP (tráfico web sin cifrar).
*   **Puerto 443:** Es el HTTPS, ideal para seguridad web porque cifra los datos.
*   **Puerto 22:** Es el protocolo SSH. Cifra la información y se usa para administrar servidores de forma remota y segura.

<img width="1280" height="822" alt="image" src="https://github.com/user-attachments/assets/efe14f34-2904-4aa7-9f5c-4dedc2c1352d" />

---

## Comprobación de la creación de la regla

<img width="1280" height="754" alt="image" src="https://github.com/user-attachments/assets/0c179e83-81df-47d7-b04d-6967194c580b" />




Ingresamos a la página por medio de la IP externa de la VM para generar tráfico y registros:




<img width="1280" height="745" alt="image" src="https://github.com/user-attachments/assets/3ef52d8b-fbbd-4b1c-a8cd-512148125b9f" />
<img width="851" height="540" alt="image" src="https://github.com/user-attachments/assets/fa568c27-491d-4b51-8826-a491a36e8a82" />

---

## Log Explorer (Explorador de registros)

Una vez dentro, en resultados seleccionamos subredes. En nombre del registro, seleccionamos `compute.googleapis.com/vpc_flows` e ingresamos el siguiente comando (sustituyendo con nuestra IP externa) en imagen no se ve la dirección ip utilizada pero esta se puede octener atraves de una pagina como: https://www.whatismyip.com/ 

```text
jsonPayload.connection.src_ip="TU_IP_EXTERNA"
```

<img width="1280" height="857" alt="image" src="https://github.com/user-attachments/assets/0a68c165-4670-4fc7-ac93-8b2767c04116" />

Podemos obtener el registro filtrando por los registros del Firewall, la sub red y nuestra dirección IP :

<img width="1158" height="452" alt="image" src="https://github.com/user-attachments/assets/7b68b353-3b34-470b-8bc7-e8fbae1e72d0" />

Después de analizar los detalles de esta entrada de registro, notamos que se permitió el tráfico de red (en el puerto 80 de HTTP) ya que  la regla de firewall `allow-http-ssh` que creamos anteriormente . Sabemos que la conexión fue exitosa porque hay intercambio de bytes y no hay ningún mensaje de "denegado".

Al expandir el registro en formato JSON podemos ver lo siguiente:

<img width="578" height="196" alt="image" src="https://github.com/user-attachments/assets/ad648b83-1e70-460b-b754-72e2313c9c2e" />

*   **`dest_ip`:** Es la dirección IP de destino del servidor web.
*   **`dest_port`:** Es el número del puerto de destino del servidor web (Puerto 80).
*   **`protocol`:** El protocolo es 6, que es el número estándar de la IANA para el tráfico TCP.
*   **`src_ip`:** Es la dirección IP de origen de la computadora cliente.
*   **`src_port`:** Es el número de puerto de origen asignado a la computadora. Suele ser un puerto aleatorio entre 49152 y 65535.

---

## Nueva regla para denegar el ingreso a la página

<img width="1199" height="513" alt="image" src="https://github.com/user-attachments/assets/ff477747-e429-46d1-b8cb-12cb91453c98" />

Con la configuración anterior implementada:

<img width="1042" height="843" alt="image" src="https://github.com/user-attachments/assets/e5f22abb-cd53-4258-883b-f19e650148a3" />

Ingresamos nuevamente mediante la IP externa de la VM, confirmando que el acceso ha sido denegado por el navegador:

<img width="607" height="174" alt="image" src="https://github.com/user-attachments/assets/3bc053d9-e758-49cd-9040-0cc02def06f6" />

### Confirmación final en Log Explorer

Revisando los registros de tipo *Firewall* :

<img width="745" height="180" alt="image" src="https://github.com/user-attachments/assets/605271d9-5202-498a-a2d8-e1ca39752652" />

Ahora aparece en el estado de "denegado",  y así podemos confirmar que la nueva  regla de bloqueo funciona de manera correcta.


