# README de evidencias – Proyecto TechStore

## Evaluación Sumativa 3 – Integración y Despliegue de Soluciones Cloud

Este **README** resume las evidencias organizadas para facilitar la revisión del proyecto **TechStore**.  
Las capturas están separadas por actividad y nombradas con una numeración correlativa para que puedan revisarse en orden sin abrir archivos al azar.

> **Nota de seguridad:** algunas capturas fueron redactadas para ocultar contraseña, JWT, tokens o valores sensibles. Esto no afecta la validación técnica de la actividad.

---

## Resumen general de cumplimiento

| Actividad | Tema evaluado | Estado de evidencia |
|---|---|---|
| **Actividad 1** | Contenerización, ECR, ECS Fargate, ALB y despliegue del microservicio | Evidenciada |
| **Actividad 2** | RDS, SQS, Lambda, CloudWatch y pruebas E2E | Evidenciada |
| **Actividad 3** | API Gateway y restricciones de seguridad con Security Groups | Evidenciada |
| **Actividad 4** | Pipeline CI/CD con GitHub Actions | Evidenciada |

---

## Estructura del ZIP

```text
Evidencias_Finales_Recomendadas/
├── 01_A1_Contenerizacion_Orquestacion/
├── 02_A2_SQS_Lambda_RDS_E2E/
├── 03_A3_API_Gateway_Seguridad/
└── 04_A4_CICD_GitHub_Actions/
```

---

# Actividad 1 – Contenerización y Orquestación

Esta actividad demuestra que el microservicio fue contenerizado, subido a ECR y desplegado en ECS Fargate mediante un Application Load Balancer.

| Evidencia | Qué demuestra |
|---|---|
| `A1_01_ECR_repositorio_techstore_api.png` | Existencia del repositorio ECR `techstore-api`. |
| `A1_02_ECR_imagen_latest.png` | Imagen Docker disponible en ECR con tag `latest`. |
| `A1_03_Docker_push_ECR.png` | Proceso de subida de la imagen Docker a ECR. |
| `A1_04_ECS_cluster_activo.png` | Cluster ECS `techstore-cluster` creado y activo. |
| `A1_05_Task_definition_Fargate_LabRole.png` | Definición de tarea Fargate con rol `LabRole`. |
| `A1_06_Task_definition_lista_activa.png` | Task Definition `techstore-api-task` registrada y activa. |
| `A1_07_ECS_service_running_1.png` | Servicio ECS con una tarea en ejecución. |
| `A1_08_ALB_detalle_DNS.png` | ALB creado con DNS público. |
| `A1_09_ALB_activo_listado.png` | Load Balancer en estado activo. |
| `A1_10_Target_group_destino_healthy.png` | Target Group con destino en estado saludable. |
| `A1_11_Health_ALB_OK.png` | Endpoint `/api/health` respondiendo correctamente desde el ALB. |
| `A1_12_Verificacion_ECS_CLI_runningCount_1.png` | Verificación por AWS CLI del servicio ECS en ejecución. |
| `A1_13_CloudWatch_SpringBoot_iniciado.png` | Logs de CloudWatch mostrando que Spring Boot inició correctamente. |
| `A1_14_Variables_entorno_tarea_REDACTADO.png` | Variables de entorno configuradas en la tarea, con datos sensibles ocultos. |

**Conclusión A1:** el microservicio fue empaquetado en Docker, publicado en ECR y desplegado correctamente en ECS Fargate con ALB y health check funcional.

---

# Actividad 2 – Integración SQS, Lambda, RDS y Validación E2E

Esta actividad demuestra la integración del microservicio con base de datos RDS, cola SQS, función Lambda y logs de CloudWatch.

| Evidencia | Qué demuestra |
|---|---|
| `A2_01_RDS_techstore_available.png` | Base de datos RDS PostgreSQL `techstore-db` disponible. |
| `A2_02_SQS_queue_creada.png` | Cola SQS `techstore-audit-queue` creada. |
| `A2_03_Lambda_techstore_audit_logger_creada.png` | Lambda `techstore-audit-logger` creada. |
| `A2_04_Lambda_trigger_SQS.png` | Trigger SQS configurado para ejecutar Lambda. |
| `A2_05_Lambda_event_source_mapping_SQS.png` | Mapeo de origen de eventos entre SQS y Lambda. |
| `A2_06_CloudWatch_log_group_ECS.png` | Grupo de logs de ECS disponible en CloudWatch. |
| `A2_07_CloudWatch_logs_ECS_SpringBoot.png` | Logs del microservicio ejecutándose en AWS. |
| `A2_08_Postman_login_JWT_REDACTADO.png` | Login y obtención de JWT, con token oculto. |
| `A2_09_Postman_POST_producto_API_Gateway.png` | Creación de producto desde Postman. |
| `A2_10_Postman_GET_productos_API_Gateway.png` | Consulta de productos desde Postman. |
| `A2_11_Postman_PUT_producto_API_Gateway.png` | Modificación de producto desde Postman. |
| `A2_12_Postman_DELETE_producto_API_Gateway.png` | Eliminación de producto desde Postman. |
| `A2_13_SQS_mensaje_crear.png` | Mensaje de auditoría asociado a creación. |
| `A2_14_SQS_mensaje_modificar.png` | Mensaje de auditoría asociado a modificación. |
| `A2_15_SQS_mensaje_eliminar.png` | Mensaje de auditoría asociado a eliminación. |
| `A2_16_CloudWatch_Lambda_auditoria_crear.png` | Lambda procesando auditoría de creación. |
| `A2_17_CloudWatch_Lambda_auditoria_modificar.png` | Lambda procesando auditoría de modificación. |
| `A2_18_CloudWatch_Lambda_auditoria_eliminar.png` | Lambda procesando auditoría de eliminación. |
| `A2_19_SQS_sin_mensajes_por_consumo_Lambda.png` | Cola SQS vacía luego del consumo automático por Lambda. |

**Conclusión A2:** se evidencia el flujo completo: Postman/API → microservicio ECS → RDS → SQS → Lambda → CloudWatch Logs.

---

# Actividad 3 – Exposición y Seguridad con Amazon API Gateway

Esta actividad demuestra la exposición del microservicio mediante Amazon API Gateway y el ajuste de seguridad para evitar acceso directo no autorizado a las tareas ECS.

| Evidencia | Qué demuestra |
|---|---|
| `A3_01_API_Gateway_creado.png` | API Gateway HTTP `techstore-api-gw` creada. |
| `A3_02_API_Gateway_integracion_HTTP_PROXY_ALB_CLI.png` | Integración HTTP_PROXY desde API Gateway hacia el ALB. |
| `A3_03_API_Gateway_ruta_default_y_stage_CLI.png` | Ruta `$default` y stage `prod` configurados. |
| `A3_04_Curl_API_Gateway_health_OK.png` | Prueba por CLI del endpoint `/api/health` usando API Gateway. |
| `A3_05_API_Gateway_vista_web.png` | Vista web de API Gateway configurado. |
| `A3_06_API_Gateway_rutas_integracion.png` | Rutas e integración de API Gateway verificadas desde consola. |
| `A3_07_SecurityGroup_reglas.png` | Reglas del Security Group asociadas al proyecto. |
| `A3_08_SecurityGroup_revocar_8080_publico.png` | Evidencia de eliminación del acceso público directo al puerto 8080. |
| `A3_09_SecurityGroup_verificacion_reglas.png` | Verificación de reglas de seguridad luego del ajuste. |
| `A3_10_Prueba_ALB_y_API_Gateway_OK_despues_seguridad.png` | Prueba de conectividad luego de aplicar la seguridad. |

**Conclusión A3:** el microservicio fue expuesto mediante API Gateway y se configuraron reglas de seguridad para restringir el acceso directo al puerto 8080 de las tareas ECS.

---

# Actividad 4 – Pipeline CI/CD con GitHub Actions

Esta actividad demuestra la automatización del despliegue usando GitHub Actions, construcción de imagen Docker, publicación en ECR y actualización del servicio ECS.

| Evidencia | Qué demuestra |
|---|---|
| `A4_01_GitHub_Actions_workflow_exitoso.png` | Workflow de GitHub Actions ejecutado correctamente. |
| `A4_02_ECS_servicio_activo_post_deploy.png` | Servicio ECS activo luego del despliegue. |
| `A4_03_Verificacion_CLI_servicio_post_deploy.png` | Verificación por AWS CLI posterior al despliegue. |

**Conclusión A4:** el pipeline CI/CD quedó operativo y permite desplegar la imagen actualizada hacia ECS.

---

# Evidencias sensibles redactadas

Las siguientes capturas fueron redactadas para evitar exponer datos privados:

| Archivo | Dato protegido |
|---|---|
| `A1_14_Variables_entorno_tarea_REDACTADO.png` | Contraseña de RDS, secretos y valores sensibles. |
| `A2_08_Postman_login_JWT_REDACTADO.png` | Token JWT completo. |

Esto permite entregar evidencia técnica sin revelar credenciales.

---

# Evidencias clave

Ya que la evidencias son muchas, se recomienda abrir estas capturas:

1. `A1_02_ECR_imagen_latest.png` – imagen Docker publicada en ECR.
2. `A1_07_ECS_service_running_1.png` – servicio ECS corriendo.
3. `A1_10_Target_group_destino_healthy.png` – Target Group saludable.
4. `A1_11_Health_ALB_OK.png` – endpoint por ALB funcionando.
5. `A2_01_RDS_techstore_available.png` – RDS disponible.
6. `A2_04_Lambda_trigger_SQS.png` – Lambda conectada a SQS.
7. `A2_09_Postman_POST_producto_API_Gateway.png` – prueba funcional desde Postman.
8. `A2_16_CloudWatch_Lambda_auditoria_crear.png` – Lambda registrando auditoría.
9. `A3_04_Curl_API_Gateway_health_OK.png` – API Gateway funcionando.
10. `A3_09_SecurityGroup_verificacion_reglas.png` – reglas de seguridad verificadas.
11. `A4_01_GitHub_Actions_workflow_exitoso.png` – pipeline exitoso.

---

# Conclusión

Con estas evidencias se respalda que las actividades 1, 2, 3 y 4 fueron implementadas y probadas.  

