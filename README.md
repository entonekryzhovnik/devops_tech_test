# devops_tech_test

# README

- **Развернуть minikube**

Я воспользовалась офицальной документацией. Поставила kubectl и minikube через brew.

```jsx
brew install kubectl
brew install minikube
```

Чтобы к minikube можно было обращаться из любой директории дополнительно пропишем:

```jsx
sudo mv minikube /usr/local/bin
```

Minikube в качестве драйвера будет использовать docker. 

```jsx
minikube start --vm-driver=docker
```

![Ура!!! Всё работает!!!!!](Screenshot_2024-04-27_at_10.53.49_AM.png)

Ура!!! Всё работает!!!!!

- **Написать Helm чарт, который устанавливает последнюю версию. Nginx должен быть доступен по порту 32080 на ноде Kubernetes.**

Helm я также поставила через Homebrew командой: 

```jsx
brew install helm
```

Я создала Helm чарт с помощью стандартной команды и далее внесла изменения в некоторые файлы.

```jsx
helm create my-nginx
```

 В файле values изменила тип соединения и задала нужный нам порт.

```jsx
service:
  type: NodePort
  port: 32080
  nodePort: 32080
```

В файле templates/service.yaml добавила строчка nodePort

```jsx
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http
      nodePort: {{ .Values.service.nodePort }}
      protocol: TCP
      name: http
```

- **Развернуть Jenkins в Minikube**

Чтобы развернуть Jenkins нужно применить все yaml файл из папки Jenkins

`kubectl apply -f serviceAccount.yaml`

`kubectl create -f volume.yaml`

`kubectl apply -f deployment.yaml`

`kubectl apply -f service.yaml`

Поскольку Minikube работает на драйвере Docker, то мы не можем обратиться к нашему сервису напрямую через <http://<minikube-ip>:32000>, но мы можем запустить сервис командой `minikube service jenkins-service` туннелирование настроиться автоматически, и мы попадем на страницу логина в Jenkins. 

![Снимок экрана 2024-05-01 в 19.20.16.png](%25D0%25A1%25D0%25BD%25D0%25B8%25D0%25BC%25D0%25BE%25D0%25BA_%25D1%258D%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D0%25B0_2024-05-01_%25D0%25B2_19.20.16.png)

Вводим логин admin, чтобы узнать пароль нужно ввести команду: 

`kubectl get pods` 

Далее используя имя пода, узнаем пароль для входа в Jenkins Dashboard

`kubectl exec -it  <pod_name> cat /var/jenkins_home/secrets/initialAdminPassword`

Пароль должен выглядеть примерно так: 71fec557e9614f5783257b8f6ce92e19

Устанавливаем все необходимые плагины.

![Ура!!! Jenkins!!!!](%25D0%25A1%25D0%25BD%25D0%25B8%25D0%25BC%25D0%25BE%25D0%25BA_%25D1%258D%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D0%25B0_2024-05-01_%25D0%25B2_19.36.28.png)

Ура!!! Jenkins!!!!

Жмем Create a Job и выбираем Pipeline. Листаем ниже, выбираем в качестве scm  Git, вставляем ссылку на репозиторий, выбираем ветку main и жмем сохранить. 

![Снимок экрана 2024-05-01 в 19.38.32.png](2eda0a1b-4e79-48f5-8eea-1dcc6f6004c5.png)

![Снимок экрана 2024-05-01 в 21.07.04.png](%25D0%25A1%25D0%25BD%25D0%25B8%25D0%25BC%25D0%25BE%25D0%25BA_%25D1%258D%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D0%25B0_2024-05-01_%25D0%25B2_21.07.04.png)

Финишная прямая! Жмем “Собрать сейчас“

![Снимок экрана 2024-05-01 в 21.08.16.png](%25D0%25A1%25D0%25BD%25D0%25B8%25D0%25BC%25D0%25BE%25D0%25BA_%25D1%258D%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D0%25B0_2024-05-01_%25D0%25B2_21.08.16.png)

Как только все успешно собралось, открываем терминал и пишем 

`minikube service my-nginx`

![Снимок экрана 2024-05-01 в 21.14.43.png](%25D0%25A1%25D0%25BD%25D0%25B8%25D0%25BC%25D0%25BE%25D0%25BA_%25D1%258D%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D0%25B0_2024-05-01_%25D0%25B2_21.14.43.png)

Все успешно! :)
