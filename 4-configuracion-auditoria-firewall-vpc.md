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
<img width="1183" height="853" alt="image" src="https://github.com/user-attachments/assets/9ba09298-9a32-442d-9d07-ad9872357f93" />

<img width="1280" height="822" alt="image" src="https://github.com/user-attachments/assets/efe14f34-2904-4aa7-9f5c-4dedc2c1352d" />


