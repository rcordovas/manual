# Instrucciones de Pentesting en nube

Para garantizar el correcto funcionamiento del laboratorio, se sugiere desplegar una instancia Debian con las siguientes especificaciones: tamaño t3.small o similar y, al menos, 20 GB de almacenamiento.

## Configuaciones previas

### Descargamos los scripts necesarios
Una vez en la maquina, ingresamos como usuario root y luego descargamos los scripts necesarios.
```
sudo su
wget https://raw.githubusercontent.com/rcordovas/scripts/refs/heads/main/script.sh
wget https://raw.githubusercontent.com/rcordovas/scripts/refs/heads/main/script2.sh
```

### Instalar los scripts 
```
bash ./script.sh
bash ./script2.sh
```

## Configuración de Perfiles (Tokens/Credenciales)

### Configuración de Perfiles (Tokens/Credenciales)
Antes de ejecutar las herramientas, asegúrate de tener configurados los entornos de acceso:


#### AWS (CLI Profile)
>Se almacena en ~/.aws/credentials.
>
```
aws configure --profile pentesting2
AWS Access Key ID [None]: AKIAWVAULXXXXXXXXXX
AWS Secret Access Key [None]: YUF5GBDQZdlSCbSo/HmgvU69XXXXXXXX/XXXX
Default region name [None]: us-east-1
Default output format [None]: html
```

#### Azure (Service Principal) - Opcional
>Se suele usar un Service Principal o el login interactivo.
>
```
sudo apt-get install azure-cli
az login
```
>> Nos va a salir el siguiente mensaje para navegar e ingresar el codigo (para mayor referencia ver el video adjunto):
>> To sign in, use a web browser to open the page https://login.microsoft.com/device and enter the code EXXXXXC9W to authenticate.

#### GCP (Service Account) - Opcional
>Se utiliza un archivo JSON de llave.
```
#!/bin/bash

sudo apt-get update
sudo apt-get install -y apt-transport-https lsb-release ca-certificates curl gnupg

sudo mkdir -p /etc/apt/keyrings
curl -sLS https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor | sudo tee /etc/apt/keyrings/microsoft.gpg > /dev/null
sudo chmod go+r /etc/apt/keyrings/microsoft.gpg

AZ_DIST=$(lsb_release -cs)
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/microsoft.gpg] https://packages.microsoft.com/repos/azure-cli/ $AZ_DIST main" | sudo tee /etc/apt/sources.list.d/azure-cli.list

curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg
echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list

sudo apt-get update
sudo apt-get install -y azure-cli google-cloud-cli

export GOOGLE_APPLICATION_CREDENTIALS="/ruta/a/tu/key.json"
gcloud auth activate-service-account --key-file=$GOOGLE_APPLICATION_CREDENTIALS
```
>> Puedes hacer clic en la cuenta de servicio correspondiente en la consola de Cloud y crear una nueva clave desde la pestaña CLAVES haciendo clic en Agregar clave. Una vez creada la clave, se descargará el archivo JSON.

## Uso de Herramientas

### Usar cloud_enum
>Herramienta OSINT multi-cloud para enumerar recursos públicos o semipúblicos en AWS, Azure y GCP, como buckets, blobs, Firebase, App Engine y Cloud Functions. Etapa: reconocimiento externo / discovery inicial del attack surface.
>

### CloudBrute
>Herramienta de descubrimiento black-box para encontrar infraestructura, archivos y aplicaciones de un objetivo en varios proveedores cloud; está pensada para hallar buckets, apps y bases de datos expuestas sin necesidad de autenticarse. Etapa: reconocimiento externo y mapeo de superficie expuesta.
>

### Prowler
>Plataforma/herramienta de assessment de seguridad y compliance con cientos de checks listos para usar sobre entornos cloud. No está enfocada en explotar, sino en detectar hallazgos de configuración y postura. Etapa: evaluación de postura de seguridad / misconfiguraciones y también validación posterior al hardening.
>

### ScoutSuite
>Herramienta de auditoría multi-cloud que usa las APIs del proveedor para recopilar configuración, resaltar áreas de riesgo y presentar una vista clara del attack surface. Etapa: enumeración interna autenticada y evaluación de postura/configuración.
>

### Pacu
>Framework ofensivo para AWS diseñado para explotar fallas de configuración dentro de una cuenta; incluye módulos para privilege escalation, backdooring de usuarios IAM y abuso de funciones Lambda, entre otros. Etapa: explotación, post-explotación y escalamiento de privilegios.
>

### Stormspotter
>Herramienta para Azure que construye un attack graph de recursos y objetos de Azure/Azure AD, permitiendo visualizar superficie de ataque y oportunidades de pivoting. Etapa: enumeración interna, análisis de rutas de ataque y planeamiento de movimiento lateral.
>

### PMapper
>Herramienta para AWS IAM que modela usuarios y roles como un grafo dirigido para identificar riesgos de configuración, especialmente privilege escalation y caminos alternos de acceso a recursos. Etapa: análisis de permisos/IAM, modelado de rutas de ataque y preparación de escalamiento.
>

### Cloudsplaining
>Herramienta de assessment de IAM en AWS centrada en detectar violaciones al principio de least privilege y priorizar riesgos como data exfiltration, infrastructure modification, resource exposure y privilege escalation. Etapa: revisión de permisos, priorización de hallazgos IAM y pre-explotación.
>

### MicroBurst
>Toolkit en PowerShell para Azure con scripts para discovery de servicios, auditoría de configuraciones débiles y acciones de post-explotación como credential dumping. Etapa: cubre varias, pero principalmente enumeración interna, auditoría de debilidades y post-explotación.
>

### gcp_scanner
>Scanner para GCP que ayuda a determinar qué nivel de acceso tienen ciertas credenciales y está pensado para evaluar el impacto de una comprometida de VM/container, una service account o una OAuth token leak. >
Etapa: enumeración autenticada, impact assessment y post-compromiso/post-explotación en GCP.
>
