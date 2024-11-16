https://istio.io/latest/docs/setup/getting-started/






Go to the Istio release page to download the installation file for your OS, or download and extract the latest release automatically (Linux or macOS):

$ curl -L https://istio.io/downloadIstio | sh -


Move to the Istio package directory. For example, if the package is istio-1.22.2:

$ cd istio-1.22.2


Add the istioctl client to your path (Linux or macOS):

$ export PATH=$PWD/bin:$PATH


INSTALL ISTIO

For this installation, we use the demo configuration profile. It’s selected to have a good set of defaults for testing, but there are other profiles for production or performance testing.

 istioctl install --set profile=demo -y

Add a namespace label to instruct Istio to automatically inject Envoy sidecar proxies when you deploy your application later:

$ kubectl label namespace default istio-injection=enabled








Deploy the Sample Appllication for Testing

Deploy the Bookinfo sample application:

$ kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml


Verify everything is working correctly up to this point. Run this command to see if the app is running inside the cluster and serving HTML pages by checking for the page title in the response:

 kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
<title>Simple Bookstore App</title>










Open the application to outside traffic

The Bookinfo application is deployed but not accessible from the outside. To make it accessible, you need to create an Istio Ingress Gateway, which maps a path to a route at the edge of your mesh.

    Associate this application with the Istio gateway:

    $ kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
    gateway.networking.istio.io/bookinfo-gateway created
    virtualservice.networking.istio.io/bookinfo created

Ensure that there are no issues with the configuration:

$ istioctl analyze
✔ No validation issues found when analyzing namespace: default.













Determining the ingress IP and ports

Follow these instructions to set the INGRESS_HOST and INGRESS_PORT variables for accessing the gateway. Use the tabs to choose the instructions for your chosen platform:


Execute the following command to determine if your Kubernetes cluster is running in an environment that supports external load balancers:

$ kubectl get svc istio-ingressgateway -n istio-system

