# Evaluación Parcial N°1 - Orquestación de Contenedores con Kubernetes

**Asignatura:** INY1106 - Infraestructura de Aplicaciones II  
**Tiempo Asignado:** 5 horas pedagógicas  
**Ponderación:** 30% de la nota final  
**Modalidad:** Ejecución práctica individual

## Objetivo General

Implementar una infraestructura para microservicios usando tecnología de orquestación de contenedores Kubernetes, verificando la capacidad para configurar, desplegar y gestionar una infraestructura para microservicios en un entorno local y de nube pública.

## Indicadores de Logro a Evaluar

- **IL1.1:** Despliega una infraestructura basada en microservicios utilizando Kubernetes, en función de los requisitos de escalabilidad y mantenimiento de la organización.
- **IL1.2:** Optimiza el rendimiento de una infraestructura de microservicios utilizando herramientas de gestión, demostrando la capacidad de mejorar la eficiencia operativa en un entorno de producción.
- **IL1.3:** Implementa soluciones para mitigar los desafíos de seguridad inherentes a la arquitectura de microservicios, considerando la sensibilidad de los datos.

## Arquitectura a Implementar

La solución debe incluir los siguientes componentes:

- **WordPress** con 2 réplicas escalables
- **MariaDB** con almacenamiento persistente
- **phpMyAdmin** para administración de base de datos
##  Diagrama de implementación

![Diagrama de implementación]([actividades-sumativas/parcial-01/images/diagrama-infraestructura-kubernetes.png](https://github.com/Matishac14/INY1106/blob/ecb360353971652ba0e5a228ac023e9c29542c07/actividades-sumativas/parcial-01/images/diagrama-infraestructura-kubernetes.png))


### Especificaciones de Servicios

- **wordpress-service:** NodePort 30088 → Pods WordPress (puerto contenedor 8080)
- **phpmyadmin-service:** NodePort 30080 → Pod phpMyAdmin (puerto 80)
- **mariadb-service:** ClusterIP accesible solo dentro del clúster (puerto 3306)

## Etapas de Implementación

### 1. Configuración y Despliegue del Clúster de Kubernetes

#### Prerrequisitos
- Instalar `kubectl` en la máquina local
- Instalar CLI específico del proveedor de nube (aws-cli, gcloud, az-cli). En caso de ser una implementación cloud

#### Creación del Clúster
- Utilizar servicios gestionados (Amazon EKS, Google GKE, Azure AKS) o Docker Desktop
- Configurar cantidad de nodos, autoescalamiento, tipo de instancia y región (si es cloud)
- Verificar conexión mediante `kubectl`

#### Configuración de Redes y Seguridad (Solo para Cloud)
- Configurar redes del clúster con rangos IP y subredes (mínimo X.X.X.X/20)
- Implementar Network Policies para control de tráfico
- Configurar Security Groups/Firewalls/NSG para protección de nodos
- Establecer autenticación y autorización RBAC

#### Despliegue de Manifiestos
- **Deployment:** 2 réplicas, imagen del contenedor, políticas de actualización, recursos de cómputo y memoria
- **Service:** Configurar ClusterIP, NodePort o LoadBalancer según corresponda
- **Ingress:** (Si aplica) Configurar para exposición HTTP/HTTPS con cifrado en tránsito

#### Balanceadores de Carga
- Implementar balanceador para distribuir tráfico entre réplicas

### 2. Configuración de Volúmenes Persistentes

- **PersistentVolume (PV):** Conectar con servicios de almacenamiento del proveedor
- **PersistentVolumeClaim (PVC):** Solicitar almacenamiento al PV
- **Modificación de Deployments:** Montar volúmenes en rutas especificadas

### 3. Escalabilidad Horizontal y Vertical

#### Escalado Horizontal
- Ajustar manualmente el número de réplicas de los pods en el Deployment para simular un cambio en la carga de trabajo.

#### Escalado Vertical
- Ajusta los recursos asignados a los contenedores (CPU y memoria) mediante la modificación de los límites y solicitudes en el manifiesto del Deployment.

## Materiales e Insumos


- Conectividad a Internet
- Acceso a nube pública (opcional)
- Contenedores Docker públicos
- Contenedores Bitnami (MariaDB y WordPress)

## Entregables

1. **Documento paso a paso** en formato PDF o enlace a Wiki de acceso público
2. **Archivos YAML** para reproducir el despliegue realizado

## Rúbrica de Evaluación

### Criterios de Desempeño

| Nivel                    | Porcentaje | Descripción                                                                                                                                                                                       |
|--------------------------|------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Muy buen desempeño**   | 100%       | Demuestra un desempeño destacado, evidenciando el logro de todos los aspectos evaluados en el indicador.                                                                                          |
| **Buen desempeño**       | 80%        | Alto Demuestra un alto desempeño del indicador, presentando pequeñas omisiones, dificultades y/o errores.                                                                                         |
| **Desempeño aceptable**  | 60%        | Demuestra un desempeño competente, evidenciando el logro de los elementos básicos del indicador, pero con omisiones, dificultades o errores.                                                      |
| **Desempeño incipiente** | 30%        | Presenta importantes omisiones, dificultades o errores en el desempeño, que no permiten evidenciar los elementos básicos del logro del indicador, por lo que no puede ser considerado competente. |
| **Desempeño no logrado** | 0%         | Presenta ausencia o incorrecto desempeño.                                                                                                                                                         |

### Pauta de evaluación

