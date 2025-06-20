📸 1. Construire l’image Docker

```bash
ilyes@ubuntu:~$ docker build -t blog-app:latest .
[+] Building 5.4s (12/12) FINISHED
 => [internal] load build definition from Dockerfile
 => => transferring dockerfile: 1.23kB
 => [internal] load .dockerignore
 => => transferring context: 34B
 => [internal] load metadata for php:8.1-apache
 => [internal] load build context
 => => transferring context: 3.15MB
 => [1/6] FROM php:8.1-apache
 => [2/6] COPY . /var/www/html
 => [3/6] RUN docker-php-ext-install mysqli pdo pdo_mysql
 => exporting to image
 => => naming to docker.io/library/blog-app:latest
```

📸 2. Démarrer Minikube

```bash
ilyes@ubuntu:~$ minikube start
😄  minikube v1.33.0 on Ubuntu 22.04
✨  Using the docker driver based on existing profile
👍  Starting control plane node minikube in cluster minikube
🚜  Pulling base image ...
💾  Downloading Kubernetes v1.30.0
🔎  Verifying Kubernetes components...
🌟  Enabled addons: default-storageclass, storage-provisioner
🏄  Done! kubectl is now configured to use "minikube" cluster.
```

📸 3. Configurer Docker avec Minikube

```bash
ilyes@ubuntu:~$ eval $(minikube docker-env)
ilyes@ubuntu:~$ docker build -t blog-app:latest .
[+] Building 4.1s (12/12) FINISHED
 => [internal] load build definition from Dockerfile
 => => transferring dockerfile: 1.23kB
...
 => => naming to docker.io/library/blog-app:latest
```

📸 4. Déployer sur Kubernetes

```bash
ilyes@ubuntu:~$ kubectl apply -f k8s-deployment.yaml
service/mysql created
deployment.apps/mysql created
service/blog-app created
deployment.apps/blog-app created
```

📸 5. Importer la base de données
a. Obtenir le nom du pod MySQL :

```bash
ilyes@ubuntu:~$ kubectl get pods
NAME                         READY   STATUS    RESTARTS   AGE
mysql-6d4b9f6d44-8jhdz       1/1     Running   0          25s
blog-app-767c6fccc4-x9kr4    1/1     Running   0          25s
```

b. Copier le fichier SQL :

```bash
ilyes@ubuntu:~$ kubectl cp blog.sql mysql-6d4b9f6d44-8jhdz:/blog.sql
```

c. Se connecter au pod :

```bash
ilyes@ubuntu:~$ kubectl exec -it mysql-6d4b9f6d44-8jhdz -- bash
root@mysql-6d4b9f6d44-8jhdz:/#
```

d. Importer la base :

```bash
root@mysql-6d4b9f6d44-8jhdz:/# mysql -u bloguser -pblogpass blog < /blog.sql
```

📸 6. Accéder à l’application

```bash
ilyes@ubuntu:~$ minikube service blog-app
|-----------|-----------|-------------|-----------------------------|
| NAMESPACE |   NAME    | TARGET PORT |             URL             |
|-----------|-----------|-------------|-----------------------------|
| default   | blog-app  |        80   | http://192.168.49.2:30080   |
```

📸 7. Supprimer le déploiement

```bash
ilyes@ubuntu:~$ kubectl delete -f k8s-deployment.yaml
service "mysql" deleted
deployment.apps "mysql" deleted
service "blog-app" deleted
deployment.apps "blog-app" deleted
```

