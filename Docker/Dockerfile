FROM python:3.6-alpine

WORKDIR /code

COPY requirements.txt requirements.txt

RUN pip install -r requirements.txt

COPY helloworld.py helloworld.py

EXPOSE 5000

CMD ["python", "helloworld.py"]