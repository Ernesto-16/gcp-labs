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


## Despliegue de vpc y firewall
> ```bash
> cloudshell_open --repo_url "https://github.com/terraform-google-modules/docs-examples.git" --print_file "./motd" --dir "firewall_basic" --page >
> "editor" --tutorial "./tutorial.md" --open_in_editor "main.tf" --force_new_clone
> ```

cloudshell_open: En el entorno d etrabbajo actual ejecua lo siguinete

--repo_url "https://...": Le indica a Cloud Shell qué repositorio de Git debe clonar automáticamente. lista de ejemplos oficiales de Terraform para Google Cloud (docs-examples.git) ete comando internamente ejecuta git clone

--print_file "./motd":Imprime lo que venga en este archivo del repositorio

dicho mensaj es :
These examples use real resources that will be billed to the
Google Cloud Platform project you use - so make sure that you
run "terraform destroy" before quitting!

--dir "firewall_basic"	Navega a la subcarpeta: Una vez clonado el repositorio, cambia el directorio de trabajo (working directory) a la carpeta firewall_basic. Todo comando que ejecutes en la terminal empezará en ese directorio
.
--page "editor":Cambia la interfaz visual: En lugar de abrir solo la pantalla negra de la terminal, fuerza a Cloud Shell a iniciar con la interfaz gráfica del Cloud Shell Editor (el entorno de desarrollo basado en VS Code).


--tutorial "./tutorial.md":Activa el panel lateral derecho de tutoriales

--open_in_editor "main.tf"	 Hace que el editor que antes ya se le diimos que se ejecute  abra automáticamente una pestaña con el archivo main.tf (el código principal de Terraform que define las reglas de Firewall y la VPC

--force_new_clone	 :este parámetro elimina la carpeta antigua y descarga una copia completamente limpia y nueva desde GitHub para evitar conflictos.

imendiantemnete se abre el editor con las configuraciones
<img width="1016" height="597" alt="image" src="https://github.com/user-attachments/assets/a0ab5254-ef43-494a-ad1c-0a5b11b1211f" />
<img width="1137" height="264" alt="image" src="https://github.com/user-attachments/assets/2b1f75a6-492a-4f0a-9a17-2ac2a17b5e2d" />
## Analisiis de recursoso de terrafrom

una vez ejecutado el comando anaterioro ahora 

export GOOGLE_CLOUD_PROJECT=qwiklabs-gcp-01-236d93d38030
guardamos el id del proyecto ene una  varibale

y en mi caso al ejjecutar terrafrom init veo que en la aterminal que no no esta instalada 
<img width="1497" height="228" alt="image" src="https://github.com/user-attachments/assets/3273e1e6-a18f-4fa4-8a52-c45a67968233" />
 ejecuto esto comando 
 nano $HOME/.customize_environment
 sirve para:

 posteriomenete dentro del editor escribo
 wget -O - https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
 significa:
 despues 
 echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
significa
despues 
sudo apt update && sudo apt install terraform
y por ultimo para guardar los canbio clk + o entren clk + x

porteriormnete en la terminal 
bash $HOME/.customize_environment

parra poder 

despues bash

para 

despues ahor asi terrafrom init 
<img width="1280" height="435" alt="image" src="https://github.com/user-attachments/assets/e1a31c5d-e7b4-402c-b7a8-d0472493af05" />

despues terrafrom aply

<img width="686" height="309" alt="image" src="https://github.com/user-attachments/assets/cbd2d900-cd14-4462-bd4a-0c4527e8bde2" />
confirmamos con 
terraform plan
<img width="1132" height="276" alt="image" src="https://github.com/user-attachments/assets/5f30f21d-9ccd-423c-8118-eff4e5a285c2" />

verificamos en redes vpc al ver la nueva vpc veremos loq ue configuramos 

en este caso no los vamso a ocupara mas asiq ue los vamsoa  sdestruir
terraform destroy
<img width="1133" height="82" alt="image" src="https://github.com/user-attachments/assets/996ea238-a727-4a5c-b746-5c2117c58f92" />


<img width="1100" height="731" alt="image" src="https://github.com/user-attachments/assets/f4eed9ce-b4e6-4e91-b642-df9cf033b80b" />





