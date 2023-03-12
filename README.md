# Toshikx_platform
Toshikx Platform repository

# Выполнено ДЗ № 1

 - [X] Основное ДЗ
 - [X] Задание со *

## В процессе сделано:
 - Изучены способы восстановления контейнеров 
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
 - [ ] Выставлен label с темой домашнего задания