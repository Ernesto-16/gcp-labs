# ☁️ Aprovisionamiento de Red VPC y Reglas de Firewall en GCP mediante Terraform

**Basado en el laboratorio:** *Cambia las reglas de firewall con Terraform y Cloud Shell (Google Cloud Skills Boost)*

> **Entorno:** Google Cloud Platform (GCP)  
> **Herramientas:** VPC Network, Cloud Firewall, Cloud Shell, Terraform, Linux/Bash, Git.

---

## 🎯 Objetivos 

* **Configuración del Entorno Cloud Shell:** Solucionar e instalar las dependencias de Terraform mediante la personalización del entorno bash (`.customize_environment`).
* **Gestión de Infraestructura como Código (IaC):** Clonar e inspeccionar manifiestos HCL (`main.tf`) para la definición declarativa de recursos de red en GCP.
* **Aprovisionamiento Automatizado:** Inicializar (`terraform init`) e implementar (`terraform apply`) una VPC y un conjunto de reglas de firewall dinámicas en la nube.
* **Verificación de Recursos:** Validar desde la consola web de Google Cloud la correcta propagación de las políticas de firewall (filtrado TCP/ICMP) y la VPC creada.

---

## 📐 Arquitectura de la Solución

<img width="1218" height="768" alt="image" src="https://github.com/user-attachments/assets/0247fba2-4b76-4aac-92ea-5d2303e538be" />

---

## 🚀 Despliegue del Entorno de Trabajo

Para agilizar la configuración, inicializamos el entorno clonando un repositorio oficial de ejemplos de Terraform directamente en Cloud Shell:

```bash
cloudshell_open --repo_url "https://github.com/terraform-google-modules/docs-examples.git" --print_file "./motd" --dir "firewall_basic" --page "editor" --tutorial "./tutorial.md" --open_in_editor "main.tf" --force_new_clone
```

Análisis detallado del comando `cloudshell_open`:

* **`--repo_url "https://..."`**: Le indica a Cloud Shell qué repositorio de Git debe clonar automáticamente. Es la lista de ejemplos oficiales de Terraform para Google Cloud (docs-examples.git). Este comando ejecuta internamente un `git clone`.
* **`--print_file "./motd"`**: Imprime un mensaje inicial ("Message of the Day") en la consola. En este caso advierte: "These examples use real resources that will be billed to the Google Cloud Platform project you use - so make sure that you run 'terraform destroy' before quitting!".
* **`--dir "firewall_basic"`**: Cambia el directorio de trabajo (working directory) a la subcarpeta firewall_basic. Todo comando posterior en la terminal iniciará en esa ubicación.
* **`--page "editor"`**: Cambia la interfaz visual para iniciar directamente con el entorno gráfico de Cloud Shell Editor (basado en VS Code) en lugar de solo la terminal.
* **`--tutorial "./tutorial.md"`**: Activa el panel lateral derecho con la guía del tutorial paso a paso.
* **`--open_in_editor "main.tf"`**: Abre automáticamente una pestaña en el editor con el archivo main.tf (el código HCL principal de Terraform que define la VPC y el Firewall).
* **`--force_new_clone"`**: Elimina cualquier carpeta o clon antiguo para descargar una copia totalmente limpia desde GitHub y evitar conflictos.

Inmediatamente se abre el editor con las configuraciones en lenguaje HCL listas para inspeccionar:

### ⚙️ Resolución de Dependencias (Instalación de Terraform)
Antes de ejecutar Terraform, guardamos el ID de nuestro proyecto de GCP en una variable de entorno para vincular las ejecuciones:

```bash
export GOOGLE_CLOUD_PROJECT=qwiklabs-gcp-01-236d93d38030
```

Al intentar ejecutar `terraform init`, la terminal nos notifica que el comando no existe porque el binario de Terraform no está instalado en la máquina virtual temporal de Cloud Shell:

#### Solución: Configuración de `.customize_environment`
Para solucionar esto permanentemente dentro de la sesión, utilizamos el editor de texto por terminal `nano` para editar el script de arranque del entorno:

```bash
nano $HOME/.customize_environment
```

**¿Qué hace nano y este archivo?**
`nano` es un editor de texto dentro de la terminal de Linux. El archivo `$HOME/.customize_environment` es un script oculto que Cloud Shell ejecuta automáticamente cada vez que la máquina virtual se enciende para instalar o personalizar dependencias del usuario.

Dentro del editor nano, escribimos las siguientes líneas de comandos:

```bash
# 1. Descarga la clave criptográfica oficial (GPG) de HashiCorp y la guarda en el llavero del sistema para verificar la autenticidad de los paquetes.
wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

# 2. Agrega la URL del repositorio oficial de HashiCorp a las fuentes de software de APT en la distribución Linux.
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

# 3. Actualiza el índice del gestor de paquetes de Debian/Ubuntu e instala el binario de Terraform.
sudo apt update && sudo apt install terraform
```

**Pasos para guardar y salir en Nano:**
1. Presiona `Ctrl + O` (guardar archivo).
2. Presiona `Enter` para confirmar el nombre.
3. Presiona `Ctrl + X` (salir del editor nano).

Posteriormente, ejecutamos manualmente el script para aplicar la instalación sin necesidad de reiniciar la sesión, y abrimos un nuevo intérprete de comandos bash para refrescar el PATH:

```bash
bash $HOME/.customize_environment
bash
```

### 🏗️ Análisis y Despliegue de Infraestructura
Con Terraform instalado correctamente, ejecutamos el flujo estándar de Infraestructura como Código (IaC):

1. **Inicialización (`terraform init`)**
   Inicializa el directorio de trabajo, analiza los archivos `.tf` y descarga el proveedor oficial de Google Cloud (`hashicorp/google`).
2. **Planificación (`terraform plan`)**
   Realiza una lectura del estado actual en GCP y compara con el código, mostrando los cambios exactos que se aplicarán en la nube.
3. **Aplicación (`terraform apply`)**
   Crea los recursos en Google Cloud Platform. Escribimos `yes` para autorizar la ejecución.

Al finalizar, navegamos en la consola web de GCP a **Redes VPC** y **Reglas de Firewall** para verificar que la red y las reglas de filtrado de tráfico fueron creadas correctamente.

### 🗑️ Limpieza del Entorno
Para evitar el consumo indeseado de créditos o recursos en la cuenta de GCP, eliminamos toda la infraestructura aprovisionada ejecutando:

```bash
terraform destroy
```
Confirmamos con `yes` y Terraform eliminará en orden inverso todas las reglas de Firewall y la red VPC de manera automática.

---

