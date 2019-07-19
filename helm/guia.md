Create DO cluster


helm fetch --untar --untardir ./charts stable/nginx-ingress
###################
controller:
  stats:
    enabled: true
  metrics:
    enabled: true
###################
helm template --values values-nginx.yaml --name boream --output-dir ./manifests ./charts/nginx-ingress/
kubectl apply -f manifests/nginx-ingress/templates/


helm fetch --untar --untardir ./charts jetstack/cert-manager
###################
replicaCount: 1

resources:
  requests:
    cpu: 10m
    memory: 32Mi

securityContext:
  enabled: true
  fsGroup: 1001
  runAsUser: 1001
###################
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    # You must replace this email address with your own.
    # Let's Encrypt will use this to contact you about expiring
    # certificates, and issues related to your account.
    email: paurosello@gmail.com
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: issuer-account-key
    # Add a single challenge solver, HTTP01 using nginx
    solvers:
    - http01:
        ingress:
          class: nginx
###################
helm template --values values-certmanager.yml --name boream --output-dir ./manifests charts/cert-manager/
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml
kubectl apply -f manifests/cert-manager/templates/


helm fetch --untar --untardir ./charts stable/mariadb
###################
rootUser:
  password: boream

db:
  user: boream
  password: boream
  name: my_database

replication:
  enabled: true
  user: replicator
  password: replicator
###################
helm template --values values-mariadb.yaml --name boream --output-dir ./manifests charts/mariadb/
kubectl apply -f manifests/mariadb/templates/
###################
slave:
  replicas: 4

service:
  type: NodePort
  port: 3306
###################
kubectl get pods -A -w



helm fetch --untar --untardir ./charts stable/kube-ops-view
###################
ingress:
  enabled: true
  hostname: ops.188.166.203.89.nip.io
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt
  tls:
    - secretName: kube-ops-view.tls
      hosts:
        - ops.188.166.203.89.nip.io

rbac:
  create: true
###################
helm template --values values-kube-ops-view.yaml --name boream --output-dir ./manifests charts/kube-ops-view/
kubectl apply -f manifests/kube-ops-view/templates/




helm repo add openfaas https://openfaas.github.io/faas-netes/
helm fetch --untar --untardir ./charts openfaas/openfaas
##################
functionNamespace: openfaas-fn

operator:
  image: openfaas/openfaas-operator:0.9.7
  create: true
  createCRD: true
  resources:
    requests:
      memory: "120Mi"
      cpu: "50m"

ingress:
  enabled: true
  hosts:
    - host: faas.188.166.203.89.nip.io
      serviceName: gateway
      servicePort: 8080
      path: /
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt
  tls:
    - secretName: faas.tls
      hosts:
        - faas.188.166.203.89.nip.io
##################
kubectl create secret generic basic-auth \
--from-literal=basic-auth-user=admin \
--from-literal=basic-auth-password="boream"

kubectl create namespace openfaas-fn
##################
helm template --values values-openfaas.yml --name boream --output-dir ./manifests charts/openfaas/
kubectl apply -f manifests/openfaas/templates/
kubectl get functions -A

echo "POST https://faas.188.166.203.89.nip.io/function/figlet" | vegeta attack -duration=120s | tee results.bin | vegeta report

https://hub.helm.sh/charts/stable/moodle