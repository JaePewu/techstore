# TechStore Cloud

Microservicio RESTful desarrollado en Java con Spring Boot para la tienda ficticia **TechStore Chile**.

El proyecto permite gestionar productos mediante una API protegida con JWT, persistencia en PostgreSQL, envío de eventos de auditoría a SQS, despliegue en contenedores Docker y despliegue cloud en AWS usando ECR, ECS Fargate, RDS, SQS, Lambda, CloudWatch, Application Load Balancer, API Gateway y GitHub Actions.

Este proyecto fue desarrollado como parte de la **Evaluación Sumativa 3** del curso **JVY0101 - Java: Diseño y Construcción de Soluciones nativas en Nube**.

---

## Índice

- [Creadoras del proyecto](#creadoras-del-proyecto)
- [Objetivo del proyecto](#objetivo-del-proyecto)
- [Funcionalidades principales](#funcionalidades-principales)
- [Arquitectura general](#arquitectura-general)
- [Estructura del proyecto](#estructura-del-proyecto)
- [Tecnologías utilizadas](#tecnologías-utilizadas)
- [Microservicios y componentes](#microservicios-y-componentes)
- [Seguridad con JWT](#seguridad-con-jwt)
- [Base de datos](#base-de-datos)
- [Auditoría con SQS y Lambda](#auditoría-con-sqs-y-lambda)
- [Ejecución local](#ejecución-local)
- [Ejecución con Docker / Docker Swarm](#ejecución-con-docker--docker-swarm)
- [Despliegue en AWS](#despliegue-en-aws)
- [CI/CD con GitHub Actions](#cicd-con-github-actions)
- [Endpoints principales](#endpoints-principales)
- [Uso del token en Postman](#uso-del-token-en-postman)
- [Evidencias](#evidencias)
- [Comandos útiles](#comandos-útiles)
- [Estado final del proyecto](#estado-final-del-proyecto)

---

## Creadoras del proyecto

- Victoria Bustos
- Jael Yapur

---

## Objetivo del proyecto

Construir y desplegar una solución cloud basada en microservicios, aplicando los contenidos trabajados en clases:

- Spring Boot.
- API REST.
- Seguridad con JWT.
- Arquitectura por capas.
- PostgreSQL local y PostgreSQL en AWS RDS.
- Docker y Docker Compose / Docker Swarm.
- Amazon ECR.
- Amazon ECS Fargate.
- Application Load Balancer.
- Amazon API Gateway.
- Amazon SQS.
- AWS Lambda.
- Amazon CloudWatch Logs.
- CI/CD con GitHub Actions.
- Pruebas con Postman.

---

## Funcionalidades principales

- Login con JWT.
- Listar productos activos.
- Crear productos.
- Modificar productos.
- Eliminar productos mediante borrado lógico.
- Persistencia de productos en PostgreSQL.
- Envío de eventos de auditoría a Amazon SQS.
- Procesamiento automático de eventos mediante Lambda.
- Registro de logs en CloudWatch.
- Despliegue del microservicio en ECS Fargate.
- Exposición del servicio mediante ALB y API Gateway.
- Pipeline CI/CD para construir imagen Docker, subirla a ECR y actualizar ECS.

---

## Arquitectura general

### Arquitectura local

```text
Frontend / Postman
        ↓
API Gateway local
        ↓
TechStore API
        ↓
PostgreSQL en Docker
```

### Arquitectura en AWS

```text
Postman / Navegador
        ↓
Amazon API Gateway
        ↓
Application Load Balancer
        ↓
ECS Fargate - techstore-api-service
        ↓
Amazon RDS PostgreSQL

TechStore API
        ↓
Amazon SQS - techstore-audit-queue
        ↓
AWS Lambda - techstore-audit-logger
        ↓
Amazon CloudWatch Logs
```

---

## Estructura del proyecto

```text
techstore-2/
│
├── .github/
│   └── workflows/
│       └── deploy.yml
│
├── api-gateway/
│   ├── Dockerfile
│   ├── pom.xml
│   └── src/
│
├── techstore-api/
│   ├── Dockerfile
│   ├── pom.xml
│   └── src/
│       └── main/
│           ├── java/cl/techstore/techstore_api/
│           │   ├── controller/
│           │   ├── dto/
│           │   ├── model/
│           │   ├── repository/
│           │   ├── security/
│           │   └── service/
│           └── resources/
│               └── application.properties
│
├── frontend/
│   ├── index.html
│   ├── style.css
│   └── app.js
│
├── evidencias/
│   └── README_EVIDENCIAS_TECHSTORE.md
│
├── docker-compose.yml
└── README.md
```

---

## Tecnologías utilizadas

- Java 21.
- Spring Boot 3.2.5.
- Spring Web.
- Spring Data JPA.
- Spring Security.
- JWT.
- PostgreSQL.
- Docker.
- Docker Compose.
- Docker Swarm.
- Maven.
- Postman.
- Git y GitHub.
- GitHub Actions.
- Amazon ECR.
- Amazon ECS Fargate.
- Amazon RDS PostgreSQL.
- Amazon SQS.
- AWS Lambda.
- Amazon CloudWatch Logs.
- Amazon API Gateway.
- Application Load Balancer.

---

## Microservicios y componentes

### 1. `techstore-api`

Microservicio principal del proyecto. Expone los endpoints de autenticación, salud y gestión de productos.

Capas principales:

```text
Controller → Service → Repository → Base de datos
        ↓
      DTO / Model
        ↓
     Security JWT
        ↓
   Auditoría SQS
```

Responsabilidades:

- Autenticación con JWT.
- CRUD de productos.
- Borrado lógico de productos.
- Persistencia en PostgreSQL.
- Envío de eventos de auditoría a SQS.

### 2. `api-gateway`

Gateway local desarrollado con Spring Cloud Gateway.

Responsabilidades:

- Recibir peticiones en el puerto `8080`.
- Redirigir las rutas `/api/**` hacia `techstore-api`.
- Permitir integración con frontend local y Postman.

### 3. `frontend`

Frontend simple en HTML, CSS y JavaScript para probar login y gestión de productos desde navegador.

---

## Seguridad con JWT

La API utiliza Spring Security y JWT.

Endpoints públicos:

```text
POST /api/auth/login
GET  /api/health
```

Todos los demás endpoints requieren token JWT mediante header:

```text
Authorization: Bearer TOKEN_GENERADO
```

Usuarios de prueba:

| Usuario | Contraseña | Rol |
|---|---|---|
| `admin` | `admin` | ADMIN |
| `user` | `password` | USER |

---

## Base de datos

### Base de datos local con Docker

El proyecto utiliza PostgreSQL mediante Docker.

Datos de conexión local:

| Dato | Valor |
|---|---|
| Base de datos | `techstore` |
| Usuario | `admin` |
| Contraseña | `admin123` |
| Puerto local | `5433` |
| Puerto interno Docker | `5432` |

> La contraseña `admin123` corresponde al ambiente local de desarrollo, no al login de la API.

### Base de datos en AWS RDS

En AWS Academy se utilizó una instancia PostgreSQL en Amazon RDS.

| Dato | Valor usado en evidencias |
|---|---|
| Identificador | `techstore-db` |
| Motor | PostgreSQL |
| Base inicial | `techstore` |
| Usuario | `postgres` |
| Endpoint | `techstore-db.chmha6fn68zs.us-east-1.rds.amazonaws.com` |

> La contraseña de RDS no se ha subir al repositorio ni se muestra en evidencias.

---

## Auditoría con SQS y Lambda

El microservicio envía eventos de auditoría a Amazon SQS cada vez que se realiza una operación sobre productos.

Acciones auditadas:

- `CREAR`
- `MODIFICAR`
- `ELIMINAR`

Ejemplo de mensaje enviado a SQS:

```json
{
  "accion": "CREAR",
  "productoId": 1,
  "nombre": "Mouse Logitech",
  "usuario": "admin",
  "fecha": "2026-07-02T03:12:53"
}
```

Flujo:

```text
POST / PUT / DELETE producto
        ↓
ProductoService
        ↓
AuditoriaSqsService
        ↓
Amazon SQS
        ↓
AWS Lambda
        ↓
CloudWatch Logs
```

Recursos usados en AWS Academy:

| Recurso | Nombre |
|---|---|
| Cola SQS | `techstore-audit-queue` |
| Lambda | `techstore-audit-logger` |
| Logs Lambda | `/aws/lambda/techstore-audit-logger` |

---

## Ejecución local

### 1. Generar archivo `.jar` de `techstore-api`

Desde la carpeta `techstore-api`:

```bash
./mvnw clean package -DskipTests
```

En Windows CMD o PowerShell:

```bash
mvnw.cmd clean package -DskipTests
```

### 2. Generar archivo `.jar` de `api-gateway`

Desde la carpeta `api-gateway`:

```bash
./mvnw clean package -DskipTests
```

En Windows CMD o PowerShell:

```bash
mvnw.cmd clean package -DskipTests
```

---

## Ejecución con Docker / Docker Swarm

El archivo `docker-compose.yml` define tres servicios:

- `postgres`
- `techstore-api`
- `api-gateway`

### 1. Construir imagen de `techstore-api`

Desde la raíz del proyecto:

```bash
docker build -t techstore-api ./techstore-api
```

### 2. Construir imagen de `api-gateway`

```bash
docker build -t api-gateway ./api-gateway
```

### 3. Levantar con Docker Compose

```bash
docker compose up -d
```

### 4. Levantar con Docker Swarm

Si Docker Swarm no está iniciado:

```bash
docker swarm init
```

Desplegar el stack:

```bash
docker stack deploy -c docker-compose.yml techstore
```

Ver servicios:

```bash
docker service ls
```

Ver contenedores:

```bash
docker ps
```

Probar health local:

```bash
curl http://localhost:8080/api/health
```

Respuesta esperada:

```text
OK
```

---

## Despliegue en AWS

El despliegue cloud se realizó en AWS Academy, región `us-east-1`.

Recursos principales:

| Servicio | Recurso |
|---|---|
| ECR | `techstore-api` |
| RDS | `techstore-db` |
| SQS | `techstore-audit-queue` |
| Lambda | `techstore-audit-logger` |
| CloudWatch Logs | `/ecs/techstore-api` y `/aws/lambda/techstore-audit-logger` |
| ECS Cluster | `techstore-cluster` |
| ECS Task Definition | `techstore-api-task:1` |
| ECS Service | `techstore-api-service` |
| Target Group | `techstore-api-tg` |
| ALB | `techstore-alb` |
| API Gateway | `techstore-api-gw` |

### URL del ALB usada en evidencias

```text
http://techstore-alb-376766229.us-east-1.elb.amazonaws.com/api/health
```

Respuesta esperada:

```text
OK
```

### URL de API Gateway usada en evidencias

```text
https://c2uyh7lax4.execute-api.us-east-1.amazonaws.com/prod/api/health
```

Respuesta esperada:

```text
OK
```

> Las URLs de AWS Academy pueden dejar de funcionar si el laboratorio se detiene, se reinicia o expiran las credenciales temporales.

---

## CI/CD con GitHub Actions

El proyecto incluye el workflow:

```text
.github/workflows/deploy.yml
```

Este pipeline realiza:

1. Checkout del repositorio.
2. Configuración de Java 21.
3. Compilación de `techstore-api` con Maven.
4. Configuración de credenciales AWS.
5. Login en Amazon ECR.
6. Build de imagen Docker.
7. Push de imagen a ECR.
8. Force new deployment del servicio ECS.

Secrets necesarios en GitHub:

```text
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN
```

> En AWS Academy, el `AWS_SESSION_TOKEN` cambia cada vez que se renuevan las credenciales del laboratorio.

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

### Listar productos

```http
GET /api/productos
```

Requiere token JWT.

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

### Modificar producto

```http
PUT /api/productos/{id}
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

### Eliminar producto

```http
DELETE /api/productos/{id}
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

1. Ejecutar login en `/api/auth/login`.
2. Copiar el valor del campo `token`.
3. Abrir un endpoint protegido, por ejemplo `/api/productos`.
4. Ir a la pestaña **Auth**.
5. Seleccionar **Bearer Token**.
6. Pegar el token.
7. Enviar la petición.

También se puede enviar manualmente el header:

```text
Authorization: Bearer TOKEN_GENERADO
```

---

## Evidencias

Las evidencias del proyecto se encuentran en la carpeta:

```text
evidencias/
```

El archivo recomendado para revisar las evidencias es:

```text
evidencias/README_EVIDENCIAS_TECHSTORE.md
```

Ese README explica qué demuestra cada pantallazo y a qué actividad corresponde.

Resumen de cumplimiento por actividad:

| Actividad | Estado |
|---|---|
| Actividad 1: Contenerización, ECR, ECS Fargate y ALB | Completada |
| Actividad 2: SQS, Lambda, RDS, Postman, E2E y CloudWatch | Completada |
| Actividad 3: API Gateway y restricción de acceso directo | Completada |
| Actividad 4: CI/CD con GitHub Actions | Completada |
| Actividad 5: Video demostrativo | Pendiente al momento de preparar evidencias |

---

## Comandos útiles

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

Eliminar contenedores detenidos:

```bash
docker container prune -f
```

### Docker Swarm

Ver servicios:

```bash
docker service ls
```

Ver tareas de un stack:

```bash
docker stack ps techstore
```

Eliminar stack:

```bash
docker stack rm techstore
```

### PostgreSQL local

Entrar al contenedor de PostgreSQL:

```bash
docker exec -it techstore-2-postgres-1 psql -U admin -d techstore
```

Ver tablas:

```sql
\dt
```

Consultar productos:

```sql
SELECT * FROM productos;
```

Salir:

```sql
\q
```

### AWS CLI

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

> **Nota sobre los comandos AWS CLI:**  
> Los comandos de esta sección se incluyen como apoyo técnico y como referencia para verificar o reproducir la configuración realizada en AWS Academy.  
> Estos comandos fueron preparados tomando como referencia la documentación de AWS, las opciones disponibles en la consola web y el apoyo de herramientas de asistencia técnica e IA.

---

## Variables de entorno importantes

Variables usadas por `techstore-api`:

```text
SERVER_PORT
SPRING_DATASOURCE_URL
SPRING_DATASOURCE_USERNAME
SPRING_DATASOURCE_PASSWORD
JWT_SECRET
JWT_EXPIRATION
AWS_REGION
AWS_SQS_QUEUE_URL
```

No se deben subir al repositorio:

```text
Contraseña de RDS
JWT_SECRET real de producción
AWS_ACCESS_KEY_ID
AWS_SECRET_ACCESS_KEY
AWS_SESSION_TOKEN
.env
credentials
techstore-api-task.json con valores sensibles
```

---

## Estado final del proyecto

- API REST funcional.
- Login con JWT funcionando.
- CRUD de productos funcionando.
- Borrado lógico implementado.
- PostgreSQL local funcionando con Docker.
- PostgreSQL en AWS RDS funcionando.
- Auditoría hacia SQS implementada.
- Lambda procesando mensajes de SQS.
- Logs disponibles en CloudWatch.
- Imagen Docker subida a ECR.
- Servicio desplegado en ECS Fargate.
- ALB funcionando con Target Group Healthy.
- API Gateway HTTP configurado sobre el ALB.
- Restricción de puerto 8080 aplicada mediante Security Group.
- Pipeline GitHub Actions configurado y validado.
- Evidencias organizadas por actividad.
- Proyecto listo para presentación y video demostrativo.
