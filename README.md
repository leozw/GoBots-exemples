# 📦 **GoBots-Exemples: Instrumentação com OpenTelemetry**

## 🚀 **Introdução**

Este repositório contém exemplos para instrumentar aplicações com OpenTelemetry. Siga o passo a passo abaixo para instrumentar seus serviços e começar a coletar métricas e traces!  🌐

## 🛠️ **Requisitos**

- Kubernetes com `kubectl` configurado  ☸️
- Helm  🪖
- Kustomize

## 📂 **Estrutura do Projeto**

- `app-exemple/`: Exemplo de aplicação instrumentada com OpenTelemetry
- `opentelemetry-operator/`: Operador para gerenciar OpenTelemetry
- `otel-collector/`: Coletor de dados OpenTelemetry

## 📝 **Passo a Passo**

### 1. **Clone o repositório**  📥

```bash
git clone https://github.com/leozw/GoBots-exemples.git
cd GoBots-exemples
```

### 2. **Instale o OpenTelemetry Operator**  ⚙️

```bash
helm repo add open-telemetry https://open-telemetry.github.io/opentelemetry-helm-charts -f opentelemetry-operator/values.yaml
helm install opentelemetry-operator open-telemetry/opentelemetry-operator -n monitoring
```

### 3. **Instale o OpenTelemetry Collector**  🛠️

```bash
kubectl apply -k otel-collector/
```

### 4. **Instrumente sua Aplicação**  🚀

No diretório `app-exemple/`, a aplicação já está instrumentada.

Você precisa adicionar a seguinte anotação no seu manifesto o Helm:

```yaml
# You need this annotation for instrument your application
annotations:
  instrumentation.opentelemetry.io/inject-java: "true"
```

E as seguintes variáveis:

```yaml
# You need this envs for instrument your application    
env:
  - name: OTEL_SERVICE_NAME
    value: "<APP_SERVICE_NAME>"
  - name: OTEL_TRACES_EXPORTER
    value: "otlp"
  - name: OTEL_METRICS_EXPORTER
    value: "otlp"
  - name: OTEL_LOGS_EXPORTER
    value: "otlp" 
  - name: OTEL_EXPORTER_OTLP_TRACES_ENDPOINT
    value: "http://opentelemetrycollector.monitoring.svc.cluster.local:4318/v1/traces"
  - name: OTEL_EXPORTER_OTLP_LOGS_ENDPOINT
    value: "http://opentelemetrycollector.monitoring.svc.cluster.local:4318/v1/logs"              
  - name: OTEL_EXPORTER_OTLP_METRICS_ENDPOINT
    value: "http://opentelemetrycollector.monitoring.svc.cluster.local:4318/v1/metrics"
```

Altere `OTEL_SERVICE_NAME` para o desejado.

### 5. **Deploy no Kubernetes**  ☸️

Para fazer deploy da aplicação de teste no Kubernetes:

```bash
kubectl apply -f app-exemple/deployment.yaml -f app-exemple/service.yaml
```

### 6. **Verifique os Dados**  🔍

Acesse os dados enviados para sua stack observabilidade.