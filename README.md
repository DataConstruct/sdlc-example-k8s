# sdlc-example-k8s
This repo is a custom K8S setup of the [eShopOnContainers](https://github.com/dotnet-architecture/eShopOnContainers) .NET Core microservices example. All of the container images have been prebuilt and live under the Github Organization [DataConstruct](https://github.com/DataConstruct). Filter on "sdlc-example".

# setup
IN THEORY, you should be able to run each K8S config and have a working application start up. I've removed the SSL configuration options. You can modify this repo by following this [guide](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nginx-ingress-with-cert-manager-on-digitalocean-kubernetes) and updating the relevant ingress controllers. The directories and files are numbered for the specific order that they should be followed. You can `kubectl apply -f FILENAME.yml` all files. The relevant namespaces have been properly configured.

This example should work, IN THEORY, with minikube. You may need to do a few additional items.

- Add the NGINX backed Ingress Controllers by applying all configurations in order in `1-init`
- Run `kubectl get svc --namespace=ingress-nginx` and grab the `EXTERNAL-IP`. This will be the external IP that you'll want for DNS.
- Add the common infrastructure that is used by the microservices by applying all configurations in order in `2-common-infra`. This will create a namespace called `common-infra` and setup MS SQL, Mongo, Redis, RabbitMQ and Redis. Note that these containers aren't backed by specific volumes which means that all data is lost if the container is restarted. This was done deliberatly as I didn't really care about the data.
- Update all instances of `YOUR_HOST_HERE.com`, in the files under `3-apps`, to whatever you'd like. It can be an actual domain you own or just work with some local hosts configs. You'll be creating 3 DNS entries:  `eshop-webshoppingapigw.YOUR_HOST_HERE.com` (an API gateway), `eshop-identity.YOUR_HOST_HERE.com` (the SSO/Identity endpoint), `eshop.YOUR_HOST_HERE.com` (the actual web app) all pointing to the same `EXTERNAL-IP` that you previously noted.
- Add all the applications by all configurations in order in `3-apps`. This will create namespaces for each application and then generate a service and deployment (in certain instances, ingress controllers) within that namespace for the application. The namespace has the same name as the application by convention.
- Open a browser and navigate to `eshop.YOUR_HOST_HERE.com`. It should load the app!