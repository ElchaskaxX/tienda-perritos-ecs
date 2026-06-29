\# 🐶 Tienda de Perritos — DevOps EP3



Aplicación de gestión de productos desplegada en AWS ECS con Fargate, pipeline CI/CD con GitHub Actions y autoscaling automático.



\## Arquitectura



\- \*\*Frontend:\*\* Nginx (React/HTML estático) → ECS Fargate

\- \*\*Backend:\*\* Node.js API REST → ECS Fargate  

\- \*\*Base de datos:\*\* MySQL

\- \*\*Registry:\*\* Amazon ECR

\- \*\*Orquestación:\*\* AWS ECS con Fargate

\- \*\*CI/CD:\*\* GitHub Actions

\- \*\*Logs:\*\* Amazon CloudWatch

\- \*\*Autoscaling:\*\* Application Auto Scaling (Target Tracking 50% CPU)



\## URL Pública

http://54.166.165.251



\## Estructura del proyecto



tienda-perritos/

├── frontend/

│   ├── Dockerfile

│   ├── default.conf

│   ├── index.html

│   └── app.js

├── backend/

│   ├── Dockerfile

│   ├── package.json

│   └── server.js

├── db/

│   └── init.sql

├── k8s/

│   └── (manifests de referencia)

└── .github/

└── workflows/

└── deploy-eks.yml



\## Requisitos previos



\- AWS CLI v2

\- Docker Desktop

\- Git

\- Cuenta AWS Academy con laboratorio activo



\## Cómo desplegar manualmente



\### 1. Configurar credenciales AWS

Copiar credenciales desde AWS Academy en `\~/.aws/credentials`:

\[default]



aws\_access\_key\_id=TU\_ACCESS\_KEY



aws\_secret\_access\_key=TU\_SECRET\_KEY



aws\_session\_token=TU\_SESSION\_TOKEN



\### 2. Login a ECR

```bash

aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 609490184594.dkr.ecr.us-east-1.amazonaws.com

```



\### 3. Build y Push imágenes

```bash

\# Frontend

docker build -t tienda-perritos-frontend ./frontend

docker tag tienda-perritos-frontend:latest 609490184594.dkr.ecr.us-east-1.amazonaws.com/tienda-perritos-frontend:latest

docker push 609490184594.dkr.ecr.us-east-1.amazonaws.com/tienda-perritos-frontend:latest



\# Backend

docker build -t tienda-perritos-backend ./backend

docker tag tienda-perritos-backend:latest 609490184594.dkr.ecr.us-east-1.amazonaws.com/tienda-perritos-backend:latest

docker push 609490184594.dkr.ecr.us-east-1.amazonaws.com/tienda-perritos-backend:latest

```



\### 4. Deploy a ECS

```bash

aws ecs update-service --cluster tienda-perritos --service frontend-service --force-new-deployment

aws ecs update-service --cluster tienda-perritos --service backend-service --force-new-deployment

```



\## Pipeline CI/CD



El pipeline se activa automáticamente con cada push a `main`:



1\. \*\*Checkout\*\* — descarga el código

2\. \*\*Configurar AWS\*\* — usa secrets de GitHub

3\. \*\*Login ECR\*\* — autenticación al registry

4\. \*\*Build y Push Frontend\*\* — construye y sube imagen

5\. \*\*Build y Push Backend\*\* — construye y sube imagen

6\. \*\*Deploy Frontend\*\* — actualiza servicio ECS

7\. \*\*Deploy Backend\*\* — actualiza servicio ECS

8\. \*\*Verificar\*\* — espera que los servicios estabilicen



\## Autoscaling



Configurado con Target Tracking al \*\*50% de CPU\*\*:



| Servicio | Mínimo | Máximo | Umbral CPU |

|----------|--------|--------|------------|

| frontend-service | 1 | 4 | 50% |

| backend-service | 1 | 4 | 50% |



\## Logs



Los logs están disponibles en CloudWatch:

\- `/ecs/tienda-frontend`

\- `/ecs/tienda-backend`



Para ver logs desde CLI:

```bash

aws logs get-log-events --log-group-name /ecs/tienda-frontend --log-stream-name ecs/frontend/TASK\_ID

```



\## Recursos AWS creados



| Recurso | Nombre |

|---------|--------|

| ECS Cluster | tienda-perritos |

| ECS Service Frontend | frontend-service |

| ECS Service Backend | backend-service |

| ECR Frontend | tienda-perritos-frontend |

| ECR Backend | tienda-perritos-backend |

| Security Group | tienda-perritos-sg |

| CloudWatch Log Group | /ecs/tienda-frontend, /ecs/tienda-backend |



\## Integrantes



\- Benjamín Gajardo 

\- Benjamín Aravena



\## Curso



ISY1101 — Introducción a Herramientas DevOps  

DuocUC — 2025

