# Files
* `app.py` - basic lambda function written in python (3.8)
* `requirements.txt` - additional packages for lambda function to be installed
* `entry.sh` - entrypoint to execute Lambda Runtime Interface Client for Python
* `Docker-basic` - dockerfile to build lambda function image based on AWS provided image
* `Docker-optimized` - dockerfile with multistage build based on size-optimized linux distributions
* `Docker-java` - approach to use multistage build with gradle projects written in java


# AWS provided image

1. Build docker image
```bash
docker build . -f Dockerfile-basic -t example-lambda-basic
```

2. Check image size 
```bash
docker images

REPOSITORY             TAG       IMAGE ID       CREATED         SIZE
example-lambda-basic   latest    89d8431220d8   4 seconds ago   681MB
```

3. Run the image and create a sample request to the lambda function

```bash
docker run -p 8080:8080 example-lambda-basic
curl -XPOST "http://localhost:8080/2015-03-31/functions/function/invocations" -d '{}'

"Hello from AWS Lambda using Python3.8.12 (default, Feb  3 2022, 14:50:09) \n[GCC 7.3.1 20180712 (Red Hat 7.3.1-13)]!"%
```
```
10 Mar 2022 21:37:42,789 [INFO] (rapid) exec '/var/runtime/bootstrap' (cwd=/var/task, handler=)
10 Mar 2022 21:37:09,852 [INFO] (rapid) extensionsDisabledByLayer(/opt/disable-extensions-jwigqn8j) -> stat /opt/disable-extensions-jwigqn8j: no such file or directory
10 Mar 2022 21:37:09,852 [WARNING] (rapid) Cannot list external agents error=open /opt/extensions: no such file or directory

START RequestId: 82303d1f-39be-4608-8375-cff54bedde6b Version: $LATEST
END RequestId: 82303d1f-39be-4608-8375-cff54bedde6b
REPORT RequestId: 82303d1f-39be-4608-8375-cff54bedde6b	Init Duration: 0.22 ms	Duration: 84.04 ms	Billed Duration: 85 ms	Memory Size: 3008 MB	Max Memory Used: 3008 MB

START RequestId: a02f23ec-35a6-4d0f-b277-34112dd5ab5a Version: $LATEST
END RequestId: a02f23ec-35a6-4d0f-b277-34112dd5ab5a
REPORT RequestId: a02f23ec-35a6-4d0f-b277-34112dd5ab5a	Duration: 1.44 ms	Billed Duration: 2 ms	Memory Size: 3008 MB	Max Memory Used: 3008 MB
```


# Alpine optimized image

1. Build docker image
```bash
docker build . -f Dockerfile-optimized -t example-lambda-optimized
```

2. Check image size
```bash
docker images

example-lambda-basic       latest    89d8431220d8   About an hour ago    681MB
example-lambda-optimized   latest    897a62097c96   About a minute ago   61.7MB

3. Run the image and create a sample request to the lambda function
```bash
docker run -p 9000:8080 example-lambda-optimized
```

```bash
curl -XPOST "http://localhost:9000/2015-03-31/functions/function/invocations" -d '{}'

"Hello from AWS Lambda using Python3.8.12 (default, Feb 26 2022, 00:40:51) \n[GCC 10.3.1 20211027]!"%
```
```
10 Mar 2022 21:37:32,807 [INFO] (rapid) exec '/usr/local/bin/python' (cwd=/function, handler=app.handler)
10 Mar 2022 21:37:48,535 [INFO] (rapid) extensionsDisabledByLayer(/opt/disable-extensions-jwigqn8j) -> stat /opt/disable-extensions-jwigqn8j: no such file or directory
10 Mar 2022 21:37:48,535 [WARNING] (rapid) Cannot list external agents error=open /opt/extensions: no such file or directory

START RequestId: 321dbf44-63c2-4690-bd73-68343d2598e4 Version: $LATEST
END RequestId: 321dbf44-63c2-4690-bd73-68343d2598e4
REPORT RequestId: 321dbf44-63c2-4690-bd73-68343d2598e4	Init Duration: 0.58 ms	Duration: 346.99 ms	Billed Duration: 347 ms	Memory Size: 3008 MB	Max Memory Used: 3008 MB

START RequestId: 31f6ca29-1bea-442d-8444-f9b5e8cc2e94 Version: $LATEST
END RequestId: 31f6ca29-1bea-442d-8444-f9b5e8cc2e94
REPORT RequestId: 31f6ca29-1bea-442d-8444-f9b5e8cc2e94	Duration: 1.94 ms	Billed Duration: 2 ms	Memory Size: 3008 MB	Max Memory Used: 3008 MB
```


# References
* https://docs.docker.com/develop/develop-images/multistage-build/
* https://docs.aws.amazon.com/lambda/latest/dg/python-image.html#python-image-clients
* https://docs.aws.amazon.com/lambda/latest/dg/images-test.html 
* most important: **https://aws.amazon.com/blogs/aws/new-for-aws-lambda-container-image-support/**
* https://docs.aws.amazon.com/lambda/latest/dg/runtimes-custom.html
* https://github.com/aws/aws-lambda-python-runtime-interface-client
