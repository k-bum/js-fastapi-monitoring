FROM locustio/locust
COPY requirements.txt requirements.txt 
RUN pip install -r requirements.txt
WORKDIR /mnt/locust
COPY . /mnt/locust
EXPOSE 8089