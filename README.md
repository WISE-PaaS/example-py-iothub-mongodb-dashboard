###### Example-python-Iothub-MongoDB

This is WIES-PaaS example-code include the mongodb and rabbitmq service。


[cf-introduce](https://advantech.wistia.com/medias/ll0ov3ce9e)

[IotHub](https://advantech.wistia.com/medias/up3q2vxvn3)

[Dashboard](https://advantech.wistia.com/medias/bpvxpuvnk4)

[graph](https://advantech.wistia.com/medias/hluoy8qdz3)

### Quick Start

## Environment Prepare

cf-cli

[CF-CLI](https://docs.cloudfoundry.org/cf-cli/install-go-cli.html?source=post_page---------------------------)

python3

[Python3](https://www.python.org/downloads/?source=post_page---------------------------)

![](https://cdn-images-1.medium.com/max/2000/1*iJwh3dROjmveF8x1rC6zag.png)

python3 package(those library you can try application in local):

    #mqtt
    pip3 install paho-mqtt
    #python-backend
    pip3 install Flask
    #python mongodb library
    pip3 install flask_pymongo

## Download this file

    git clone this respository

![Imgur](https://i.imgur.com/JNJmxFy.png)

## Login to WISE-PaaS

    #cf login -a api.{domain name} -u {WISE-PaaS/EnSaaS account} -p {WISE-PaaS/EnSaaS password}
    cf login -a api.wise-paas.io -u xxxxx@advantech.com -p xxxxxxxx

    #check the cf status
    cf target

open the **`manifest.yml`** and editor the application name to yours，because the appication can't duplicate in same domain。
check the service instance name in **manifest.yml** and **WISE-PaaS**

![Imgur](https://i.imgur.com/2A2HDzz.png)

**manifest.yml** can bind the service to our application in WISE-PaaS 

![Imgur](https://i.imgur.com/VVMcYO8.png)

### SSO setting(yo can ignore because it is a tool help design a login mechanism)

open **`templates/index.html`**

    #change this **python-demo-jimmy** to your **application name**
    var ssoUrl = myUrl.replace('python-demo-jimmy', 'portal-sso');

## Service name in Index.js

we need to check out the service name in `index.py` & `WISE-PaaS` because the name maybe different


![Imgur](https://i.imgur.com/6777rmg.png)

![https://github.com/WISE-PaaS/example-python-iohtub-mongodb/blob/master/source/code_image.PNG](https://github.com/WISE-PaaS/example-python-iohtub-mongodb/blob/master/source/code_image.PNG)

## Push our application to WISE-PaaS

    #cf push {application name}
    cf push python-demo-mongodb

## Get the application environment in WISE-PaaS

    #get the application environment
    cf env {application name} > env.json

Edit the **publisher.py** `broker、port、username、password` you can find in env.json

- bokrer:"VCAP_SERVICES => p-rabbitmq => externalHosts"
- port :"VCAP_SERVICES => p-rabbitmq => mqtt => port"
- username :"VCAP_SERVICES => p-rabbitmq => mqtt => username"
- password: "VCAP_SERVICES => p-rabbitmq => mqtt => password"

open two terminal

    #cf logs {application name}
    cf logs python-demo-mongodb

.

    python publisher.py

![https://github.com/WISE-PaaS/example-python-iohtub-mongodb/blob/master/source/ALREADY.PNG](https://github.com/WISE-PaaS/example-python-iohtub-mongodb/blob/master/source/ALREADY.PNG)

#### Result

You can check out the data use Robo 3T

[ROBO 3T](https://robomongo.org/download)

Let's create a new connect

(File=> connect => Create )

![Imgur](https://i.imgur.com/HDJPVOT.png)

Edit those config you need to go back to **WISE-PaaS applicaton List** and find your applcation environment

![Imgur](https://i.imgur.com/PHxUFrr.png)

`Authentication`

- Databace: "VCAP_SERVICES => mongodb-innoworks => credentials => database"
- User Name: "VCAP_SERVICES => mongodb-innoworks => credentials => username"
- Password: "VCAP_SERVICES => mongodb-innoworks => credentials => password"

`Connection`

- Name : Define by yourself
- Address : "VCAP_SERVICES => mongodb-innoworks => external_host"

![Imgur](https://i.imgur.com/XsKH6xG.png)

## Open your Dashboard in Application List

_Notice: If you create your own service instance your nedd to bind it to Dashboard first_

We need to add datasource first。
(Configuration => Data sources => Add Datasource => choose the "MongoDB")

![Imgur](https://i.imgur.com/L0xB7S5.png)

We need to setup a proxy server use the other application use this link

[https://github.com/WISE-PaaS/dashboard-mongo-datasource-example](https://github.com/WISE-PaaS/dashboard-mongo-datasource-example)

![Imgur](https://i.imgur.com/h7LGX6T.png)

![Imgur](https://i.imgur.com/vLvw0AO.png)

- URL: The proxy server application link we push
- MongoDB URL: "VCAP_SERVICES => credentials => uri"

  **(Notict:The host you need to change it to external_host)**
  **(Notice:The url behind the MongoDB you need to add the `?replicaSet=name`)**
  _you can get the replicaSet name in environment_

- replicaSet ="VCAP_SERVICES => credentials => replicaSetName"

- MongoDB Database:"VCAP_SERVICES => credebtials => database"

![Imgur](https://i.imgur.com/6uljbeJ.png)

![Imgur](https://i.imgur.com/vfdAjpe.png)

#### Add panel

Create => Dashboard => choose "Graph"

![Imgur](https://i.imgur.com/MYHUkyz.png)

panel Title => Editor

![Imgur](https://i.imgur.com/aTkKw5t.png)

    db.temps.aggregate([
    { "$match" : { "date" : { "$gte" : "$from", "$lt" : "$to" }}},
    { "$sort":{"date":1 }},
    { "$project" : { "name" : "$topic", "value" : "$data", "ts" : "$date", "_id" : 0 } }
    ])

- \$match: The Dashboard need the "date" format to match the "timestamp"，so our code in `index.js` use the datetime library to send the time。

- \$sort: Sort the "date"

- \$project: match the Dashboard
