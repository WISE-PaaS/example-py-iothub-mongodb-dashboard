
# WISE-PaaS example-python-Mongodb & MQTT Data Worker

This example can show you how to use WISE-PaaS Mongodb and Rabbitmq and we can make a device connection application

## STEP 1:Prepare Environment

cf-cli

[https://docs.cloudfoundry.org/cf-cli/install-go-cli.html](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html?source=post_page---------------------------)

python3

[https://www.python.org/downloads/](https://www.python.org/downloads/?source=post_page---------------------------)

![](https://cdn-images-1.medium.com/max/2000/1*iJwh3dROjmveF8x1rC6zag.png)

## STEP 2:How to use Rabbitmq(MQTT)

This article only tell you the quick set up，go to download this article

[https://github.com/WISE-PaaS/example-python-iothub-sso](https://github.com/WISE-PaaS/example-python-iothub-sso)

First，we need to use cf to login to our WISE-PaaS ，if you can’t login you need to check our your domain is wise-paas.ioor wise-paas.com 。

    #cf login -skip-ssl-validation -a {api.domain_name}  -u "account" -p "password"
    
    cf login –skip-ssl-validation -a api.wise-paas.io -u xxxxx@advtech.com.tw -p xxxxxx
    
    #check the cf status
    cf target

Open the manifest.yml and **edit** the application name to yours，because the application name can't duplicate。

open templates/index.html

    #change this **`python-demo-jimmy`** to your **application name**
    var ssoUrl = myUrl.replace('python-demo-jimmy', 'portal-sso');

When we login，we need to push our application to the WISE-PaaS

    #cf push {application name}
    cf push python-demo-try

and we need to get the application environment save it to env.json

    #cf env {application name} > env.json

Edit the publisher.py broker、port、username、password you can find in env.json

* bokrer:”VCAP_SERVICES => p-rabbitmq => externalHosts”

* port :”VCAP_SERVICES => p-rabbitmq => mqtt => port”

* username :”VCAP_SERVICES => p-rabbitmq => mqtt => username”

* password: “VCAP_SERVICES => p-rabbitmq => mqtt => password”

open two terminal

    #cf logs {application name}
    cf logs python-demo-try

.

    python publish.py

![](https://cdn-images-1.medium.com/max/2466/1*WzwjNwVA7QMZRJn7bGH27Q.png)

## Step 3:MongoDB setup

We want to use flask_pymongothis library to implement our MongoDB application，first we need to add this library to our requirements file，this file can help buildpack to install library and compile 。

open the requirements.txt and add the flask_pymongo

<iframe src="https://medium.com/media/597fed8b058aed05fd67132df3d06343" frameborder=0></iframe>

and we need to bind our service instance in WISE-PaaS，first we need to open our WISE-PaaS/EnSaaS

![](https://cdn-images-1.medium.com/max/2542/1*U4IMFUoNtaUguhkytLdiwQ.png)

the mongodb service instance already exist Service Instance List，or you can add by yourself(add button => mongodb => save =>instance name) the service name maybe different。

open our manifest.yml we need to add the mongodbinstance name to our file，change the memory and disk_quota to 256M。

<iframe src="https://medium.com/media/12d55e61fe68a56cf018a16a1c1415a7" frameborder=0></iframe>

Now，we can editor our index.py，add MongoDB to our code，we also need to change the on_message function than save data to the MongoDB。

<iframe src="https://medium.com/media/3c315d90228f1fa72052006dd23035f2" frameborder=0></iframe>

Because we already bind the MongoDB Service Instance so we can use the os.getenv to get the application environment in WISE-PaaS， uri is our database location，and we use two router /temp (get all data) /insert (insert data) can help we debug。

Now，we can push it。

(you need to check the service name in `index.py` and WISE-PaaS)
![https://github.com/WISE-PaaS/example-python-iohtub-mongodb/blob/master/source/service-name.PNG](https://github.com/WISE-PaaS/example-python-iohtub-mongodb/blob/master/source/service-name.PNG)
![https://github.com/WISE-PaaS/example-python-iohtub-mongodb/blob/master/source/code_image.PNG](https://github.com/WISE-PaaS/example-python-iohtub-mongodb/blob/master/source/code_image.PNG)

    cf push python-demo-mongodb

We also change the publisher.py to send a random data 。

<iframe src="https://medium.com/media/e99d30c129cf563ed20c940c9a0763ad" frameborder=0></iframe>

![](https://cdn-images-1.medium.com/max/2000/1*t9Ctvi9lAXX9p88vIhp7Fw.png)

![](https://cdn-images-1.medium.com/max/2308/1*tsTGvjO9UiohvPqtNEczfg.png)
