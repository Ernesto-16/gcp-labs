# Aprovisionamiento de Red VPC y Reglas de Firewall en GCP mediante Terraform
**Basado en el laboratorio:** *Cambia las reglas de firewall con Terraform y Cloud Shell (Google Cloud Skills Boost)*

> **Entorno:** Google Cloud Platform (GCP)  
> **Herramientas:** VPC Network, Cloud Firewall, Cloud Shell, Terraform, Linux/Bash, Git.

---

## Objetivos 

* **Configuración del Entorno Cloud Shell:** Solucionar e instalar las dependencias de Terraform mediante la personalización del entorno bash (`.customize_environment`).
* **Gestión de Infraestructura como Código (IaC):** Clonar e inspeccionar manifiestos HCL (`main.tf`) para la definición declarativa de recursos de red en GCP.
* **Aprovisionamiento Automatizado:** Inicializar (`terraform init`) e implementar (`terraform apply`) una VPC y un conjunto de reglas de firewall dinámicas en la nube.
* **Verificación de Recursos:** Validar desde la consola web de Google Cloud la correcta propagación de las políticas de firewall (filtrado TCP/ICMP) y la VPC creada.

---


## Notas Técnicas

> **Solución a la falta del binario de Terraform:**
> Durante la ejecución inicial, el entorno de Cloud Shell no contaba con Terraform instalado correctamente. Se agregó el repositorio oficial de HashiCorp y su clave GPG al script `~/.customize_environment` para garantizar su disponibilidad y ejecución mediante:
> ```bash
> nano $HOME/.customize_environment
> bash $HOME/.customize_environment
> ```
## Arquitectura
<img width="1218" height="768" alt="image" src="https://github.com/user-attachments/assets/0247fba2-4b76-4aac-92ea-5d2303e538be" />
