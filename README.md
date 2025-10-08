# order-system-deploy


# Extra commands:

  Saving a Grafana Dashboard:

    kubectl create configmap cart-service-dashboard \
      --from-file=cart-service.json \
      --dry-run=client \
      --namespace=monitoring \
      -o yaml \
      | sed '/^metadata:/a \  labels:\n    grafana_dashboard: "1"' \
      > cart-service.yaml