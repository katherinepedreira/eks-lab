Este repositorio contiene toda la infraestructura utilizada para desplegar la solución de microservicios del laboratorio en Amazon EKS, así como los archivos necesarios para ejecutar la arquitectura localmente mediante Docker Compose.

Incluye los manifiestos para:

-Users API (Django REST)

-Notification Service (Flask)

-Frontend (Nginx)

Y la configuración necesaria para:

-Crear el clúster en EKS

-Desplegar los servicios y exponerlos mediante LoadBalancer

-Probar la arquitectura completa de forma local

eks-lab/
│
├── cluster-config.yaml          # Configuración del clúster EKS (eksctl)
│
├── docker-compose.yml           # Arquitectura completa para entorno local
│
├── users-deployment.yaml        # Deployment de la Users API
├── users-service.yaml           # Service de la Users API
│
├── notification-deployment.yaml # Deployment del Notification Service
├── notification-service.yaml    # Service del Notification Service
│
├── frontend-deployment.yaml     # Deployment del frontend Nginx
└── frontend-service.yaml        # Service del frontend (LoadBalancer)


####Ejecución LOCAL con Docker Compose####

Permite probar toda la arquitectura antes de desplegarla en Kubernetes.

-Levantar todos los servicios
docker compose up --build


####Servicios expuestos####

Frontend → http://localhost:5500

Users API → http://localhost:8000/users/

Notification Service → http://localhost:5001/notify

####Detener los servicios####
docker compose down

####Creación del clúster en Amazon EKS####

-A partir del archivo cluster-config.yaml:

eksctl create cluster -f cluster-config.yaml


####Actualizar kubeconfig####

aws eks update-kubeconfig --region us-east-1 --name cluster

####Despliegue de microservicios en Kubernetes####
Users API
kubectl apply -f users-deployment.yaml
kubectl apply -f users-service.yaml

Notification Service
kubectl apply -f notification-deployment.yaml
kubectl apply -f notification-service.yaml

Frontend
kubectl apply -f frontend-deployment.yaml
kubectl apply -f frontend-service.yaml

####Verificación del despliegue####
Ver pods
kubectl get pods -A

Ver services
kubectl get svc -A


El frontend se expone mediante un LoadBalancer, cuyo External-IP se usa luego para el dominio HTTPS.

####DNS + HTTPS (CloudDNS + ACM)####

El LoadBalancer generado por frontend-service.yaml se asigna al dominio configurado en CloudDNS, y se valida el certificado SSL usando AWS ACM con registros CNAME automáticos.

Dominio usado en el lab:

https://frontend.labinfra.abrdns.com

####Requisitos previos####

AWS CLI configurado

eksctl instalado

kubectl instalado

Docker y Docker Compose

Cuenta de AWS con permisos (EKS, ACM, ELB, EC2)

Cuenta en CloudDNS (o servicio equivalente de DNS)

####Notas####

Este repositorio no contiene código fuente de los microservicios.

Los servicios están en repos separados:

-Frontend

-Users API

-Notification Service

Este repo se usa exclusivamente para infraestructura y despliegue.
