# Evaluación Parcial N°1 - Orquestación de Contenedores con Kubernetes

**Asignatura:** INY1106 - Infraestructura de Aplicaciones II

**Sección:** 001V

**Integrante:** Matías Fernández Z.

**Docente:** Oscar Rivera

---

## _Descripción del proyecto_

**Proyecto:** WordPress + MariaDB + phpMyAdmin en Kubernetes  

**Namespace:** `dev-k8s`

**Plataforma:** Minikube (Entorno Local en Docker Desktop)  

**Fecha:** 14 Septiembre 2025


---

## Componentes principales

| Servicio       | Imagen                  | Puerto contenedor | Puerto expuesto | Función                        | Escalabilidad |
|----------------|-------------------------|-------------------|-----------------|--------------------------------|---------------|
| **MariaDB**    | bitnami/mariadb:latest  | 3306              | 3306            | Base de Datos                  | 1 réplica     |
| **phpMyAdmin** | phpmyadmin:latest       | 80                | 30080           | Aplicación web principal       | Escalable     |
| **WordPress**  | bitnami/wordpress:latest| 8080              | 30088           | Administrador de Base de Datos | 1 réplica     |

---

## _Diagrama de implementación_

![Diagrama de implementación](https://github.com/Matishac14/INY1106/blob/parcial-01-pruebas/actividades-sumativas/parcial-01/images/diagrama-infraestructura-kubernetes.png)

----

## Estructura de archivos

```
actividades-sumativas
├── README.md
└── parcial-01
    ├── 00-namespace
    │   └── namespace.yaml
    ├── 01-secrets
    │   └── database-credentials.yaml
    ├── 02-storage
    │   ├── mariadb-storage.yaml
    │   └── wordpress-storage.yaml
    ├── 03-deployments
    │   ├── mariadb-deployment.yaml
    │   ├── phpmyadmin-deployment.yaml
    │   └── wordpress-deployment.yaml
    ├── 03-services
    │   ├── mariadb-service.yaml
    │   ├── phpmyadmin-service.yaml
    │   └── wordpress-service.yaml
    └── images
    │   └── diagrama-infraestructura-kubernetes.png
    └── wiki.md
```

---

## Configuración detallada

### 1. Namespace y Secrets
*   **namespace**: Proporcion almacenamiento y organización de recursos.
*   **secrets**: Almacena credenciales sensibles codificadas en base64
    * `mariadb-root-password`: Contraseña del usuario root de MariaDB
    * `mariadb-user-password`: Contraseña del usuario de aplicación
    * `mariadb-user`: Nombre del usuario de aplicación
    
### 2. Almacenamiento persistente

**MariaDB PV/PVC:**
- Capacidad: 5Gi
- Modo de acceso: ReadWriteOnce
- Ruta local: `/mnt/data/mariadb` (hostPath en Minikube)

**WordPress PV/PVC:**
- Capacidad: 5Gi
- Modo de acceso: ReadWriteOnce
- Ruta local: `/mnt/data/wordpress` (hostPath en Minikube)

### 3. Escalabilidad

#### Escalabilidad vertical

*   Todos los contenedores tienen límites de recursos configurados:

```yaml
resources:
  requests:
    cpu: "250m"      # CPU mínima garantizada
    memory: "256Mi"  # Memoria mínima garantizada
  limits:
    cpu: "500m"      # CPU máxima permitida
    memory: "512Mi"  # Memoria máxima permitida
```
#### Escalabilidad horizontal

*   El balanceador de carga se implementa automáticamente en el Service de WordPress:

```yaml
# 08-wordpress-service.yaml
selector:
  app: wordpress  # Busca TODOS los pods con esta etiqueta
type: NodePort    # Expone el servicio externamente
```
**¿Cómo funciona?**

el selector filtra que pods supervisa y controla el deployment, en este caso todos los que tengan la etiqueta de wordpress

---

## Variables de entorno

### MariaDB (Bitnami)
**Fuente imagen:** [Docker Hub - bitnami/mariadb](https://hub.docker.com/r/bitnami/mariadb)

**Fuente variables:** [GitHub - bitnami/mariadb](https://github.com/bitnami/containers/tree/main/bitnami/mariadb)

Variables utilizadas:
- `MARIADB_ROOT_PASSWORD`: Contraseña del usuario root
- `MARIADB_DATABASE`: Nombre de la base de datos (wordpress)
- `MARIADB_USER`: Usuario de aplicación
- `MARIADB_PASSWORD`: Contraseña del usuario de aplicación

### WordPress (Bitnami)
**Fuente:** [Docker Hub - bitnami/wordpress](https://hub.docker.com/r/bitnami/wordpress)

**Fuente variables:** [GitHub - bitnami/wordpress](https://github.com/bitnami/containers/tree/main/bitnami/wordpress)

Variables utilizadas:
- `WORDPRESS_DATABASE_HOST`: Host de la base de datos
- `WORDPRESS_DATABASE_PORT_NUMBER`: Puerto de conexión (3306)
- `WORDPRESS_DATABASE_USER`: Usuario para conectar a la BD
- `WORDPRESS_DATABASE_PASSWORD`: Contraseña del usuario
- `WORDPRESS_DATABASE_NAME`: Nombre de la base de datos

### phpMyAdmin (Oficial)
**Fuente:** [Docker Hub - phpmyadmin](https://hub.docker.com/_/phpmyadmin)

Variables utilizadas:
- `PMA_HOST`: Dirección del servidor de base de datos
- `PMA_PORT`: Puerto del servidor (3306)

---

## Comandos de despliegue

### Levantar Minikube

```bash
minikube start
```

### Aplicar manifiestos

```bash
kubectl apply -f 00-namespace/namespace.yaml
kubectl apply -f 01-secrets/database-credentials.yaml
kubectl apply -f 02-storage/
kubectl apply -f 03-deployments/
kubectl apply -f 04-services/
```

## Comandos de validación

### Verificación de pods, services, deployments, replicasets, persistent volume, persistent volume claim y secrets

```bash
kubectl get all -n dev-k8s
kubectl get pv,pvc -n dev-k8s
kubectl get secrets -n dev-k8s
```

## Comandos de acceso a servicos

### Wordpress

```bash
minikube service wordpress -n dev-k8s --url
```

### phpMyAdmin

```bash
minikube service phpmyadmin -n dev-k8s --url
```

## Verificación de escalabilidad

### Escalabiliad vertical

* **Modificación se hace manual**

### Escalabilidad Horizontal

WordPress está configurado para escalar fácilmente:

```bash
kubectl scale deployment wordpress --replicas=3 -n dev-k8s
kubectl get pods -n dev-k8s -l app=wordpress
```
### Eliminación de ambiente de prueba

```bash
kubectl delete all --all -n dev-k8s
kubectl delete pvc --all -n dev-k8s
kubectl delete pv --all
kubectl delete secret --all -n dev-k8s
```
### Detener Minikube

```bash
minikube stop
```
