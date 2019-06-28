helm install stable/nginx-ingress --name boream-ingress -f nginx.yaml

helm init --client-only
mkdir charts
helm fetch stable/nginx-ingress -d charts
mkdir templates
helm template charts/nginx-ingress/ -f nginx.yaml --output-dir templates