# order-system-deploy

    helm repo add sealed-secrets https://bitnami-labs.github.io/sealed-secrets
    helm install sealed-secrets -n kube-system --set-string fullnameOverride=sealed-secrets-controller sealed-secrets/sealed-secrets
    go install github.com/bitnami-labs/sealed-secrets/cmd/kubeseal@main

    kubectl create secret generic grafana-secret \
      --namespace monitoring \
      --from-literal=admin-user={FILL_VALUE_HERE} \
      --from-literal=admin-password={FILL_VALUE_HERE} \
      --dry-run=client -o yaml | \
    kubeseal \
      --controller-name=sealed-secrets-controller \
      --controller-namespace=kube-system \
      --format yaml > \
    ./monitoring/base/prometheus-grafana/grafana-secret-sealed.yaml


    kubectl create secret generic jenkins-secret \
      --namespace integration \
      --from-literal=username={FILL_VALUE_HERE} \
      --from-literal=password={FILL_VALUE_HERE} \
      --dry-run=client -o yaml | \
    kubeseal \
      --controller-name=sealed-secrets-controller \
      --controller-namespace=kube-system \
      --format yaml > \
    ./integration/base/jenkins/jenkins-secret-sealed.yaml


# Extra commands:

  Update test scripts

    kubectl create configmap cart-update-soak-test-script \
      --from-file=./apps/base/cart-service/job-scripts/cart-update-soak-test.js \
      --dry-run=client \
      --namespace=apps \
      -o yaml | \
    sed '/^metadata:/a \  labels:\n    app.kubernetes.io/name: cart-service' > \
    ./apps/base/cart-service/job-scripts/cart-update-soak-test-script.yaml


  Saving a Grafana Dashboard:

    kubectl create configmap cart-service-dashboard \
      --from-file=cart-service.json \
      --dry-run=client \
      --namespace=monitoring \
      -o yaml \
      | sed '/^metadata:/a \  labels:\n    grafana_dashboard: "1"' \
      > cart-service.yaml