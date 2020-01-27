# private-helmcharts

# How to host a Private Helm Repository on Github.

If you are using the "stable" oficial repositories given by the oficial kubernetes charts storage and you want to save and create new helm charts you can do it with the help of github.

First of all we have to download a helm chart to customize it:

````
helm fetch --untar stable/traefik
````

With the option ``--untar`` helm will download the chart and will uncompress it automatically. 

Now we can customize stuff like values.yaml or Chart.yaml (change the description for example) 

````
vim ./traefik/Chart.yaml
````

It will return this: 

````
apiVersion: v1
appVersion: 1.7.20
description: A Traefik based Kubernetes ingress controller with Let's Encrypt support
engine: gotpl
home: https://traefik.io/
icon: https://docs.traefik.io/img/traefik.logo.png
keywords:
- traefik
- ingress
- acme
- letsencrypt
maintainers:
- email: kent.rancourt@microsoft.com
  name: krancour
- email: emile@vauge.com
  name: emilevauge
- email: daniel.tomcej@gmail.com
  name: dtomcej
- email: ludovic@containo.us
  name: ldez
name: traefik
sources:
- https://github.com/containous/traefik
- https://github.com/helm/charts/tree/master/stable/traefik
version: 1.85.0

````

Now do some little changes to prove that is your own customized repo!

When you ended modifying all this stuff you can tar your chart again **renember that helm waits just for .tgz files, not folders**.

````
helm package ./traefik && rm -fr ./traefik
````

Now we have to create a index for the Helm Repository, this index will add all the charts that we have inside of our repo.

````
helm repo index <path of the cloned repository>
````

Now please do the commit to your github repository.

````
git add .
git commit -m 'Helm chart and index added'
git push origin master
````

Now please check our "raw" repository link, is always like this:

````
https://raw.githubusercontent.com/<Username>/<Repo>/<Branch>
````

In my case it seems like this: 

````
https://raw.githubusercontent.com/ShankyJS/private-helmcharts/master

````

Now we are going to add the repo to our "trusted" helm remote repositories. 

````
helm repo add <name> <url-raw-repo>
````

In my case is something like:

````
helm repo add lemp https://raw.githubusercontent.com/ShankyJS/private-helmcharts/master
````
So if we do a little cat to our repositories file we can see our new origin 

````
cat ~/.helm/repository/repositories.yaml

apiVersion: v1
generated: 2020-01-04T00:23:07.670573514-06:00
repositories:

- caFile: ""
  cache: /home/shankyjs/.helm/repository/cache/lemp-index.yaml
  certFile: ""
  keyFile: ""
  name: lemp
  password: ""
  url: https://raw.githubusercontent.com/ShankyJS/private-helmcharts/master
  username: ""


````

How to test if everything is working?

````
helm search traefik
````           

lemp/traefik  	1.85.0       	1.7.20     	A Traefik based Kubernetes ingress controller with Let's ...

