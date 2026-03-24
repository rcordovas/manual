# Instrucciones de Pentesting en nube

En una instancia debian de tamaño recomendado t3.small y tamaño de disco mínimo de 20GB.

## Descargamos los scripts necesarios
Una vez en la maquina, ingresamos como usuario con el comando:
sudo su

Luego descargamos de los scripts
```
wget https://raw.githubusercontent.com/rcordovas/scripts/refs/heads/main/script.sh
wget https://raw.githubusercontent.com/rcordovas/scripts/refs/heads/main/script2.sh
```

## Instalar los scripts 
```
bash ./script.sh
bash ./script.sh
```

## Usar cloud_enum
>
Herramienta OSINT multi-cloud para enumerar recursos públicos o semipúblicos en AWS, Azure y GCP, como buckets, blobs, Firebase, App Engine y Cloud Functions. Etapa: reconocimiento externo / discovery inicial del attack surface.
>

## CloudBrute
>
Herramienta de descubrimiento black-box para encontrar infraestructura, archivos y aplicaciones de un objetivo en varios proveedores cloud; está pensada para hallar buckets, apps y bases de datos expuestas sin necesidad de autenticarse. Etapa: reconocimiento externo y mapeo de superficie expuesta.
>

## Prowler
>
Plataforma/herramienta de assessment de seguridad y compliance con cientos de checks listos para usar sobre entornos cloud. No está enfocada en explotar, sino en detectar hallazgos de configuración y postura. Etapa: evaluación de postura de seguridad / misconfiguraciones y también validación posterior al hardening.
>

## ScoutSuite
>
Herramienta de auditoría multi-cloud que usa las APIs del proveedor para recopilar configuración, resaltar áreas de riesgo y presentar una vista clara del attack surface. Etapa: enumeración interna autenticada y evaluación de postura/configuración.
>

## Pacu
>Framework ofensivo para AWS diseñado para explotar fallas de configuración dentro de una cuenta; incluye módulos para privilege escalation, backdooring de usuarios IAM y abuso de funciones Lambda, entre otros. Etapa: explotación, post-explotación y escalamiento de privilegios.
>

## Stormspotter
>
Herramienta para Azure que construye un attack graph de recursos y objetos de Azure/Azure AD, permitiendo visualizar superficie de ataque y oportunidades de pivoting. Etapa: enumeración interna, análisis de rutas de ataque y planeamiento de movimiento lateral.
>

## PMapper
>
Herramienta para AWS IAM que modela usuarios y roles como un grafo dirigido para identificar riesgos de configuración, especialmente privilege escalation y caminos alternos de acceso a recursos. Etapa: análisis de permisos/IAM, modelado de rutas de ataque y preparación de escalamiento.
>

## Cloudsplaining
>
Herramienta de assessment de IAM en AWS centrada en detectar violaciones al principio de least privilege y priorizar riesgos como data exfiltration, infrastructure modification, resource exposure y privilege escalation. Etapa: revisión de permisos, priorización de hallazgos IAM y pre-explotación.
>

## MicroBurst
>
Toolkit en PowerShell para Azure con scripts para discovery de servicios, auditoría de configuraciones débiles y acciones de post-explotación como credential dumping. Etapa: cubre varias, pero principalmente enumeración interna, auditoría de debilidades y post-explotación.
>

## gcp_scanner
>
Scanner para GCP que ayuda a determinar qué nivel de acceso tienen ciertas credenciales y está pensado para evaluar el impacto de una comprometida de VM/container, una service account o una OAuth token leak. >
Etapa: enumeración autenticada, impact assessment y post-compromiso/post-explotación en GCP.
>
