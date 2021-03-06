
#Minikube setup

I installed minikube as given in the website, by downloading the binary. Kubectl was already installed in my system. Few commands to show that minikube is installed and working as expected are:

```shell
gairik@gairik:~/Desktop/SO1/docker-mysql-spring-boot-example$ sudo minikube status
[sudo] password for gairik: 
host: Running
kubelet: Running
apiserver: Running
kubeconfig: Configured

```

### 1. Set up (in kubernetes/minikube) 2 pods with a java example app:

I ended up spending a lot of time to setup Spring-boot applciation however due to some java issues in my system I could not create a jar file. I am not very familiar with the Spring frame work thereofore I was not successful with solve the issue. 
However to go one with the problem statement I deployed a Nodejs application. The Nodejs app is very basic and I have created it from scratch and build and image and pushed to docker hub. the links are given below.

kubernetes file - nodejs.yaml

### 2. Load balance the traffic to the backends.
Services takes care of it. There is default round robin balancing created with services.

### 3. Create policy to auto-heal or recreate the pod if it goes down or is unresponsive.

Deployment takes care of it.

### 4. Add a mysql.

I created PV and PVC for mysql and the files in the repo with name
my-sql-deployment.yaml

### 5. Can you do a HA of a database? Any way to keep the data persistent when pods are recreated?


Persistent storage is used to store the data seperately. We create volumes and mount on them. So even if mysql crashes the data can be retrieved from PV. It is declared in

```shell
volumes:
      - name: task-pv-claim
        persistentVolumeClaim:
          claimName: task-pv-claim
```

### 6. Add CI to the deployment process.

I am familiar with gitlab-ci.yaml so I have created a dummy CI.
```shell

job1:
    stage: build
    image: someimage_with_kubectl
    script: 
    - kubectl apply -f nodejs.yaml
    - kubectl apply -f my-sql-deployment.yaml
    only:
     changes:
     - nodejs.yaml
     - my-sql-deployment.yaml

```



### 7. Split your deployment into prd/qa/dev environment.

Create environemnt such as prd/qa/dev and deploy on them. 

### 8. Please suggest a monitoring solution for your system. How would you notify an admin that the resources are scarce?          

This can be used by grafana or splunk or may be AWS cloudwatch. But I am not familiar with monitoring tools.


# FINAL VIEW

```shell
gairik@gairik:~/Desktop/nodejs-mysql$ kubectl get pods
NAME                                    READY   STATUS    RESTARTS   AGE
hello-minikube-797f975945-v6r8d         1/1     Running   2          31h
mysql-5fcdb49b9b-6nf8s                  1/1     Running   0          154m
simple-api                              1/1     Running   0          31s
simple-api-deployment-57c647b4c-42dnm   1/1     Running   0          30s
simple-api-deployment-57c647b4c-f4vxs   1/1     Running   0          30s

```

```shell
gairik@gairik:~/Desktop/nodejs-mysql$ kubectl get svc
NAME         TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
kubernetes   ClusterIP   10.96.0.1       <none>        443/TCP    31h
mysql        ClusterIP   None            <none>        3306/TCP   155m
simple-api   ClusterIP   10.96.230.203   <none>        8080/TCP   66m

```

```shell
gairik@gairik:~/Desktop/nodejs-mysql$ kubectl get deployments
NAME                    READY   UP-TO-DATE   AVAILABLE   AGE
hello-minikube          1/1     1            1           31h
mysql                   1/1     1            1           156m
simple-api-deployment   2/2     2            2           2m11s

```

### Docker hub
https://hub.docker.com/repository/docker/gairik/node

### Docker commands to build and push
From the folder
```shell
docker build . -t gairik/node:v1
docker push gairik/node:vq
```

### References
https://www.serverlab.ca/tutorials/development/nodejs/how-to-deploy-nodejs-applications-in-kubernetes/
https://medium.com/@gairikaluni


