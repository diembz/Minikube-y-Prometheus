Guía Práctica: Desplegar y Monitorear una Aplicación en Kubernetes con Prometheus y Grafana

Este repositorio contiene los manifiestos y pasos necesarios para desplegar una aplicación en un clúster de Kubernetes usando Minikube, y monitorearla con Prometheus y Grafana.
Índice

    Requisitos previos
    Iniciar el clúster con Minikube
    Desplegar la aplicación de ejemplo
    Configurar Prometheus
    Configurar Grafana
    Errores comunes y soluciones
    Reiniciar después de apagar la PC

1. Requisitos previos

Antes de comenzar, asegúrate de tener instalado lo siguiente:

    Minikube
    kubectl
    Docker

2. Iniciar el clúster con Minikube

Inicia Minikube para crear tu clúster local:

minikube start

Verifica que el clúster esté funcionando:

kubectl get nodes

3. Desplegar la aplicación de ejemplo
Aplicar los manifiestos

    Crea el namespace:

kubectl apply -f namespace.yaml

Despliega la aplicación y su Service:

kubectl apply -f app-deployment.yaml
kubectl apply -f app-service.yaml

Verifica que los pods y servicios estén en ejecución:

    kubectl get pods -n monitoring-app
    kubectl get svc -n monitoring-app

4. Configurar Prometheus
Aplicar los manifiestos

    Configura el ConfigMap, Deployment y Service de Prometheus:

kubectl apply -f prometheus-configmap.yaml
kubectl apply -f prometheus-deployment.yaml
kubectl apply -f prometheus-service.yaml

Verifica el estado de los pods y el Service:

    kubectl get pods -n monitoring-app
    kubectl get svc -n monitoring-app

5. Configurar Grafana
Aplicar los manifiestos

    Despliega Grafana y su Service:

kubectl apply -f grafana-deployment.yaml
kubectl apply -f grafana-service.yaml

Verifica que Grafana esté corriendo:

    kubectl get pods -n monitoring-app
    kubectl get svc -n monitoring-app

6. Errores comunes y soluciones
1. Error: ImagePullBackOff en la aplicación

    Causa: El nombre de la imagen es incorrecto o no existe.
    Solución:
        Verifica que la imagen sea válida (usamos prom/pushgateway).
        Reaplica el manifiesto con la imagen corregida:

        kubectl apply -f app-deployment.yaml

2. Error: context deadline exceeded en Prometheus

    Causa: El Service apunta a un puerto incorrecto.
    Solución:
        Verifica el puerto en el Service (app-service.yaml):

ports:
  - port: 9091
    targetPort: 9091

Reaplica el manifiesto:

        kubectl apply -f app-service.yaml

3. Error: Prometheus no puede acceder a los targets

    Causa: Configuración incorrecta en prometheus-configmap.yaml.
    Solución:

        Verifica que el target de Prometheus coincida con el nombre del Service y puerto:

static_configs:
  - targets: ['example-app-service.monitoring-app.svc.cluster.local:9091']

Reaplica el ConfigMap:

kubectl apply -f prometheus-configmap.yaml

Reinicia el Deployment de Prometheus:

        kubectl rollout restart deployment prometheus -n monitoring-app

7. Reiniciar después de apagar la PC

Cuando vuelvas a encender tu PC, realiza los siguientes pasos:
1. Inicia Minikube

minikube start

2. Verifica los pods y servicios

Asegúrate de que los pods estén en Running:

kubectl get pods -n monitoring-app
kubectl get svc -n monitoring-app

3. Acceder a Prometheus

Haz un port-forward al Service de Prometheus:

kubectl port-forward svc/prometheus-service 9090:9090 -n monitoring-app

Accede a Prometheus en tu navegador:

http://localhost:9090

4. Acceder a Grafana

Haz un port-forward al Service de Grafana:

kubectl port-forward svc/grafana-service 3000:3000 -n monitoring-app

Accede a Grafana en tu navegador:

http://localhost:3000

    Usuario: admin
    Contraseña: admin (por defecto).

