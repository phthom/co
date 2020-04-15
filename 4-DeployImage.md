# Building and Pushing a Docker Image



On your laptop :



## Install Docker

Install Docker on your laptop

## Login to OpenShift

Get your token (see lab 2)

```
oc login --token=<token> --server=https://c107-e.us-south.containers.cloud.ibm.com:30322
```

Results:

```
 oc login --token=fydw5xNNarSjQ7_UyHF6auUpe8phNncPVExJfXT08-o --server=https://c107-e.us-south.containers.cloud.ibm.com:30322
Logged into "https://c107-e.us-south.containers.cloud.ibm.com:30322" as "IAM#ultrasmartboy77@gmail.com" using the token provided.

You have access to 97 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "labproj30".

```

Go to your project:

``` 
oc project labprojxx
```



## Login to the OpenShift Registry



Get the Registry route:

```
HOST=$(oc get route default-route -n openshift-image-registry --template='{{ .spec.host }}')
echo $HOST
default-route-openshift-image-registry.niceam-ba36b2ed0b6b09dbc627b56ceec2f2a4-0000.us-south.containers.appdomain.cloud

```

Login to the registry

``` 
docker login $HOST -u openshift -p $(oc whoami -t) 
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Go to to your Application directory

```
cd stage1
ls
drwxr-xr-x  7 phil  staff   224 Oct  9  2019 .
drwxr-xr-x  9 phil  staff   288 Oct 16 14:08 ..
-rw-r--r--  1 phil  staff    27 Oct  9  2019 .dockerignore
-rw-r--r--@ 1 phil  staff   382 Oct  9  2019 Dockerfile
-rw-r--r--  1 phil  staff  2890 Apr  4  2017 README.md
-rw-r--r--@ 1 phil  staff   275 Oct  9  2019 app.js
-rw-r--r--@ 1 phil  staff   265 Oct  9  2019 package.json
```



## Build your image

```
docker build -t $HOST/adminproj/appcc:1.0 .

...
Removing intermediate container 8c2a85af9e53
 ---> c65974ad3b67
Step 5/7 : COPY . .
 ---> fdfddc4707e6
Step 6/7 : EXPOSE 8080
 ---> Running in 779d553b56ac
Removing intermediate container 779d553b56ac
 ---> b0523c9bc024
Step 7/7 : CMD [ "node", "server.js" ]
 ---> Running in c8d428f260ef
Removing intermediate container c8d428f260ef
 ---> ec6c1242a91d
Successfully built ec6c1242a91d
Successfully tagged default-route-openshift-image-registry.niceam-ba36b2ed0b6b09dbc627b56ceec2f2a4-0000.us-south.containers.appdomain.cloud/adminproj/appcc:1.0

```

Where appcc is the name of your image.

Look at your image stored locally

``` docker 
docker images
```

## Push your image 

``` 
docker push default-route-openshift-image-registry.niceam-ba36b2ed0b6b09dbc627b56ceec2f2a4-0000.us-south.containers.appdomain.cloud/adminproj/appcc:1.0

The push refers to repository [default-route-openshift-image-registry.niceam-ba36b2ed0b6b09dbc627b56ceec2f2a4-0000.us-south.containers.appdomain.cloud/adminproj/appcc]
ba376f68972f: Pushed 
f76767b8decc: Pushed 
ea0ef32e0275: Pushed 
a18db61c80fd: Pushed 
...
588545a7a2a3: Pushed 
8452468a5e50: Pushed 
55b19a5e648f: Pushed 
1.0: digest: sha256:eb48345959ad070b21bd9f6f5f43af5b3d29a10833e5bd2352b09e097972e77f size: 3048

```

## Deploy your image in the cluster

```
oc new-app   --docker-image="$HOST/labprojxx/appcc:1.0"  --name=appcc

W0414 19:08:23.381201   34119 newapp.go:482] Could not find an image stream match for "default-route-openshift-image-registry.niceam-ba36b2ed0b6b09dbc627b56ceec2f2a4-0000.us-south.containers.appdomain.cloud/adminproj/appcc:1.0". Make sure that a Docker image with that tag is available on the node for the deployment to succeed.
--> Found container image ec6c124 (2 hours old) from default-route-openshift-image-registry.niceam-ba36b2ed0b6b09dbc627b56ceec2f2a4-0000.us-south.containers.appdomain.cloud for "default-route-openshift-image-registry.niceam-ba36b2ed0b6b09dbc627b56ceec2f2a4-0000.us-south.containers.appdomain.cloud/adminproj/appcc:1.0"

    * This image will be deployed in deployment config "appcc"
    * Port 8080/tcp will be load balanced by service "appcc"
      * Other containers can access this service through the hostname "appcc"
    * WARNING: Image "default-route-openshift-image-registry.niceam-ba36b2ed0b6b09dbc627b56ceec2f2a4-0000.us-south.containers.appdomain.cloud/adminproj/appcc:1.0" runs as the 'root' user which may not be permitted by your cluster administrator

--> Creating resources ...
    deploymentconfig.apps.openshift.io "appcc" created
    service "appcc" created
--> Success
    Application is not exposed. You can expose services to the outside world by executing one or more of the commands below:
     'oc expose svc/appcc' 
    Run 'oc status' to view your app.

```

## Get the route

```
oc get routes -n labprojxx 
```



