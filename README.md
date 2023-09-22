# Kubernetes For DEV

Conceitos-chave do k8s

É importante saber que a forma como o k8s gerencia os contêineres é ligeiramente diferente de outros orquestradores, como o Docker Swarm, sobretudo devido ao fato de que ele não trata os contêineres diretamente, mas sim através de pods. Vamos conhecer alguns dos principais conceitos que envolvem o k8s a seguir:

    Pod: É o menor objeto do k8s. Como dito anteriormente, o k8s não trabalha com os contêineres diretamente, mas organiza-os dentro de pods, que são abstrações que dividem os mesmos recursos, como endereços, volumes, ciclos de CPU e memória. Um pod pode possuir vários contêineres;

    Deployment: É um dos principais controllers utilizados. O Deployment, em conjunto com o ReplicaSet, garante que determinado número de réplicas de um pod esteja em execução nos nós workers do cluster. Além disso, o Deployment também é responsável por gerenciar o ciclo de vida das aplicações, onde características associadas a aplicação, tais como imagem, porta, volumes e variáveis de ambiente, podem ser especificados em arquivos do tipo yaml ou json para posteriormente serem passados como parâmetro para o kubectl executar o deployment. Esta ação pode ser executada tanto para criação quanto para atualização e remoção do deployment;

    ReplicaSets: É um objeto responsável por garantir a quantidade de pods em execução no nó;

    Services: É uma forma de você expor a comunicação através de um ClusterIP, NodePort ou LoadBalancer para distribuir as requisições entre os diversos Pods daquele Deployment. Funciona como um balanceador de carga.

## Kubectl

```shell
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Instalação Global

```shell
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

Instalação local

```shell
chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl

```

## Kind

[https://kind.sigs.k8s.io/](https://kind.sigs.k8s.io/)

O Kind (Kubernetes in Docker) é outra alternativa para executar o Kubernetes num ambiente local para testes e aprendizado, mas não é recomendado para uso em produção.

Instalar

```shell
# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.20.0/kind-linux-amd64
chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

Criar um cluster Sigle node

```shell
kind create cluster
```

Criando um cluster com múltiplos nós locais com o Kind

É possível para essa aula incluir múltiplos nós na estrutura do Kind, que foi mencionado anteriormente.

Execute o comando a seguir para selecionar e remover todos os clusters locais criados no Kind.

kind delete clusters $(kind get clusters)

Deleted clusters: ["giropops" "kind"]

  Crie um arquivo de configuração para definir quantos e o tipo de nós no cluster que você deseja. No exemplo a seguir, será criado o arquivo de configuração kind-3nodes.yaml para especificar um cluster com 1 nó control-plane (que executará o control plane) e 2 workers.


Criar um cluster multi nodes

```shell
cat << EOF > $HOME/kind-3nodes.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
EOF
```

```shell
kind create cluster --name cda --config kind-3nodes.yaml
```

Listar os nodes

```shell
kubectl get nodes
```

Listar os podes no namespace default

```shell
kubectl get pods
```

Listar todos pods

```shell
kubectl get po -A
```

Listar os Deployments

```shell
kubectl get deployment
kubectl get deployment -A
```

Listar os Services

```shell
kubectl get service
kubectl get service -A
```

Listar as ReplicaSer

```shell
kubectl get replicaset
kubectl get replicaset -A
```

Criar um Pod

```shell
kubectl run --image nginx --port 80 cda
```

Listar pods

```shell
kubectl get pods
```

Delete pods

```shell
kubectl delete pods cda
```

Expor um Pod como Serviço

```shell
kubectl expose pods cda
```

Delete Service

```shell
kubectl delete svc cda
```

Expor um Pod como Serviço

```shell
kubectl expose pods --type NodePort
```

# Conhecendo o YAML e o Kubectl com dry-run

Simula a criação de um Pod

```shell
kubectl run --image nginx --port 80 cda --dry-run=client
```

Simula a criação de um Pod e mostra no terminal o yaml

```shell
kubectl run --image nginx --port 80 cda --dry-run=client -oyaml
```

```shell
kubectl run --image nginx --port 80 cda --dry-run=client -oyaml > pod.yaml
```

Criar um Deployment com uma replicaset

```shell
kubectl create deployment --image nginx cda --dry-run=client -oyaml
```

Expor o deployment como serviço

```shell
kubectl expose deployment cda --type="NodePort" --port 80
```

Acessar o serviço pelo Browser usando port-forward

```shell
kubectl port-forward services/cda 8080:80
```

[http:localhost:8080](http://localhost:8080)