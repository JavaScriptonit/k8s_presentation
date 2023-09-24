# Презентация по k8s:

1. ### Что такое k8s и зачем он нужен организациям?

Kubernetes - это открытая система для автоматизации развертывания, масштабирования и управления контейнеризованными приложениями.

Чтобы понять, для чего хорошо использовать Kubernetes, рассмотрим некоторые примеры:

Вы хотите запустить определенное приложение в контейнере на нескольких различных местах. Конечно, если у вас есть 2-3 сервера/места, вы можете справиться самостоятельно, но возможностей масштабирования на дополнительные места может быть недостаточно.

Выполнение обновлений и изменений для сотен контейнеров.

Обработка случаев, когда текущая нагрузка требует масштабирования (вверх или вниз).

2. ### Архитектура k8s кластера:

Кластер\
Мастер-узел\
API-сервер\
Эталонное хранилище и конечная точка

### Контроллеры:
Контроллер планирования\
Контроллер репликации\
Контроллер управления конечной точкой\
Контроллер управления настроек конфигурации\
Рабочие узлы

### Узловые службы:
Сетевая служба\
Proxy-сервер\
Планировщик подов\
Kubelet (агент Kubernetes)\
Kube-proxy (для обеспечения сетевой связности)

3. ### Что такое k8s кластер?

Кластер состоит из Мастера (который координирует работу кластера) и Узлов, на которых запускаются приложения.

4. ### Пример команды для создания развёртывания:
```
kubectl create deployment kubernetes-httpd --image=httpd
```
Команда  создает развертывание (deployment) с именем "kubernetes-httpd" и использует образ "httpd".

`kubectl`: утилита командной строки Kubernetes.\
`create deployment`: команда для создания развертывания.\
`kubernetes-httpd`: это имя, которое вы выбираете для вашего развертывания. Вы можете выбрать любое уникальное имя для своего развертывания.\
`--image=httpd`: опция указывает образ, который будет использоваться для создания контейнера в вашем развертывании. В данном случае, указывается образ httpd, который является веб-сервером Apache HTTP Server.

Таким образом, команда создает развертывание с именем "kubernetes-httpd", которое будет запускать контейнер на основе образа "httpd".

5. ### За что отвечает master?

Мастер координирует все рабочие процессы в кластере:

Планирование приложений\
Управление желаемым состоянием\
Развертывание обновлений

6. ### Как проверить состояние и работу мастера в k8s?

Проверить, что мастер-компоненты запущены и работают:\
`kubectl get componentstatuses`\
Проверить состояние узлов в кластере:\
`kubectl get nodes`\
Проверить состояние развертываний и подов:\
`kubectl get deployments`\
`kubectl get pods`\
Создание развертывания (deployment) с именем "my-deployment" и 3 репликами:\
`kubectl create deployment my-deployment --image=my-image --replicas=3`\
Масштабирование развертывания до 5 реплик:\
`kubectl scale deployment my-deployment --replicas=5`\
Обновление образа для развертывания:\
`kubectl set image deployment/my-deployment my-container=my-new-image`\
Просмотр логов пода:\
`kubectl logs my-pod`

7. ### Компоненты k8s:

`kubectl` - это утилита командной строки для работы с Kubernetes API-сервером, который является основной частью мастера Kubernetes.

`scheduler`, `controller-manager` и `etcd-0` - это компоненты мастера Kubernetes:

`scheduler` отвечает за планирование (размещение) подов на доступные узлы в кластере в соответствии с заданными требованиями и ограничениями.\
`controller-manager` управляет различными контроллерами, которые обеспечивают желаемое состояние кластера (например, контроллер репликации, контроллер конечной точки и другие).\
`etcd` представляет собой распределенное хранилище, используемое для сохранения и синхронизации состояния кластера Kubernetes.

```
docker exec -it minikube /bin/sh
docker ps

e6b6aad92a6d 115053965e86 "/metrics-sidecar" Up 3 days k8s_dashboard-metrics-scraper
5849db300377 deb04688c4a3 "kube-apiserver --ad…" Up 3 days k8s_kube-apiserver
3c8f7d15de97 46a6bb3c77ce "/usr/local/bin/kube…" Up 3 days k8s_kube-proxy
1ad890efd424 e9c08e11b07f "kube-controller-man…" Up 3 days k8s_kube-controller-manager
01f9cb94c715 fce326961ae2 "etcd --advertise-cl…" Up 3 days k8s_etcd_etcd
b10bfdb5744d 655493523f60 "kube-scheduler --au…" Up 3 days k8s_kube-scheduler
862a594e1b92 registry.k8s.io/pause:3.6 "/pause" Up 3 days k8s_POD_coredns
```
Выведенные контейнеры через docker ps внутри оболочки minikube, представляют компоненты мастера Kubernetes. У каждого компонента есть свой уникальный идентификатор контейнера (например, k8s_kube-apiserver, k8s_kube-controller-manager, k8s_etcd_etcd и т. д.). Они представляют основные сервисы, которые работают на мастер-узле и обеспечивают функционирование кластера Kubernetes.

Таким образом, перечисленные контейнеры являются компонентами мастера Kubernetes, которые управляют планированием, управлением и хранением состояния кластера.

8. ### Что такое Node (Узел)?

Узел - это виртуальная машина или физический сервер, который служит воркером для запуска приложений. Рекомендуется иметь по крайней мере 3 узла в производственной среде Kubernetes.

Каждый узел (Node) в кластере Kubernetes имеет свои вычислительные ресурсы (процессор, память, хранилище) и предоставляет возможность запускать и поддерживать контейнеры.
Чтобы посмотреть список узлов (Nodes), вы можете использовать команду:
```
kubectl get nodes
```

Количество узлов, которые могут быть в кластере, зависит от ограничений ресурсов на сервере. Если сервер имеет 10 ГБ памяти и 10 ядер, то может быть развернуто несколько узлов в кластере. Окончательное количество узлов зависит от конфигурации и использования ресурсов каждым узлом.

9. ### Что такое Kubelet?

Kubelet - это основной компонент Node (агент), который работает на каждом узле Kubernetes. Kubelet отвечает за управление жизненным циклом контейнеров на узле. Отвечает за связь между узлом и мастером.

Он следит за состоянием подов, запускает контейнеры в соответствии с заданными спецификациями, реагирует на команды API-сервера и отчеты о состоянии узла.
У Kubelet есть несколько аналогов, которые выполняют схожие функции. Например, Docker Daemon в связке с Docker Swarm работает в режиме Swarm-Agent и выполняет некоторые функции, схожие с Kubelet.

10. ### Взаимодействие с Kubelet:

Чтобы проверить состояние `kubelet` на узле Kubernetes, вы можете использовать следующую команду systemctl на узле:
```
systemctl status kubelet
```
Эта команда покажет текущее состояние `kubelet` сервиса на узле. Если сервис работает нормально, вы должны увидеть сообщение "active (running)" или подобное.

Вы также можете выполнить команду `ps` на узле для проверки, выполняется ли процесс `kubelet`:
```
ps aux | grep kubelet
```
Если процесс kubelet выполняется, вы увидите выходные данные соответствующего процесса.

11. ### Что такое minikube?

`Minikube` - это легкая реализация Kubernetes. Он создает локальную виртуальную машину и разворачивает простой (одноузловый) кластер.

С помощью определенных настроек и расширений, таких как `minikube start --nodes`, вы можете добавить дополнительные узлы в Minikube. Дополнительные узлы будут запущены на отдельных виртуальных машинах, что позволяет создавать более сложные кластеры.

Основные отличия между Minikube и реальным кластером Kubernetes следующие:

* Размер и масштаб: Minikube разворачивает только одну виртуальную машину и предоставляет ограниченные возможности для развертывания и управления контейнерами и приложениями, в то время как реальный кластер Kubernetes может включать в себя десятки, сотни или даже тысячи узлов.
* Распределение: Minikube работает локально, на вашей машине, и не поддерживает горизонтальное масштабирование. Реальный кластер Kubernetes обычно распределен на несколько узлов в физическом или виртуальном окружении, что позволяет обеспечить отказоустойчивость и масштабируемость системы.
* Управление: Управление Minikube происходит через командную строку и предлагает упрощенный интерфейс для работы с кластером. В реальном кластере Kubernetes, управление происходит через Kubernetes API, и доступны дополнительные методы управления и конфигурации, такие как использование инструмента kubectl.

12. ### K8s pod:

В Kubernetes "под" (pod) представляет собой самую маленькую и базовую единицу развертывания. Он является логическим оберткой, которая объединяет один или несколько контейнеров и предоставляет им совместную среду исполнения (общую сеть и хранилище).
Основные отличия между подами (под в Kubernetes) и контейнерами Docker следующие:

* Под содержит один или несколько контейнеров, работающих вместе на одном узле.
* Контейнеры в поде делят ту же сеть, пространство имен и даже память, что позволяет им общаться друг с другом локально через localhost.
* Под управляется Kubernetes и располагается внутри узла Kubernetes, идентифицированного своим уникальным именем.

Многоконтейнерные поды полезны в следующих случаях:

* Когда несколько контейнеров должны работать в тесной связи и совместно выполнять задачу, например, веб-сервер и побочный контейнер для обработки логов.
* Когда несколько контейнеров должны разделить общие ресурсы или использовать общие тома данных, например, почтовый сервер и контейнер с базой данных.
* Когда в одном поде требуется добавить вспомогательные контейнеры для выполнения различных служебных задач, например, контейнер для запуска backup'ов или мониторинга.

Многоконтейнерные поды в Kubernetes поддерживают организацию и управление группой контейнеров, которые должны работать вместе в рамках одной целостной задачи или службы.

13. ### Мониторинг k8s кластера:

Существует несколько способов мониторинга Kubernetes, и лучший метод зависит от ваших требований и предпочтений. Некоторые популярные инструменты для мониторинга Kubernetes включают:

* `Prometheus`: популярный инструмент мониторинга с открытым исходным кодом, который предоставляет гибкую систему сбора и анализа метрик Kubernetes.
* `Grafana`: инструмент визуализации данных с открытым исходным кодом, который может работать вместе с Prometheus для создания красивых и информативных графиков и дашбордов.
* `Datadog`: облачная служба мониторинга и анализа приложений, предоставляющая широкий спектр функций для мониторинга Kubernetes, включая сбор метрик, трассировку и анализ журналов.
* `ELK Stack (Elasticsearch, Logstash, Kibana)`: комбинация инструментов для сбора, анализа и визуализации данных логов Kubernetes и приложений.

Некоторые аналоги мониторинга Kubernetes включают:

* `Rancher`: управление Kubernetes и инструменты мониторинга в едином решении.
* `OpenShift`: платформа контейнерного приложения, предоставляющая собственные функции мониторинга и управления Kubernetes.
* `Azure Kubernetes Service (AKS)`: управляемый сервис Kubernetes от Azure, предоставляющий встроенные возможности мониторинга и отладки.

Можно установить агенты мониторинга на все узлы кластера Kubernetes для сбора метрик и передачи их в инстану. Например, вы можете использовать `Prometheus Node Exporter`, который является агентом с открытым исходным кодом, предназначенным для сбора системных метрик с узлов Kubernetes. Агенты могут отправлять метрики в инстану или любой другой сервер мониторинга, который вы выбрали.

`Node Exporter` - это инструмент с открытым исходным кодом, разработанный для сбора системных метрик с узлов Kubernetes. Вы можете установить Node Exporter на все узлы в кластере и настроить передачу метрик в Grafana или другой инструмент визуализации, такой как Prometheus.

В экосистеме `Helm` существует множество готовых решений для деплоя мониторинговых инструментов в кластер Kubernetes. Например, Prometheus Operator предоставляет Helm-чарты, которые упрощают развертывание и управление инфраструктурой сбора метрик. Кроме того, существуют готовые Helm-чарты для установки Grafana, ELK Stack и других мониторинговых компонентов. Вы можете использовать команду `helm search` в Helm для поиска доступных решений и выбора подходящего для ваших потребностей.