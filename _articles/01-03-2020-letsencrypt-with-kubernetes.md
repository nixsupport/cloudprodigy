---
layout: article
title: Configure LetsEncrypt with Kubernetes
featured_img: configure-letsencrypt-with-kubernetes.jpg
description: "Setup auto generation of SSL certificates using LetsEncrypt, cert-manager and Kubernetes"
---
<br>
<h2 style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;color:#fed136;text-align:center"> Configure LetsEncrypt with Kubernetes</h2>
<br>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
As more and more solutions are built using microservices architecture, it is very important to have all your public endpoints encrypted. The good news is that you can achieve it without spending any additional penny. LetsEncrypt is one such project which is a free and open Certificate Authority and you can easily integrate it with your setup to automatically generate SSL certificates free of cost, FOREVER...
<br>
In this article I'll explain how you can setup LetsEncrypt with cert-manager on Kubernetes. I've used Route53 for this demonstration and <a href="https://cert-manager.io/docs/">cert-manager</a> version 0.12. This process is applicable for any flavor of Kubernetes be it bare-metal, EKS, GKE or AKS. I'll be using ACME issuer with DNS validation method. Let's dig in.
<br>
<br>
Please ensure you have following setup properly configured before working on LetsEncrypt setup.
<ul style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:40px;font-size:15px">
<li>Kubernetes Cluster</li>
<li><a href="https://helm.sh/docs/intro/install/">Helm</a> package manager installed</li>
</ul>
</p>
<hr>
<p style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:15px;font-size:15px">
Now follow the step by step instructions to configure letsencrypt and cert-manager on Kubernetes.
<br>
<ol style="font-family:Montserrat,'Helvetica Neue',Helvetica,Arial,sans-serif;padding-left:40px;font-size:15px">
<li>Install the <i>CustomResourceDefinition</i> resources. These are those resources which are not available by default but can be created by extending the Kubernetes API.
{% highlight ruby %}
$ kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml
{% endhighlight %}
</li>
<li>Create namespace for cert-manager.
{% highlight ruby %}
$ kubectl create ns cert-manager
{% endhighlight %}
</li>
<li>Add the Jetstack Helm repository and update your local Helm chart repo cache.
{% highlight ruby %}
$ helm repo add jetstack https://charts.jetstack.io
$ helm repo update
{% endhighlight %}
</li>
<li>Install the cert-manager Helm chart
{% highlight ruby %}
$ helm install --name cert-manager --namespace cert-manager --version v0.12.0 jetstack\cert-manager
{% endhighlight %}
</li>
<li>
Now verify that cert-manager pods are created and wait for all of them to be in running state.
{% highlight ruby %}
$ kubectl get pods -n cert-manager
NAME                                       READY   STATUS    RESTARTS   AGE
cert-manager-6bcc9d894d-d7s9j              1/1     Running   0          23h
cert-manager-cainjector-594fd9cc45-w2mhf   1/1     Running   0          23h
cert-manager-webhook-785ff8fc78-gjd88      1/1     Running   0          23h
{% endhighlight %}
</li>
<li>
Create an IAM user and attach following policy to get access to Route53 zones. If your entire setup is on AWS then you can use IAM role as well. But if you're using Kubernetes cluster outside AWS but using only Route53 then you can use IAM user.
Attach the following policy to the user and keep a note of AWS ACCESS KEY and SECRET ACCESS KEY.
{% highlight json %}
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "VisualEditor0",
            "Effect": "Allow",
            "Action": [
                "route53:GetChange",
                "route53:ChangeResourceRecordSets",
                "route53:ListResourceRecordSets"
            ],
            "Resource": [
                "arn:aws:route53:::hostedzone/*",
                "arn:aws:route53:::change/*"
            ]
        },
        {
            "Sid": "VisualEditor1",
            "Effect": "Allow",
            "Action": "route53:ListHostedZonesByName",
            "Resource": "*"
        }
    ]
}
{% endhighlight %}
</li>
<li>
Create a secret in cert-manager namespace which contains the SECRET ACCESS KEY. Save the secret key in the file called secretkey.
{% highlight ruby %}
$ kubectl create secret generic acme-route53 --from-file=secret-access-key=./secretkey -n cert-manager
{% endhighlight %}
It will create a secret called acme-route53 in the cert-manager namespace. Make sure you delete the file <i>secretkey</i>
</li>
<li>
Create a resource of type <i>ClusterIssuer</i> which will be used to issue certificates. Let's create it in the cert-manager namespace for better management. Although the namespace doesn't matter because ClusterIssuer resource is applicable to all the namespaces in the cluster.
{% highlight ruby %}
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
  namespace: cert-manager
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: YOUR EMAIL ID
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - selector:
        dnsZones:
          - "YOUR DOMAIN"
      dns01:
        route53:
          region: YOUR REGION
          accessKeyID: YOUR ACCESS KEY
          secretAccessKeySecretRef:
            name: acme-route53
            key: secret-access-key
{% endhighlight %}
</li>
<li>Now take a look at status of this resource.
{% highlight ruby %}
$ kubectl get clusterissuer -n cert-manager
NAME               READY   AGE
letsencrypt-prod   True    23h
{% endhighlight %}
Above output confirms that it is ready for use. If the status shows 'False' here then check the logs of 'cert-manager-6bcc9d894d-d7s9j' pod to troubleshoot. 
</li>
<li> Once we have our issuer setup, let's create a new certificate. It will take around 2-3 minutes as it first verifies the domain name by creating a recordset. 
{% highlight ruby %}
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: nginx-tls
  namespace: YOUR NAMESPACE
spec:
  secretName: nginx-tls
  issuerRef:
    name: letsencrypt-prod
    kind: ClusterIssuer
  dnsNames:
    - '*.YOUR.DOMAIN'
  acme:
    config:
      - dns01:
          provider: route53
        domains:
          - '*.YOUR.DOMAIN'
{% endhighlight %}
Above manifest will create a certificate by name 'nginx-tls' in the namespace you specify. Please do modify the DNS name as you provided while creating the ClusterIssuer.
</li>
<li>
Let's verify the status of the certificate.
{% highlight ruby %}
$ kubectl get cert -n YOUR_NAMESPACE
NAME       READY   SECRET    AGE
nginx-tls  True  nginx-tls   23h
{% endhighlight %}
Above output confirms that your certificate is ready to use.
</li>
<hr>
Above setup is all you need to configure a fully functional certificate generation process. Next step is to bind this certificate to your Ingress controller. Here is a sample Ingress manifest for reference. You can modify relevant parameters for your own deployment.
{%  highlight ruby %}
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: nginx
  namespace: default
  annotations:
    kubernetes.io/ingress.class: "nginx"
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
    - "YOUR.DOMAIN"
    - "*.YOUR.DOMAIN"
    secretName: nginx-tls
  rules:
  - host: YOUR.DOMAIN
    http:
      paths:
      - path: /
        backend:
          serviceName: YOUR-SERVICE
          servicePort: 80
{% endhighlight %}
<hr>
Setup of Ingress controller is beyond the scope of this article. Please follow relevant articles of the cloud providers you're working on. Take a note of the annotation for cluster issuer resource name and the secretName under tls section.
<br>
If all goes well then you'll be able to see a fully valid certificate associated with your domain and you don't have to worry about the renewal as well.