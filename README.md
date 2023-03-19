# Toshikx_platform
Toshikx Platform repository

# Выполнено ДЗ № 1

 - [X] Основное ДЗ
 - [X] Задание со *

## В процессе сделано:
 - Изучены способы восстановления контейнеров:
   Различные причины запуска контейнеров в namespace kube-system:
    - Конетейнеры kube-proxy - контролируютя DaemonSet, а значит kube-controller-manager должен поднимать pods на каждом узле кластера
    - Контейнер core-dns создаются посредством контроллера репликации ReplicaSet, и работает по принципу того, что должно быть не менее X заявленных pods 
    - Контейнеры etcd, kube-apiserver, kube-controller-manager и kube-scheduler нужны для работы кластера и контроллируются непосредственно демоном kubelet на controle-plane ноде
 - Создан Dockerfile с веб-сервоером на основе nginx, который отображает файлы из корня /app
 - Создан web-pod.yaml манифест для запуска nginx сервера из предыдущего пунка с добавлеием контейнера инициализации Pod
 - Создан frontend-pod-healthy.yaml манифест, который запускает frontend сервис Hipster Shop (Pod в статусе Running)

## Как запустить проект:
 - Нужен предварительно настроенный кластер и доступ к нему при помощи kubectl, например на основе minikube: 
   ```sh
   minikube start 
   ```
 - Необходимо склонировать себе репозиторий и перейти в ветку kubernetes-intro: 
   ```sh
   git clone https://github.com/otus-kuber-2023-02/Toshikx_platform.git
   cd Toshikx_platform
   git checkout -b kubernetes-intro
   ```
 - Применить манифест web-pod.yaml и frontend-pod-healthy.yaml:
   ```sh
   kubectl apply -f ./kubernetes-intro/web-pod.yaml
   kubectl apply -f ./kubernetes-intro/frontend-pod-healthy.yaml
   ```
## Как проверить работоспособность:
### Как проверить работоспособность web-pod.yaml
 - Дождаться запуска Pod:
   ```sh 
   watch kubectl get po web
   ```
 - Выполнить port-forwarding: 
   ```sh
   kubectl port-forward --address 0.0.0.0 pod/web 8000:8000
   ```
 - Перейти по адреус http://localhost:8000/index.html
### Как проверить работоспособность frontend
  - Выполнить команду и проверить статус Pod:
    ```
    kubectl get po frontend
    ```

## PR checklist:
 - [x] Выставлен label с темой домашнего задания

# Выполнено ДЗ № 2

 - [X] Основное ДЗ
 - [X] Задание со *

## В процессе сделано:
 - Созданы следующие файлы манифестов: 
    - kind-config.yaml - конфигурация кластера kind 
    - frontend-replicaset.yaml - контроллер репликации ReplicaSet для сервиса frontend 
    - frontend-deployment.yaml - Deployment сервиса frontend с использованием readinessProbe
    - paymentservice-replicaset.yaml - контроллер репликации ReplicaSet для сервиса paymetnService 
    - paymetnservice-deployment.yaml - Deployment сервиса paymetnService
    - paymentservice-deployment-bg.yaml - Deployment настроенный в качестве аналога blue-green развёртывания 
    - paymentservice-deployment-reverse.yaml - Deployment настроенный в качестве reverse rolling update 
    - node-exporter-daemonset.yaml - DaemonSet node-exporter с игнорированием tolerations на мастер-ноде
 - Подготовлен ответ на вопрос:
    - Вопрос: 
      Руководствуясь материалами лекции опишите произошедшую ситуацию,
      почему обновление ReplicaSet не повлекло обновление запущенных pod?
    - Ответ: Контроллер репликации следит за количеством подов. Т.е. когда мы обновили контроллер, он проверил, что поды этого типа меток уже существуют в нужном количестве и не стал ничего с ними делать, не смотря на разницу в тегах образов
## Как запустить проект:
- Необходимо склонировать себе репозиторий и перейти в ветку kubernetes-intro: 
  ```sh
  git clone https://github.com/otus-kuber-2023-02/Toshikx_platform.git
  cd Toshikx_platform
  git checkout -b kubernetes-controllers
  cd kubernetes-controllers
  ```
- Нужен предварительно настроенный кластер и доступ к нему при помощи kubectl на основе kind: 
  ```sh
  kind create cluster --config kind-config.yaml  
  ```
- Применить манифест ReplicaSet для frontend:
  ```sh
  kubectl apply -f frontend-replicaset.yaml
  ```
- Применить манифест paymentservice-deploymant.yaml:
  ```sh
  kubectl apply -f paymentservice-deployment.yaml
  ```
- Применить манифест paymentservice-deployment-reverse.yaml:
  ```sh
  kubectl apply -f paymetnservice-deployment-reverse.yaml
  ```
- Применить манифест frontend-deploymetn.yaml:
  ```sh
  kubectl apply -f frontend-deployment.yaml
  ```
- Применить манифест node exporter:
  ```sh
  kubectl apply -f node-exporter-daemonset.yaml
  ```
## Как проверить работоспособность:
### Как проверить работоспособность frontend-replicaset.yaml
 - Дождаться запуска трёх Pod:
   ```sh 
   watch kubectl get po frontend
   ```
### Как проверить работоспособность paymentService
  - Дождаться запуска под, контроллируемых Deployment:
    ```sh
    watch kubectl get po paymentservice
    ```
### Как проверить работоспособность paymentService BlueGreen
  - Дождаться запуска под, контроллируемых Deployment:
    ```sh
    watch kuebctl get po paymetnservice
    ```
  - Выполнить rollout restart deployment для проверки работы системы развёртывания:
    ```sh
    kubectl rollout restart deployment paymentservice | watch kubectl get po 
    ```
### Как проверить работоспособность paymentService Reverse Rolling Update 
  - Дождаться запуска под, контроллируемых Deployment:
    ```sh
    watch kuebctl get po paymetnservice
    ```
  - Выполнить rollout restart deployment для проверки работы системы развёртывания:
    ```sh
    kubectl rollout restart deployment paymentservice | watch kubectl get po 
    ```
### Как проверить работоспособность frontendService с применёнными readinessProbe
  - Просмотр запуска под:
    ```sh
    watch kuebctl get po
    ```
  - Проверка наличия проб:
    ```sh
    kubectl describe pod *podName*
    ```
### Как проверить работоспособность nodeExporter
  - Просмотр запуска под:
    ```sh
    watch kuebctl get po -n monitoring
    ```
  - Форвардинг портов для проверки работоспособности метрик:
    ```sh
    kubectl port-forward *podName* 9100:9100 -n monitoring
    curl localhost:9100/metrics
    ```
  - Проверка, что есть пода на мастер ноде 
    ```sh
    kubectl get po -n monitoring -o wide
    ```

## PR checklist:
 - [x] Выставлен label с темой домашнего задания