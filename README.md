# monitoring
Helm charts and deployments for monitoring tools.

kubectl create namespace monitoring

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo add grafana https://grafana.github.io/helm-charts

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts

helm repo update

helm install blackbox-exporter prometheus-community/prometheus-blackbox-exporter --namespace monitoring

helm repo update

helm install prometheus prometheus-community/prometheus -n monitoring

helm upgrade --install promtail grafana/promtail -n monitoring

helm install blackbox-exporter prometheus-community/prometheus-blackbox-exporter --namespace monitoring

helm upgrade --install --values loki.yaml loki grafana/loki-stack -n monitoring

-----------------------------------------------------------
kubectl get all -n monitoring

> edit the prometheus server deployments under volumes: as shown below

kubectl edit deployments prometheus-server -n monitoring

      volumes:
      - name: config-volume
        configMap:
          name: prometheus-config
      - name: storage-volume
        emptyDir: {}
		
kubectl rollout restart deployments prometheus-server -n monitoring

----------------------------------------------------------------------------------------
**After installing grafana use below command to check password:**
kubectl get secret --namespace mon grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo

username: admin
password: output from above command

-----------------------------------------------------------------------------------
**Portforward:**

kubectl port-forward service/prometheus-server 9090:80 -n monitoring

kubectl port-forward service/loki-grafana 3000:80 -n monitoring

kubectl port-forward service/blackbox-exporter-prometheus-blackbox-exporter 9115:9115 -n monitoring

--------------------------------------------------------------------------------------

**open grafana add prometheus datasource:**

http://prometheus-server:80

save and test.
kubectl get secret --namespace mon grafana -o jsonpath="{.data.admin-password}"

--------------------------------------------------------------------------------------------------------

Kubectl edit daemonset loki-promtail -n monitoring

**Add the below parameters:**

spec:
  template:
    spec:
      tolerations:
      - effect: NoSchedule
        key: node-role.kubernetes.io/master
        operator: Exists
      - effect: NoSchedule
        key: node-role.kubernetes.io/control-plane
        operator: Exists
      - effect: NoSchedule
        key: node-role.kubernetes.io/worker
        operator: Exists
      - effect: NoSchedule
        key: custom-taint-key
        operator: Exists
      - key: "app"
        operator: "Equal"
        value: "frontend"
        effect: "NoSchedule"
      - key: "app"
        operator: "Equal"
        value: "backend"
        effect: "NoSchedule"


kubectl rollout restart daemonset loki-promtail -n monitoring

------------------------------------------------------------------------------------------------

kubectl edit configmap prometheus-server -n monitoring

    scrape_configs:
      - job_name: prometheus
        static_configs:
          - targets:
              - localhost:9090

    - job_name: 'ABC'
      metrics_path: /probe
      params:
        module: [http_ABC]
      static_configs:
      - targets:
        - URL/IP:PORT
      relabel_configs:
        - source_labels: [__address__]
          target_label: __param_target
        - target_label: instance
          replacement: blackbox-exporter
        - target_label: __address__
          replacement: blackbox-exporter-prometheus-blackbox-exporter:9115


Add the same for all the services to be monitored by replacing targets.


kubectl rollout restart deployment prometheus-server -n monitoring

-----------------------------------------------------------------------------------------------------------

kubectl edit configmap blackbox-exporter-prometheus-blackbox-exporter -n monitoring

**Add the job details to be monitored under modules:**

      http_ADC:
        http:
          follow_redirects: true
          preferred_ip_protocol: ip4
          valid_http_versions:
          - HTTP/1.1
          - HTTP/2.0
          headers:
            User-Agent: "Blackbox Exporter"
          fail_if_ssl: false
          fail_if_not_ssl: false
          tls_config:
            insecure_skip_verify: true
          method: GET
          valid_status_codes:
          - 200
          fail_if_body_matches_regexp:
          - "Error"
        prober: http
        timeout: 30s


kubectl rollout restart deployment blackbox-exporter-prometheus-blackbox-exporter -n monitoring

---------------------------------------------------------------------------------------------------------------
**Example to tcp connect**

      tcp_connect_AEF:
        tcp:
          preferred_ip_protocol: ip4
        prober: tcp
        timeout: 30s

      tcp_connect_BCD:
        tcp:
          preferred_ip_protocol: ip4
        prober: tcp
        timeout: 30s

      tcp_connect_ABC:
        tcp:
          preferred_ip_protocol: ip4
        prober: tcp
        timeout: 30s

