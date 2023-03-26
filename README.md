# Toshikx_platform
Toshikx Platform repository

# Выполнено ДЗ № 1

 - [X] Основное ДЗ
 - [X] Задание со *
<details>
<summary>Просмотр ДЗ </summary>

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
</details>

# Выполнено ДЗ № 2

 - [X] Основное ДЗ
 - [X] Задание со *
<details>
<summary>Просмотр ДЗ </summary>

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
</details>

# Выполнено ДЗ № 3

 - [X] Основное ДЗ
 - [X] Задание со *
<details>
<summary>Просмотр ДЗ </summary>

## В процессе сделано:
 - Добавлены проверки на Pod
 - Созданы следующие файлы манифестов: 
    - metallb-config.yaml - файл конфигурации сервиса metallb;
    - nginx-lb.yaml - Сервис типа LoadBalancer, распределяющий трафик для  Ingress Nginx контроллера;
    - web-deploy.yaml - Deployment сервиса web из первого домашнего задания с исправными readiness & liveness probe;
    - web-ingress.yaml - Ingress для сервиса web;
    - web-svc-cip.yaml - Service с типом CluterIP для сервиса web;
    - web-svc-headless.yaml - Headless Service сервиса web;
    - web-svc-lb.yaml - Service для сервиса web типа LoadBalancer.
 - Созданы файлы манифестов для заданий со *:
    - ./canary/canary-ing.yaml - Ingress с аннотациями для переадресации трафика на новые Pods при canary deploy;
    - ./coredns/core-dns-lb.yaml - Service типа LoadBalancer для доступа к Kube-DNS вне кластера;
    - ./dashboard/dashboard-ing.yaml - Ingress для доступа к сервису Kubernetes Dashboard по /dashboard пути адресной строки.
 - Подготовлен ответ на вопрос:
    - Вопрос: 
      Почему следующая конфигурация валидна, но не имеет смысла?
      ```yaml
      livenessProbe:
        exec:
          command:
            - 'sh'
            - '-c'
            - 'ps aux | grep my_web_server_process'
      ```
        - Ответ: Такая команда позволит только увидеть, что процесс запущен, но не даёт информации о его работспособности
    - Вопрос: Бывают ли ситуации, когда она все-таки имеет смысл?
        - Ответ: Например в случае, если ошибка конфигурации не позволяет запустить процесс приложения
## Как запустить проект:
- Необходимо склонировать себе репозиторий и перейти в ветку kubernetes-intro: 
  ```sh
  git clone https://github.com/otus-kuber-2023-02/Toshikx_platform.git
  cd Toshikx_platform
  git checkout -b kubernetes-network
  cd kubernetes-network
  ```
- Нужен предварительно настроенный кластер и доступ к нему при помощи kubectl на основе minikube: 
  ```sh
  minikube start
  ```
- Применить манифест Deployment для сервиса web:
  ```sh
  kubectl apply -f web-deploy.yaml
  ```
- Применить манифест web-svc-cip.yaml:
  ```sh
  kubectl apply -f web-svc-cip.yaml
  ```
- Для работы следующей части задания необходимо выполнить конфигурацию IPVS, для этого необходимо выполнить следующее:
  ```sh
  kubectl --namespace kube-system edit configmap/kube-proxy
  ```
  В открывшемся редакторе найти и заменить поля mode и strictARP на следующие значения:
  ```yaml
  ...
  ipvs:
    strictARP: true
  mode: "ipvs"
  ...
  ```
  После чего необходимо перезапустить под kube-proxy:
  ```sh
  kubectl --namespace kube-system delete pod --selector='k8s-app=kube-proxy'
  ```

  Очистка правил iptables:
    ```sh
    touch /tmp/iptables.cleanup
    ```
    Добавить в созданный файл следующие правила:
    ```sh
    *nat
    -A POSTROUTING -s 172.17.0.0/16 ! -o docker0 -j MASQUERADE
    COMMIT
    *filter
    COMMIT
    *mangle
    COMMIT
    ```
    Применить конфигурацию:
    ```sh
    iptables-restore /tmp/iptables.cleanup
    ```
    Проверка результатов после ожидания 30 секунд:
    ```sh
    iptables --list -nv -t nat
    ```
- Установка metallb:
  - Для установки metallb версии 0.13.9 (актуальная информация об установке [здесь](https://metallb.org/installation/)) выполнить следующую команду:
    ```sh
    kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.13.9/config/manifests/metallb-native.yaml
    ```
  - Применение файла конфигурации:
    ```sh
    kubectl apply -f metallb-config.yaml
    ```
  - Добавление статического маршрута:
    ```sh
    minikube ssh
    ip addr show eth0 
    ```
    В выводе второй команды нужен inet адресс <br />
    *sample:*
    ```sh
    inet 192.168.64.4/24 brd 192.168.64.255 scope global dynamic eth0
    ```
    И добавить адресс в основной ОС на IP-адресс Minikube (*Linux sample*):
    ```sh
    ip route add 172.17.255.0/24 via 192.168.64.4
    ```
- Создание сервиса Web типа LoadBalancer:
  ```sh
  kubectl apply -f web-svc-lb.yaml
  ```
- Создание сервиса DNS типа LoadBalancer после конфигурирования MetalLB:
  ```sh
  kubectl apply -f ./coredns/core-dns-lb.yaml
  ```
- Для следующей части задания нужен NGINX Ingress Controller:
  - Установка: <br />
    Примените манифест из [актуальной]() версии:
    ```sh
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.6.4/deploy/static/provider/cloud/deploy.yaml
    ```
  - Конфигурирование: <br />
    Для успешной работы ingress-controller в связке с нашим MetalLB необходимо предварительно удалить сервис, поставляемый NGINX:
    ```sh
    kubectl delete svc ingress-nginx-controller -n ingress-nginx
    ```
    После чего, применить наш собственный сервис:
    ```sh
    kubectl apply -f nginx-lb 
    ```
- Применение манифеста headless сервиса web:
  ```sh
  kubectl apply -f web-svc-headless.yaml
  ```
- Применение манифеста Ingress сервиса web:
  ```sh
  kubectl apply -f web-ingress.yaml
  ```
- Применение манифеста для dashboard: <br />
  Прежде чем применять манифест самого Ingress, необходимо получить работающий сервис, актуальная версия [здесь](https://github.com/kubernetes/dashboard): 
  ```sh
  kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
  ```
  После чего можно применять манифест Ingress:
  ```sh
  kubectl apply -f ./dashboard/dashboard-ing.yaml
  ```
- Применение манифеста ingress с канареечным развёртывыанием:
  ```sh
  kubectl apply -f ./canary/canary-ing.yaml
  ```
## Как проверить работоспособность:
### Как проверить работоспособность Web Deployment
 - Дождаться запуска трёх Pod:
   ```sh 
   watch kubectl get po web
   ```
 - Проверить условия Available и Progressing для Deploymetn
   ```sh
   kubectl describe deployment web
   ```
 - Проверить условия Conditions у конкретной Pod:
   ```sh
   kubectl get po 
   kubectl describe po *pod_name*
   ```
### Как проверить работоспособность Web Service ClusterIP
  - Проверить наличие сервиса с нужным названием и типом Type:
    ```sh
    kubectl get svc web-svc-cip
    ```
  - Можно дополнительно проверить доступность сервиса на ноде:
    ```sh
    minikube ssh
    curl http://<CLUSTER-IP>/index.html
    iptables --list -nv -t nat
    ```
    *Примечание, ping \<CLUSTER-IP> работать не будет*
### Как проверить работоспособность IPVS
  - Перейти в виртуальную машину minikube:
    ```sh
    minikube ssh
    ```
  - Выполнить ping сервиса с адресом \<CLUSTER-IP>:
    ```sh
    ping <CLUSTER-IP>
    ```
  - Проверка сервиса на интерфейсе kube-ipvs0:
    ```sh
    ip addr show kube-ipvs0
    ```
### Проверка работы сервисов MetalLB
  - Проверить работоспособность компонентов:
    ```sh
    kubectl --namespace metallb-system get all
    ```
  - После применения web-svc-lb.yaml проверить логи пода-контроллера MetalLB:
    ```sh
    kubectl --namespace metallb-system logs pod/controller-XXXXXXXX-XXXXXX
    ```
  - Посмотреть назначенный IP-адресс для сервиса:
    ```sh
    kubectl describe svc web-svc-lb
    ```
  - Перейти по адресу сервиса LodBalancer в браузере и получить веб-страницу
### Проверка работы внешнего DNS 
  - Проверить адресс сервиса через наш внешний DNS:
    ```sh
    nslookup web-svc-lb.default.svc.cluster.local 172.17.255.2
    ```
### Проверка работоспособности Ingress-Controller
  - Чтобы проверить работоспособность нашего сервиса, необходимо получить его внешний адрес:
    ```sh
    kubectl get svc -n ingress-nginx
    ```
    После чего можно в браузере перейти по этому адресу, или выполнить curl:
    ```sh
    curl <EXTERNAL-IP>
    ```
    В любом случае при успешной работе мы получим страницу с 404 ошибкой и указанием на версию nginx
### Проверка работоспособности правил Ingress
  - После применения манифестов сервиса и Ingress правила, необходимо получить внешний адрес для подключения:
    ```sh
    kubectl describe ingress/web
    ```
  Теперь можно проверить, что страница доступна в бразуере или при помощи curl адреса http://\<LB_IP>/web/index.html
### Проверка работоспособности dashboard
  - Получить внешний адресс dasboard:
    ```sh
    kubectl get ing -n kubernetes-dashboard
    ```
    Перейти по адресу в браузере по адресу https://<ING-IP>/dashboard/#/login
### Проерка работоспособности canary deployment
  - Получить внешний адресс Ingress:
    ```sh
    kubectl get ing/web
    ```
  - Изменить образ и выполнить RolloutRestart Deployment:
    ```sh
    kubectl rollout restart deployment web
    ```
  - В браузере 30% от общего числа запросов пойдёт на новую версию Deployment
## PR checklist:
 - [x] Выставлен label с темой домашнего задания
</details>

# Выполнено ДЗ № 4

 - [X] Основное ДЗ
 - [X] Задание со *
<details>
<summary>Просмотр ДЗ </summary>

## В процессе сделано:
 - Проверена работа StatefulSet в кластере Kubernets;
 - Созданы следующие файлы манифестов: 
    - secret.yaml - Хранение секретов для переменных окружения;


## Как запустить проект:
- Необходимо склонировать себе репозиторий и перейти в ветку kubernetes-intro: 
  ```sh
  git clone https://github.com/otus-kuber-2023-02/Toshikx_platform.git
  cd Toshikx_platform
  git checkout -b kubernetes-volumes
  cd kubernetes-volumes
  ```
- Нужен предварительно настроенный кластер и доступ к нему при помощи kubectl на основе kind: 
  ```sh
  kind cluster create
  ```
- Применить манифесты в следующем порядке:
  ```sh
  kuectl apply -f secret.yaml
  kubectl apply -f minio-headless-service.yaml
  kubectl apply -f statefullset.yaml
  ```
## Как проверить работоспособность:
### Как проверить работоспособность
 - Проверить наличие StatefulSet:
   ```sh
   kubectl get statefulset/minio
   ```
 - Проверить наличие нужного нам PV:
   ```sh 
   kubectl get pv 
   ```
 - Проверить наличие созданного автоматически PVC:
   ```sh
   kubectl get pvc/data-minio-0
   ```
 - Проверить состояние Pod:
   ```sh
   kubectl get po/minio-0
   ```
 - Проверить, что данные в переменные окружения идут из Secrets:
   ```sh
   kubectl describe po/minio-0
   ```
   *В разделе Environment будет описано, что данные засекречены*
## PR checklist:
 - [x] Выставлен label с темой домашнего задания
</details>

# Выполнено ДЗ № 5

 - [X] Основное ДЗ
<details>
<summary>Просмотр ДЗ </summary>

## В процессе сделано:
 - Созданы следующие файлы манифестов: 
    - ./task01/bob.yaml - Service Account bob с ролью admin в рамках кластера;
    - ./task01/dave.yaml - Service Account dave без доступа к кластеру;
    - ./task02/carol.yaml - Создание Namespace "prometheus", Service Account carol, а также доступ всем Service Account Namespace prometheus для чтения Pods всего кластера;
    - ./task03/jane.yaml - Создание Namespace "dev", Service Account janne с ролью admin на Namespace "dev";
    - ./task03/ken.yaml - Service Account ken с ролью view на Namespace "dev".


## Как запустить проект:
- Необходимо склонировать себе репозиторий и перейти в ветку kubernetes-intro: 
  ```sh
  git clone https://github.com/otus-kuber-2023-02/Toshikx_platform.git
  cd Toshikx_platform
  git checkout -b kubernetes-security
  cd kubernetes-security
  ```
- Нужен предварительно настроенный кластер и доступ к нему при помощи kubectl на основе kind: 
  ```sh
  kind cluster create
  ```
- Применить манифесты в следующем порядке:
  ```sh
  kubectl apply -f task01
  kubectl apply -f task02
  kubectl apply -f task03
  ```
## Как проверить работоспособность:
### Как проверить работоспособность задание 1
 - Проверить наличие Service Account:
   ```sh
   kubectl get serviceaccounts | grep -a "dave\|bob"
   ```
 - Проверить наличие ClusterRoleBindings:
   ```sh
   kubectl get clusterrolebindings.rbac.authorization.k8s.io | grep -a "dave\|bob"
   ```
 - Проверить выданные права для Service Account dave:
   ```sh
   kubectl describe clusterrole admin-dave
   ```
 - Проверить описание созданных ClusterRoleBindings:
   ```sh
   kubectl describe clusterrolebindings.rbac.authorization.k8s.io bob-admin-cluster
   kubectl describe clusterrolebindings.rbac.authorization.k8s.io dave-admin-cluster
   ```
### Как проверить работоспособность задание 2
 - Проверить наличие Namespace:
   ```sh
   kubectl get ns | grep prometheus
   ```
 - Проверить наличие Service Account:
    ```sh
    kubectl get serviceaccounts -n prometheus | grep "carol"
    ```
 - Проверить наличие ClusterRole с правами pod-reader
   ```sh
   kubectl get clusterrole | grep pod-reader
   ```
 - Проверить выданные права для ClusterRole:
   ```sh
   kubectl describe clusterrole pod-reader
   ```
 - Проверить наличие ClusterRoleBindings:
   ```sh
   kubectl describe clusterrolebindings cluster-prometheus-ns-reader
   ```
 - Проверить описание созданных ClusterRoleBindings:
   ```sh
   kubectl describe clusterrolebindings.rbac.authorization.k8s.io cluster-prometheus-ns-reader
   ```
### Как проверить работоспособность задание 3
 - Проверить наличие Namespace:
   ```sh
   kubectl get ns | grep dev
   ```
 - Проверить наличие Service Account:
    ```sh
    kubectl get serviceaccounts -n prometheus | grep -a "jane|\ken"
    ```
 - Проверить наличие RoleBinding:
   ```sh
   kubectl get rolebinding -n dev | grep -a "jane|\ken"
   ```
 - Проверить наличие описание прав RoleBinding jane:
   ```sh
   kubectl describe rolebinding jane-admin-namespace -n dev
   ```
 - Проверить наличие описание прав RoleBinding ken:
   ```sh
   kubectl describe rolebinding ken-admin-namespace -n dev
   ```
## PR checklist:
 - [x] Выставлен label с темой домашнего задания
</details>