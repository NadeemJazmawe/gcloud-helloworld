# gcloud-helloworld

This task about Google Cloud Platform, the mean task about:
1. Create a docker image with a python service that prints "hello world" in your computer
2. Upload that docker image to Google Cloud container registry
3. Create kubernetes cluster in Google Cloud
4. Create deployment with that docker image
5. Expose it to the world (create kubernetes service) and make sure you can see the "hello world"

In order to do this task i have used Google Cloud Platform , using the free credits for new users in Google Cloud which grant $300 for free.


## 1. Docker Image:
To create python service that print "hello world" , i have used flask library , building a basic web App that return "hello world".

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return '<h2>Hello, World!</h2>'

if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0")
```
you can run this python code and visit the web on: http://127.0.0.1:5000/
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
`  docker run -d -p 5000:5000 nadeemjazmawe/helloworld`

## 2. Google Cloud container registry
To Upload that docker image to Google Cloud container registry i have do the following steps:
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




