# Installing and configuring Prometheus and Thanos

# Create Iam Policy using cli
aws iam create-policy --policy-name allow-to-s3-bucket-thanos --policy-document file://<path-to-json>
# Attach policy to cluster using eksctl and create service account
eksctl create iamserviceaccount --name prometheus-sa --cluster <cluster-name> --namespace=prometheus --attach-policy-arn arn:aws:iam::706050889978:policy/allow-to-s3-monitoring-bucket --approve --override-existing-serviceaccounts
# Create secret
kubectl  create secret generic -n prometheus thanos-storage-config --from-file=thanos-storage-config.yaml=thanos-storage-config.yaml
# Helming
helm upgrade --install prometheus stable/prometheus-operator -f values_default.yaml -n prometheus

# Deploy Thanos Querier
kubectl apply -f thanos-query-deployment.yaml -f thanos-query-service.yaml -f thanos-query-serviceMonitor.yaml

# Deploy Thanos Store
kubectl apply -f thanos-store-statefulSet.yaml -f thanos-store-service.yaml -f thanos-store-serviceMonitor.yaml

# Deploy Thanos Compactor
kubectl apply -f thanos-compact-statefulSet.yaml -f thanos-compact-service.yaml -f thanos-compact-serviceMonitor.yaml
