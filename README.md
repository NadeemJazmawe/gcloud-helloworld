# gcloud-helloworld

This task about Google Cloud Platform, the mean task about:
1. Create a docker image with a python service that prints "hello world" in your computer
2. Upload that docker image to Google Cloud container registry
3. Create kubernetes cluster in Google Cloud
4. Create deployment with that docker image
5. Expose it to the world (create kubernetes service) and make sure you can see the "hello world"

In order to do this task i have used Google Cloud Platform , using the free credits for new users in Google Cloud which grant $300 for free.


## 1. Docker Image:
To create python service that print "hello world!" , i have used flask library , building a basic web App that return "hello world".

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return '<h2>Hello, World!</h2>'

if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0")
```
You can run this python code and visit the web on: http://127.0.0.1:5000/

![Untitled](https://user-images.githubusercontent.com/44744877/183521990-4148b286-9985-44bb-8aa2-d9fb197305fe.png)


After that i have created requirements.txt file , which contain name of the libraries i have used(in my case i have used only flask library).

Also i have created Dockerfile , to build the image.
```
FROM python:3.6-alpine
WORKDIR /code
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY helloworld.py helloworld.py
EXPOSE 5000
CMD ["python", "helloworld.py"]
```

The image based on python:3.6-alpine which is a minimal Docker Official Image, installing flask library, expose port 5000 , and running the web.

After that i pushed the imgae to Dockerhub, which can be find in:

https://hub.docker.com/r/nadeemjazmawe/helloworld

Now you can run the docker image on you own computer using the following command:

`docker run -d -p 5000:5000 nadeemjazmawe/helloworld`

## 2. Google Cloud container registry
To Upload that docker image to Google Cloud container registry i did the following steps:
* Created a Google Cloud account.
* Opened a new Project 
* Activate command shell , and running the following comands
```
docker pull nadeemjazmawe/helloworld:latest
docker tag nadeemjazmawe/helloworld:latest gcr.io/PROJECT_ID/helloworld:latest
docker push gcr.io/PROJECT_ID/helloworld:latest
```

Now i have the image in my gcloud, which can seen on container registry -> images 

![Untitled1](https://user-images.githubusercontent.com/44744877/183522019-8da6e022-bfc8-492f-9474-267846f79d53.png)


## 3. Google Cloud Cluster
To create kubernetes cluster in Google Cloud , i have downloaded gcloud CLI, and did the following steps:
* Set default settings for the gcloud CLI:
	* Set the default project:
`gcloud config set project PROJECT_ID `
	* Set the default zone:
`gcloud config set compute/zone COMPUTE_ZONE`
	* Set the default region:
`gcloud config set compute/region COMPUTE_REGION`

* Create a GKE cluster, by the following command:
```
gcloud container clusters create hello-cluster \
    --num-nodes=1 \
    --zone="ENTER_COMPUTE_ZONE"
```
You can check the cluster in Kubernetes clusters

![Untitled123](https://user-images.githubusercontent.com/44744877/183534607-72eaba00-ac69-40c2-8806-6fb196aa812c.png)





## 4. Create deployment with that docker image

First , i have build the deployment using yml file(named "hello-dep.yml"):

```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: helloworld-deployment
  labels:
    app: helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: helloworld
  template:
    metadata:
      labels:
        app: helloworld
    spec:
      containers:
      - name: helloworld-web
        image: gcr.io/balmy-amp-358714/helloworld
        ports:
        - containerPort: 5000
```

In this file directory running the following command:

`kubectl apply -f hello-dep.yml`

We can check the deployment that we have created by:

`kubectl get deploy`

![Untitledasd](https://user-images.githubusercontent.com/44744877/183534123-eb39898b-1622-4dbd-b6cb-c7742d107e22.png)


Also the pod that get created by: 

`kubectl get po`

![Untitledasd](https://user-images.githubusercontent.com/44744877/183534195-60c9c578-707e-449e-8ede-5d6cac597077.png)





## 5. Expose it to the world
For this step we need to build service and connect ingress

To build the service i have created another yml file(named "hello-service.yml"):

```
apiVersion: v1
kind: Service
metadata:
  name: helloworld-service
spec:
  selector:
    app: helloworld
  type: NodePort  
  ports:
  - name: helloworld-service
    port: 5000
    targetPort: 5000
```

In this file directory running the following command:

`kubectl apply -f hello-service.yml`

We can check the services that we have by:

`kubectl get svc`

![Untitledasd](https://user-images.githubusercontent.com/44744877/183534247-56069a2b-ba37-4247-aa83-8414b649dec5.png)



Also , to connect ingress , we need to build yml file , gcloud addin the plugins we need automatically and to connect it with 

So , i have created yml file for ingress(named "hello-ingress.yaml"):

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
spec:
  rules:
  - http:
      paths:
      - path: /
        pathType: ImplementationSpecific
        backend:
          service:
            name: helloworld-service
            port:
              number: 5000
```

In this file directory running the following command:

`kubectl apply -f hello-ingress.yml`

We can check the status of ingress by:

`kubectl get ingress`

![Untitledasd](https://user-images.githubusercontent.com/44744877/183534370-a269aaee-a484-4faa-abdc-500a9d404f7f.png)


Under ADDRESS colmn we can see IP , this IP we gonna connect to using http , and we gonna see the web that we have build.

Now we can visit the following web:

http://34.160.170.159/

There is another option to buy a domain name and connect it to our system...


Thats it , Enjoy :P


