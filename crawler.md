# using scrapy in docker
## precondition
make sure that Docker and MongoDB have been installed.

* install Docker ref to this : https://github.com/codeyuguo/devops/blob/master/dev-env.md
* install MongoDB ref to this : https://github.com/codeyuguo/devops/blob/master/db.md

## git clone the scrapy project

```
git clone git@github.com:codeyuguo/crawler.git
cd crawler
```
## create Dockerfile
**create the requirements.txt:**

```
vim requirements.txt
```
```
scrapy>=1.4.0
pymongo>=3.4.0
```
**create the Dockerfile:**
```
vim Dockerfile
```
```
FROM python:3.6
ENV PATH /usr/local/bin:$PATH
ADD . /code
WORKDIR /code
RUN pip3 install -r requirements.txt
CMD scrapy crawl quotes
```
## alter the MongDB address
......do something........
## build the Docker image
```
docker build -t quotes:latest .
```

** the way to check the build result: **

```
docker images
```
## run the image of scrapy
```
docker run qutotes
```
