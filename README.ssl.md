
## Let's Encrypt --Overview
- Let's Encrypt is a Certificate Authority that lets you generate free, short-lived SSL certificates automatically
- to get a certificate we run the "cert bot" on our webserver--the Cert Bot "asks" Let's Encrypt for a certificate
- after a "challenge" is met, Let's Encrypt fulfills the cert request
- the process can be automated with a cron job so that the process becomes self-managed

## cert manager
- cert-manager provides Helm charts as a first-class method of installation on both Kubernetes
- Be sure never to embed cert-manager as a sub-chart of other Helm charts; 
- cert-manager manages non-namespaced resources in your cluster and care must be taken to ensure that it is installed exactly once
### cert manager installation with Helm
- `helm repo add jetstack https://charts.jetstack.io`
- `helm repo update`
- cert-manager requires a number of CRD resources, which can be installed manually using kubectl, or using the installCRDs option when installing the Helm chart:
  + `kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.8.0/cert-manager.crds.yaml`
  + cert manager can also be installed using the `helm` command
    + this is how it was done in our deployment:
    + `helm install cert-manager jetstack/cert-manager --namespace cert-manager --create-namespace --version v1.8.0 --set installCRDs=true`
    ![Helm cert manager installation](img/helm-deploy-cert-manager.png "helm cert manager installed successfully")

## Links
https://www.youtube.com/watch?v=hoLUigg4V18&t=121s
c
https://cert-manager.io/v0.14-docs/installation/kubernetes/ 
https://www.youtube.com/watch?v=HzxjsMrtIwc https://www.youtube.com/watch?v=7m4_kZOObzw 
https://myhightech.org/posts/20210402-cert-manager-on-eks/ 
https://github.com/antonputra/tutorials/tree/main/lessons/083 
https://aws.amazon.com/premiumsupport/knowledge-center/terminate-https-traffic-eks-acm/ 
https://cert-manager.io/docs/installation/helm/ 
https://www.howtogeek.com/devops/how-to-install-kubernetes-cert-manager-and-configure-lets-encrypt/ 
https://github.com/kubernetes-sigs/external-dns/blob/master/docs/tutorials/aws.md