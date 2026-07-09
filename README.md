# AUY1104-K8s-Lab — Plantillas Reutilizables (GitHub Actions)

Repositorio de **workflows reutilizables** (`workflow_call`) usados durante el semestre de AUY1104 (Ciclo de Vida del Software II), incluyendo la plantilla que resuelve la Evaluación Final Transversal.

## Proyecto que usa estas plantillas

La documentación técnica completa (arquitectura, estrategia de despliegue, mecanismo de rollback, evidencia de pruebas) está en el repositorio del proyecto:

👉 **[valentina-ochoa/-MiProyecto-App](https://github.com/valentina-ochoa/-MiProyecto-App)**

Ese repo contiene el código fuente, los manifiestos de Kubernetes y el workflow "caller" (`client.yaml`) que invoca las plantillas de este repositorio.

## Plantilla principal — `deploy-api.yaml`

Workflow reutilizable que ejecuta el ciclo completo de CI/CD para la API:

1. **`deps-and-test`** — instala dependencias y corre los tests (Jest/Supertest).
2. **`build-and-push`** — construye la imagen Docker y la sube a Docker Hub.
3. **`deploy-to-k8s`** — despliega en el clúster K3s con estrategia **Blue-Green**: actualiza solo la versión `green`, valida su salud (`/health`) antes de exponerla, y si la validación falla, ejecuta **rollback automático** (revierte el Service a `blue` y hace `rollout undo`).

Se invoca vía `workflow_call` con inputs parametrizables: `image-name`, `image-tag`, `k3s-server-public-ip`, `environment`, `namespace`, `env_vars`. Esto permite reutilizar la misma plantilla para distintos proyectos o entornos sin duplicar código.

## Otros workflows en este repositorio

Los siguientes workflows corresponden a entregas anteriores del semestre (EA2/EA3) y se mantienen como historial de trabajo, no forman parte del pipeline actual de la EFT:

- `ea2-lab-dispatch-main.yaml` — ejercicio de despliegue manual desde el alumno (EA2).
- `ea3-comandos-manuales.yaml` — comandos manuales de k3s practicados en EA3.
- `autorizar-llave.yaml` — utilidad para autorizar llaves SSH personales en el laboratorio.
- `holamundo.yaml` — plantilla de validación básica (template de prueba).

## Infraestructura

Este proyecto usa **K3s sobre EC2 (AWS Academy Learner Lab)** como equivalente funcional de Amazon EKS, y **Docker Hub** como equivalente de Amazon ECR — ambos implementan las mismas APIs estándar (Kubernetes y registries OCI, respectivamente), por lo que toda la lógica de despliegue es igualmente válida.








