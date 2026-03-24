# Instrucciones de Pentesting en nube

Para garantizar el correcto funcionamiento del laboratorio, se sugiere desplegar una instancia Debian con las siguientes especificaciones: tamaño t3.medium o superior y, al menos, 20 GB de almacenamiento.

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
Default output format [None]: json
```

#### Azure (Service Principal) - Opcional: Solo si va usar Microsoft Azure
>Se suele usar un Service Principal o el login interactivo.
>
```
sudo apt-get install azure-cli
az login
```
>> Nos va a salir el siguiente mensaje para navegar e ingresar el codigo (para mayor referencia ver el video adjunto):
>> To sign in, use a web browser to open the page https://login.microsoft.com/device and enter the code EXXXXXC9W to authenticate.

#### GCP (Service Account) - Opcional: Solo si va usar GCP
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
#### Busca buckets de S3, Azure Containers y GCP Buckets relacionados con una palabra clave
```
#Comandos de ejemplos:
cloud_enum -k "data-clientes-peru" -qs
cloud_enum -k "data-clientes-peru" -t 10
cloud_enum -k "storagemyazure" 
```

### CloudBrute
>Herramienta de descubrimiento black-box para encontrar infraestructura, archivos y aplicaciones de un objetivo en varios proveedores cloud; está pensada para hallar buckets, apps y bases de datos expuestas sin necesidad de autenticarse. Etapa: reconocimiento externo y mapeo de superficie expuesta.
>
```
#Comandos de ejemplos:
CloudBrute -d ia-peru.online -w /home/admin/cloud-security-tools/src/cloud_enum/enum_tools/fuzz.txt -k peru -t 50
```

### Prowler
>Plataforma/herramienta de assessment de seguridad y compliance con cientos de checks listos para usar sobre entornos cloud. No está enfocada en explotar, sino en detectar hallazgos de configuración y postura. Etapa: evaluación de postura de seguridad / misconfiguraciones y también validación posterior al hardening.
>
```
#Comandos de ejemplos:
prowler aws --profile pentesting2 -o ./prowler-results
```

En caso tengan error con el perfil de aws creado, probar con estos comandos.
```
unset AWS_PROFILE AWS_DEFAULT_PROFILE
export AWS_ACCESS_KEY_ID='AKIAWVAULXXXXXXXXXX'
export AWS_SECRET_ACCESS_KEY='YUF5GBDQZdlSCbSo/HmgvU69XXXXXXXX/XXXX'
unset AWS_SESSION_TOKEN

prowler aws -o ./prowler-results
```
 
### ScoutSuite
>Herramienta de auditoría multi-cloud que usa las APIs del proveedor para recopilar configuración, resaltar áreas de riesgo y presentar una vista clara del attack surface. Etapa: enumeración interna autenticada y evaluación de postura/configuración.
>
```
scout aws --profile pentesting2
cd /home/admin/scoutsuite-report
python3 -m http.server 80
```
>>Luego accede via web a http://ip_publica
>>

### Pacu
>Framework ofensivo para AWS diseñado para explotar fallas de configuración dentro de una cuenta; incluye módulos para privilege escalation, backdooring de usuarios IAM y abuso de funciones Lambda, entre otros. Etapa: explotación, post-explotación y escalamiento de privilegios.
>
```
pacu
What would you like to name this new session? audit
Key alias [None]: tester
Access key ID [None]: AKIAWVAULXXXXXXXXXX
Secret access key [None]: YUF5GBDQZdlSCbSo/HmgvU69XXXXXXXX/XXXX
Session token (Optional - for temp AWS keys only) [None]:
Keys saved to database.

```

```
Pacu (audit:tester) > list
```
>>Probamos al menos una prueba por cada módulo, por ejemplo con el comando `run ec2__enum` 

### Stormspotter
>Herramienta para Azure que construye un attack graph de recursos y objetos de Azure/Azure AD, permitiendo visualizar superficie de ataque y oportunidades de pivoting. Etapa: enumeración interna, análisis de rutas de ataque y planeamiento de movimiento lateral.
>
Ingresamos a la web:
http://TU_IP:7474 con el usuario neo4j y contraseña se encuentra ejecutando el comando `cat /home/admin/cloud-security-tools/state/stormspotter_neo4j_password.txt`. Importante recuerda abrir el puerto 7474 y 7687 en el Grupo de Seguridad de la Instancia

####Fase de Recolección (Gather) y Carga de datos al contenedor Docker
>Esta fase usa tus credenciales de AWS (el perfil pentesting2) para generar un archivo comprimido con toda la metadata de la cuenta. 

>Ver mayor información de uso en https://github.com/Azure/Stormspotter

### PMapper
>Herramienta para AWS IAM que modela usuarios y roles como un grafo dirigido para identificar riesgos de configuración, especialmente privilege escalation y caminos alternos de acceso a recursos. Etapa: análisis de permisos/IAM, modelado de rutas de ataque y preparación de escalamiento.
```
pmapper --help
pmapper --profile pentesting2 graph create
pmapper --profile pentesting2 analysis
pmapper --profile pentesting2 visualize
#Una posibilidad para ver la imagen generada es levantar un servidor web en el directorio actual
python3 -m http.server 80
```
>>Analizar la imagen generada.

### Cloudsplaining
>Herramienta de assessment de IAM en AWS centrada en detectar violaciones al principio de least privilege y priorizar riesgos como data exfiltration, infrastructure modification, resource exposure y privilege escalation. Etapa: revisión de permisos, priorización de hallazgos IAM y pre-explotación.
>
```
cloudsplaining --help
aws iam get-account-authorization-details --profile pentesting2 > account-details.json
cloudsplaining scan --input-file account-details.json
#Una posibilidad para ver en archivo html generado es levantar un servidor web en el directorio actual
python3 -m http.server 80
```
>>

### MicroBurst
>Toolkit en PowerShell para Azure con scripts para discovery de servicios, auditoría de configuraciones débiles y acciones de post-explotación como credential dumping. Etapa: cubre varias, pero principalmente enumeración interna, auditoría de debilidades y post-explotación.

> Ver mayor información en https://github.com/NetSPI/MicroBurst

### gcp_scanner
>Scanner para GCP que ayuda a determinar qué nivel de acceso tienen ciertas credenciales y está pensado para evaluar el impacto de una comprometida de VM/container, una service account o una OAuth token leak. >
Etapa: enumeración autenticada, impact assessment y post-compromiso/post-explotación en GCP.

> Ver mayor información en https://github.com/google/gcp_scanner
