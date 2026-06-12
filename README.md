# Descripción
Esta solución proporciona una línea base de despliegue para aplicaciones contenerizadas en AWS utilizando Amazon ECS Fargate y CloudFormation mediante stacks anidados. El objetivo es estandarizar la implementación de servicios ECS siguiendo buenas prácticas de arquitectura cloud, modularidad, seguridad, observabilidad y reutilización de componentes.

La arquitectura permite desplegar componentes transversales compartidos, como balanceadores de carga y clusters ECS, así como componentes funcionales específicos para cada aplicación o microservicio.

La solución está diseñada para integrarse fácilmente con pipelines de CI/CD y facilitar la administración independiente de cada componente mediante templates desacoplados y reutilizables.

# Arquitectura de la Solución
![Arq](./samples/Pragma%20ECS%20Reference.drawio.png)

## Componentes Transversales
La capa transversal contiene los componentes compartidos de infraestructura necesarios para soportar múltiples servicios ECS.
Incluye:

* ECS Cluster
* Application Load Balancer (ALB)
* Listener HTTP
* Security Groups del balanceador
* Reglas de ingreso
* Recursos compartidos de conectividad

## Componentes Funcionales
La capa funcional despliega los componentes específicos de una aplicación o microservicio sobre ECS Fargate.
Incluye:

* ECS Task Definition
* ECS Service
* Target Group
* Listener Rules
* IAM Roles
* IAM Policies
* CloudWatch Log Groups
* Security Groups del servicio

# Arquitectura General
```text
Internet
    |
Application Load Balancer
    |
Listener
    |
Listener Rule
    |
Target Group
    |
ECS Service (Fargate)
    |
Task Definition
    |
Containers
```

# Estructura del Repositorio
```text
.
├── README.md
├── samples
│   ├── domain_transversal
│   │   ├── master.yaml
│   │   └── parameters.json
│   └── domain_functionality
│       ├── master.yaml
│       └── parameters.json
└── templates
    ├── elb-listener-rule-template.yaml
    ├── elb-listener-template.yaml
    ├── elb-target-group.yaml
    ├── elb-template.yaml
    ├── log-group-template.yaml
    ├── role-policy-template.yaml
    ├── role-template.yaml
    ├── service-template.yaml
    ├── sg-ingress-rule-template.yaml
    ├── sg-template.yaml
    └── task-template.yaml
```

* `templates`: Contiene templates desacoplados para cada componente de infraestructura.
* `samples/domain_transversal`: Contiene el stack principal para desplegar la infraestructura transversal compartida.
* `samples/domain_functionality`: Contiene el stack principal para desplegar servicios ECS funcionales.

# Componentes Incluidos
## Networking y Seguridad
* Security Groups
* Reglas de ingreso para ALB y ECS
* Integración con VPC existente
* Integración con subnets existentes

## Balanceo de Carga
* Application Load Balancer (ALB)
* Listeners HTTP
* Listener Rules
* Target Groups

## ECS y Contenedores
* ECS Cluster
* ECS Task Definition
* ECS Service
* ECS Fargate
* Integración con imágenes Docker

## Observabilidad
* CloudWatch Log Groups
* Logs centralizados de contenedores

## IAM
* Execution Role
* Task Role
* Policies desacopladas

# Características de la Solución
| Característica         | Descripción                                                    |
| ---------------------- | -------------------------------------------------------------- |
| Arquitectura modular   | Cada componente se despliega mediante templates independientes |
| Reutilización          | Templates reutilizables entre proyectos y ambientes            |
| Despliegue desacoplado | Separación entre componentes transversales y funcionales       |
| ECS Fargate            | Infraestructura serverless para contenedores                   |
| Balanceo de carga      | Integración con ALB y Target Groups                            |
| Observabilidad         | Integración con CloudWatch Logs                                |
| Seguridad              | Roles IAM separados y Security Groups dedicados                |
| Escalabilidad          | Base preparada para Auto Scaling                               |
| Integración CI/CD      | Compatible con pipelines automatizados                         |

# Flujo de Despliegue
## 1. Despliegue Transversal
Primero se despliegan los recursos compartidos:
* ECS Cluster
* ALB
* Listener
* Security Groups transversales

## 2. Despliegue Funcional
Posteriormente se despliegan los servicios funcionales:

* Task Definitions
* ECS Services
* Target Groups
* Listener Rules
* Roles IAM
* Logs

# Buenas Prácticas Implementadas
## ECS Fargate
* Separación entre Execution Role y Task Role.
* Uso de Target Groups tipo `ip`.
* Integración nativa con ALB.
* Logs centralizados en CloudWatch.

## Seguridad
* Security Groups desacoplados.
* Políticas IAM separadas por responsabilidad.
* Restricción de tráfico mediante reglas específicas.

## CloudFormation
* Uso de stacks anidados.
* Reutilización de templates.
* Exportación e importación de outputs entre stacks.

# Estándares Pragma
* [Estándar de Nombramiento](https://alejandria.pragma.co/es/private/conocimiento-aplicado/infraestructura/kc-cc/cloud-computing/gobierno/naming-convention)
* [Estándar de Tags](https://alejandria.pragma.co/es/private/conocimiento-aplicado/infraestructura/kc-cc/cloud-computing/gobierno/politica-tags)

# Guía de Inicio Rápido
## Prerrequisitos
* Cuenta de AWS.
* VPC y subnets previamente creadas.
* Bucket S3 con los templates cargados.
* Permisos para CloudFormation, ECS, IAM, ELB y CloudWatch.
* Rol de CloudFormation con permisos suficientes para desplegar infraestructura.

# Despliegue
## Paso 1: Cargar Templates
Cargar todos los templates del directorio `templates` en un bucket S3.

## Paso 2: Desplegar Infraestructura Transversal
Desplegar el archivo:
```text
samples/domain_transversal/master.yaml
```

## Paso 3: Desplegar Servicios Funcionales
Desplegar el archivo:
```text
samples/domain_functionality/master.yaml
```

## Paso 4: Validar Recursos
Verificar:
* ECS Cluster
* ECS Service
* Tasks en ejecución
* ALB
* Target Groups
* Logs en CloudWatch

# Posibles Mejoras Futuras
* Auto Scaling para ECS Service.
* Soporte HTTPS con ACM.
* Integración con Route53.
* Integración con AWS WAF.
* Despliegues Blue/Green.
* Integración con CodePipeline o GitHub Actions.
* Integración con Service Discovery.

# Versiones
| Versión | Realizó                         | Aprobó    | Fecha      |
| ------- | ------------------------------- | --------- | ---------- |
| v1.0.0  | Camilo Andres Perez Quintanilla | Felipe Arciniegas Cuellar | 2026/06/12 |
