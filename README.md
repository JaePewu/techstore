# TechStore

TechStore es una API REST desarrollada con Java y Spring Boot para administrar productos de una tienda ficticia.  
El sistema permite autenticar usuarios mediante JWT, gestionar productos con operaciones CRUD, aplicar borrado lógico y persistir la información en PostgreSQL.

El proyecto incorpora contenedores Docker, despliegue en AWS con ECS Fargate, exposición mediante Application Load Balancer y API Gateway, auditoría asíncrona con SQS y Lambda, monitoreo con CloudWatch y automatización de despliegue mediante GitHub Actions.

---

## Índice

- [Creadoras del proyecto](#creadoras-del-proyecto)
- [Objetivo del proyecto](#objetivo-del-proyecto)
- [Funcionalidades principales](#funcionalidades-principales)
- [Arquitectura general](#arquitectura-general)
- [Diagrama general de arquitectura](#diagrama-general-de-arquitectura)
- [Microservicio principal y componentes](#microservicio-principal-y-componentes)
- [Tecnologías utilizadas](#tecnologías-utilizadas)
- [Base de datos](#base-de-datos)
- [Usuarios de prueba para login](#usuarios-de-prueba-para-login)
- [Ejecución local con Docker Compose](#ejecución-local-con-docker-compose)
- [Despliegue cloud en AWS](#despliegue-cloud-en-aws)
- [Servicios AWS utilizados](#servicios-aws-utilizados)
- [Escalabilidad y monitoreo en ECS Fargate](#escalabilidad-y-monitoreo-en-ecs-fargate)
- [Endpoints principales](#endpoints-principales)
- [Uso del token en Postman](#uso-del-token-en-postman)
- [Flujo de auditoría con SQS y Lambda](#flujo-de-auditoría-con-sqs-y-lambda)
- [CI/CD con GitHub Actions](#cicd-con-github-actions)
- [Comandos útiles](#comandos-útiles)
- [Evidencias técnicas](#evidencias-técnicas)
- [Mejora propuesta](#mejora-propuesta)
- [Declaración de uso de IA](#declaración-de-uso-de-ia)
- [Estado final del proyecto](#estado-final-del-proyecto)

---

## Creadoras del proyecto

- Victoria Bustos
- Jael Yapur

---

## Objetivo del proyecto

El objetivo de TechStore es implementar una API REST funcional para la gestión de productos, aplicando una arquitectura por capas en Spring Boot y utilizando servicios cloud para su despliegue, monitoreo, persistencia y procesamiento asíncrono.

El sistema permite administrar productos de una tienda ficticia, proteger endpoints mediante JWT, persistir información en PostgreSQL y ejecutar la aplicación en un entorno cloud basado en AWS.

---

## Funcionalidades principales

- Login con autenticación JWT.
- Listado de productos activos.
- Creación de productos.
- Modificación de productos.
- Eliminación mediante borrado lógico.
- Persistencia en PostgreSQL.
- Ejecución local mediante Docker Compose.
- Imagen Docker publicada en Amazon ECR.
- Despliegue del microservicio en ECS Fargate.
- Exposición mediante Application Load Balancer.
- Exposición controlada mediante API Gateway.
- Auditoría asíncrona mediante SQS y Lambda.
- Registro de logs en CloudWatch.
- Pipeline CI/CD con GitHub Actions.

---

## Arquitectura general

El proyecto utiliza una arquitectura por capas dentro del microservicio Spring Boot:

```text
Controller → Service → Repository → Base de datos
        ↓
      DTO / Model
        ↓
     Security JWT
```

### Capas principales

- `controller`: recibe las peticiones HTTP.
- `service`: contiene la lógica de negocio.
- `repository`: conecta con la base de datos mediante JPA.
- `model`: contiene la entidad Producto.
- `dto`: contiene los objetos de transferencia de datos.
- `security`: contiene la configuración de JWT, filtros y seguridad.
- `config`: contiene configuraciones de apoyo del sistema.
- `audit`: contiene la lógica relacionada con eventos de auditoría.

---

## Diagrama general de arquitectura

El sistema se desplegó en AWS utilizando servicios administrados para ejecutar, exponer, persistir y monitorear el microservicio.

Flujo principal:

```text
Postman / Cliente
        ↓
Amazon API Gateway
        ↓
Application Load Balancer
        ↓
ECS Fargate - techstore-api
        ↓
Amazon RDS PostgreSQL
```

Flujo de auditoría asíncrona:

```text
techstore-api
        ↓
Amazon SQS
        ↓
AWS Lambda
        ↓
Amazon CloudWatch Logs
```

Flujo de despliegue automatizado:

```text
GitHub Actions
        ↓
Amazon ECR
        ↓
ECS Fargate
```

Este diagrama permite visualizar cómo se expone la API, cómo se ejecuta el microservicio, cómo se persisten los datos y cómo se procesan eventos de auditoría de forma asíncrona.

---

## Microservicio principal y componentes

### `techstore-api`

Microservicio principal desarrollado con Spring Boot.  
Contiene la lógica de autenticación, seguridad JWT, CRUD de productos, conexión a PostgreSQL y envío de eventos de auditoría hacia SQS.

### `api-gateway`

Componente utilizado en el entorno local para enrutar peticiones durante pruebas con Docker Compose.  
En AWS, el punto de entrada principal corresponde a Amazon API Gateway.

### `frontend`

Carpeta considerada para componentes visuales o pruebas de integración, según el avance del proyecto.

### `.github/workflows`

Contiene el workflow de GitHub Actions utilizado para construir la imagen Docker, publicarla en Amazon ECR y actualizar el servicio ECS.

---

## Tecnologías utilizadas

- Java 21
- Spring Boot
- Spring Web
- Spring Data JPA
- Spring Security
- JWT
- PostgreSQL
- Docker
- Docker Compose
- Maven
- Postman
- Git / GitHub
- GitHub Actions
- Amazon ECR
- Amazon ECS Fargate
- Amazon RDS PostgreSQL
- Amazon SQS
- AWS Lambda
- Amazon CloudWatch Logs
- Application Load Balancer
- Amazon API Gateway

---

## Base de datos

El proyecto utiliza PostgreSQL.

### Entorno local

En entorno local, PostgreSQL puede ejecutarse mediante Docker Compose.

Datos de conexión local referenciales:

```text
Base de datos: techstore
Usuario: admin
Contraseña: admin123
Puerto local: 5433
Puerto interno Docker: 5432
```

> La contraseña `admin123` corresponde al entorno local de PostgreSQL y no debe confundirse con credenciales de AWS o credenciales productivas.

### Entorno AWS

En AWS, la base de datos se ejecuta mediante Amazon RDS PostgreSQL.

Datos generales:

```text
Motor: PostgreSQL
Base de datos: techstore
Servicio: Amazon RDS
Región: us-east-1
```

> La contraseña de RDS no se debe subir al repositorio ni mostrarse en evidencias.

---

## Usuarios de prueba para login

Usuario administrador:

```text
Usuario: admin
Contraseña: admin
Rol: ADMIN
```

Usuario estándar:

```text
Usuario: user
Contraseña: password
Rol: USER
```

---

## Ejecución local con Docker Compose

Desde la raíz del proyecto, generar primero el archivo `.jar`:

```bash
./mvnw package -DskipTests
```

En Windows CMD o PowerShell:

```bash
mvnw.cmd package -DskipTests
```

Luego ejecutar:

```bash
docker compose down
docker compose up --build
```

Esto levanta:

- Contenedor de PostgreSQL.
- Contenedor del microservicio Spring Boot.
- Componentes definidos en `docker-compose.yml`.

Para verificar los contenedores activos:

```bash
docker ps
```

---

## Despliegue cloud en AWS

El despliegue cloud se realizó utilizando servicios administrados de AWS.

Flujo general:

1. Se construye la imagen Docker del microservicio.
2. La imagen se publica en Amazon ECR.
3. ECS Fargate ejecuta el contenedor.
4. El Application Load Balancer distribuye el tráfico hacia la tarea ECS.
5. API Gateway expone el acceso principal a la API.
6. El microservicio se conecta a RDS PostgreSQL.
7. Las operaciones generan eventos enviados a SQS.
8. Lambda procesa los eventos de auditoría.
9. CloudWatch registra logs de la aplicación y de Lambda.

---

## Servicios AWS utilizados

| Servicio | Uso dentro del proyecto |
|---|---|
| Amazon ECR | Almacenar la imagen Docker del microservicio |
| Amazon ECS Fargate | Ejecutar el contenedor sin administrar servidores EC2 |
| Amazon RDS PostgreSQL | Persistir la información de productos |
| Amazon SQS | Recibir eventos de auditoría generados por la API |
| AWS Lambda | Procesar mensajes de auditoría desde SQS |
| Amazon CloudWatch Logs | Registrar logs del microservicio y de Lambda |
| Application Load Balancer | Distribuir tráfico hacia ECS |
| Amazon API Gateway | Exponer la API como punto de entrada |
| GitHub Actions | Automatizar build, push y despliegue |

---

## Escalabilidad y monitoreo en ECS Fargate

El servicio `techstore-api-service` fue desplegado en ECS Fargate con una tarea en ejecución para el entorno de laboratorio.

ECS Fargate permite ejecutar contenedores sin administrar servidores EC2 directamente. En caso de falla de una tarea, ECS puede iniciar una nueva tarea para mantener el estado deseado del servicio.

Configuración utilizada:

```text
CPU: 0.25 vCPU
Memoria: 0.5 GB
Desired count: 1 tarea
Health check: /api/health
Logs: CloudWatch Logs
```

En un entorno productivo, este servicio podría escalar aumentando el número de tareas según métricas como uso de CPU, memoria o cantidad de solicitudes recibidas.

---

## Endpoints principales

### Health check

```http
GET /api/health
```

Respuesta esperada:

```text
OK
```

---

### Login

```http
POST /api/auth/login
```

Body:

```json
{
  "username": "admin",
  "password": "admin"
}
```

Respuesta esperada:

```json
{
  "token": "TOKEN_GENERADO",
  "tipo": "Bearer",
  "username": "admin"
}
```

---

### Listar productos

```http
GET /api/productos
```

Requiere token JWT.

---

### Crear producto

```http
POST /api/productos
```

Requiere token JWT.

Body:

```json
{
  "nombre": "Mouse Logitech",
  "descripcion": "Mouse inalámbrico",
  "precio": 12990,
  "stock": 20,
  "categoria": "Computación",
  "activo": true
}
```

Respuesta esperada:

```text
201 Created
```

---

### Modificar producto

```http
PUT /api/productos/1
```

Requiere token JWT.

Body:

```json
{
  "nombre": "Mouse Logitech Actualizado",
  "descripcion": "Mouse inalámbrico USB",
  "precio": 14990,
  "stock": 15,
  "categoria": "Computación",
  "activo": true
}
```

Respuesta esperada:

```text
200 OK
```

---

### Eliminar producto

```http
DELETE /api/productos/1
```

Requiere token JWT.

Respuesta esperada:

```text
204 No Content
```

El sistema no elimina físicamente el producto de la base de datos. Solo cambia el campo:

```text
activo = false
```

Esto corresponde a un borrado lógico.

---

## Uso del token en Postman

Después de realizar login:

1. Copiar el valor del campo `token`.
2. Ir al endpoint protegido.
3. Abrir la pestaña `Auth`.
4. Seleccionar `Bearer Token`.
5. Pegar el token.
6. Enviar la petición.

También se puede usar el header:

```http
Authorization: Bearer TOKEN_GENERADO
```

---

## Flujo de auditoría con SQS y Lambda

El microservicio genera eventos de auditoría cuando se realizan operaciones sobre productos.

Flujo:

```text
Operación en la API
        ↓
Evento de auditoría
        ↓
Amazon SQS
        ↓
AWS Lambda
        ↓
CloudWatch Logs
```

Este flujo permite procesar información de auditoría de forma asíncrona, desacoplando la operación principal del registro posterior de eventos.

---

## CI/CD con GitHub Actions

El proyecto incluye un workflow de GitHub Actions para automatizar el despliegue.

El pipeline realiza:

1. Checkout del código.
2. Configuración de Java 21.
3. Build con Maven.
4. Configuración de credenciales AWS.
5. Login en Amazon ECR.
6. Construcción de imagen Docker.
7. Push de imagen hacia ECR.
8. Actualización del servicio ECS.

> En AWS Academy, el `AWS_SESSION_TOKEN` cambia cada vez que se renuevan las credenciales del laboratorio.  
> Por este motivo, los secrets de GitHub Actions deben actualizarse cuando el laboratorio se reinicia.

Secrets utilizados en GitHub Actions:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN
```

Opcionalmente, según el workflow:

```text
AWS_REGION
AWS_ACCOUNT_ID
ECR_REPOSITORY
ECS_CLUSTER
ECS_SERVICE
```

---

## Comandos útiles

### Git

Ver estado del repositorio:

```bash
git status
```

Agregar cambios:

```bash
git add .
```

Crear commit:

```bash
git commit -m "mensaje del commit"
```

Subir cambios:

```bash
git push origin main
```

---

### Docker

Ver contenedores activos:

```bash
docker ps
```

Ver todos los contenedores:

```bash
docker ps -a
```

Detener Docker Compose:

```bash
docker compose down
```

Levantar nuevamente el proyecto:

```bash
docker compose up --build
```

Limpiar contenedores detenidos:

```bash
docker container prune -f
```

---

### AWS CLI

> **Nota sobre los comandos AWS CLI:**  
> Los comandos de esta sección se incluyen como apoyo técnico y como referencia para verificar o reproducir la configuración realizada en AWS.  
> Durante el desarrollo del proyecto se trabajó principalmente desde la consola web de AWS y también se utilizaron comandos equivalentes de AWS CLI para avanzar más rápido, validar recursos y obtener evidencias.  
> Estos comandos fueron preparados tomando como referencia la documentación de AWS, las opciones disponibles en la consola web y apoyo de herramientas de asistencia técnica. 
>
> En AWS Academy, el `AWS_SESSION_TOKEN` cambia cada vez que se renuevan las credenciales del laboratorio, por lo que algunos comandos pueden requerir actualizar las credenciales antes de ejecutarse nuevamente.

Validar identidad:

```bash
aws sts get-caller-identity
```

Ver servicio ECS:

```bash
aws ecs describe-services \
  --cluster techstore-cluster \
  --services techstore-api-service \
  --region us-east-1 \
  --query "services[0].{status:status,runningCount:runningCount,desiredCount:desiredCount}" \
  --output table
```

Probar ALB:

```bash
curl http://techstore-alb-376766229.us-east-1.elb.amazonaws.com/api/health
```

Probar API Gateway:

```bash
curl https://c2uyh7lax4.execute-api.us-east-1.amazonaws.com/prod/api/health
```

Ver logs ECS:

```bash
MSYS_NO_PATHCONV=1 aws logs tail "/ecs/techstore-api" \
  --region us-east-1 \
  --since 30m \
  --format short
```

---

## Evidencias técnicas

El proyecto cuenta con evidencias organizadas por actividad y recurso.

Las evidencias permiten verificar:

- Imagen Docker publicada en ECR.
- Servicio ECS activo.
- Tarea Fargate en ejecución.
- Target Group en estado Healthy.
- Health check respondiendo correctamente.
- RDS PostgreSQL disponible.
- Cola SQS creada.
- Lambda conectada a SQS.
- Logs de aplicación en CloudWatch.
- Logs de auditoría generados por Lambda.
- API Gateway creado e integrado con ALB.
- Security Groups configurados.
- Pipeline de GitHub Actions ejecutado correctamente.
- Pruebas realizadas con Postman.

Tabla resumen:

| Área | Evidencia esperada |
|---|---|
| ECR | Imagen `techstore-api:latest` publicada |
| ECS | Servicio activo y tarea en ejecución |
| ALB | Balanceador activo y DNS disponible |
| Target Group | Estado Healthy |
| RDS | Base de datos PostgreSQL disponible |
| SQS | Cola creada para auditoría |
| Lambda | Trigger SQS configurado |
| CloudWatch | Logs del microservicio y de Lambda |
| API Gateway | Ruta pública hacia el ALB |
| GitHub Actions | Workflow ejecutado correctamente |
| Postman | Login JWT y CRUD de productos |

---

## Mejora propuesta

Como oportunidad de mejora futura, se propone incorporar **AWS Secrets Manager** para administrar credenciales sensibles del sistema.

Actualmente, variables como la contraseña de la base de datos, el secreto JWT y otras configuraciones sensibles pueden ser entregadas al contenedor mediante variables de entorno. Aunque esto funciona para un entorno de laboratorio, en un entorno productivo es más seguro centralizar estos valores en un servicio especializado.

Con AWS Secrets Manager se podría:

- Evitar exponer credenciales sensibles en definiciones de tarea.
- Centralizar la administración de secretos.
- Facilitar la rotación de contraseñas.
- Mejorar la seguridad general de la arquitectura.
- Separar la configuración sensible del código fuente.
- Reducir el riesgo de exposición accidental en evidencias o repositorios.

Esta mejora no fue implementada como parte del alcance actual, pero se plantea como una evolución técnica para fortalecer la seguridad del despliegue cloud.

---

## Declaración de uso de IA

Durante el desarrollo y documentación del proyecto se utilizó IA como apoyo para ordenar ideas, mejorar redacción, estructurar evidencias.

Las decisiones técnicas, configuración de recursos, pruebas, validaciones y conclusiones fueron revisadas por el equipo según el funcionamiento real del proyecto TechStore en AWS.

---

## Estado final del proyecto

- API REST funcional.
- Autenticación JWT implementada.
- CRUD de productos funcionando.
- Borrado lógico implementado.
- PostgreSQL funcionando en entorno local y AWS.
- Proyecto dockerizado.
- Docker Compose funcionando para entorno local.
- Imagen publicada en Amazon ECR.
- Servicio desplegado en ECS Fargate.
- Application Load Balancer activo.
- Target Group en estado Healthy.
- API Gateway configurado como punto de entrada.
- SQS integrado para auditoría.
- Lambda procesando mensajes de auditoría.
- CloudWatch registrando logs.
- Pipeline CI/CD configurado con GitHub Actions.
- Pruebas realizadas con Postman.
- Proyecto subido a GitHub.
