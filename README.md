# Kubernetes com Kind - Projeto Inicial

Este projeto é uma configuração inicial para trabalhar com Kubernetes utilizando o Kind (Kubernetes IN Docker) e o kubectl. O objetivo é criar um ambiente local para aprendizado e testes com Kubernetes.

---

## Pré-requisitos

Certifique-se de ter os seguintes itens instalados em seu ambiente:

- **Docker** (necessário para executar o Kind)
- **curl** ou **wget** (para instalar o kubectl, caso ainda não esteja instalado)

---

## Passo a Passo para Configuração

### 1. Instalação do Kind

1. Instale o Kind:
    ```bash
    curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.11.1/kind-linux-amd64
    chmod +x ./kind
    mv kind /usr/local/bin/
    ```

2. Instale o kubectl:
    ```bash
    curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
    sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
    ```

3. Para criar o cluster:
    ```bash
    kind create cluster --name kube --config cluster/cluster-config.yaml
    ```

4. Para criar os namespaces:
    ```bash
    kubectl apply -f cluster/namespaces.yaml
    ```

5. Para criar o argocd
    ```bash
    kubectl apply -n argocd -f cluster/argocd/install.yaml
    #senha
    kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d && echo
    #forward
    kubectl port-forward -n argocd service/argocd-server 8443:443
    ```
    Altere a senha. Ex.: admin123

6. Para criar o rabbitmq
    ```bash
    kubectl apply -f cluster/rabbitmq/install.yaml
    #forward
    kubectl port-forward "service/rabbitmq-cluster" 15672
    ```

7. Para instalar o keda
    ```bash
    #helm
    curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
    chmod 700 get_helm.sh
    ./get_helm.sh
    
    helm repo add kedacore https://kedacore.github.io/charts
    helm repo update
    helm install keda kedacore/keda --namespace keda --create-namespace
    ```

CleanUp. Excluir o cluster
    ```bash
    kind delete cluster
    ```