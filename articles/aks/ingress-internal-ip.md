---
title: Controlador de entrada na rede interna
titleSuffix: Azure Kubernetes Service
description: Aprenda a instalar e configurar um controlador de entrada NGINX para uma rede privada interna em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 3201f510db9970b7db548ee6a3348fa68d278248
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601458"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Criar um controlador de entrada para uma rede virtual interna no AKS (Serviço de Kubernetes do Azure)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação TLS para serviços de Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras de entrada e as rotas para os serviços de Kubernetes individuais. Usando um controlador de entrada e regras de ingresso, um único endereço IP pode ser usado para rotear tráfego a vários serviços em um cluster de Kubernetes.

Este artigo mostra como implantar o [controlador de ingresso NGINX][nginx-ingress] em um Cluster do Serviço de Kubernetes do Azure (AKS0) O controlador de entrada é configurado em um endereço IP e uma rede virtual interna privada. Nenhum acesso externo é permitido. Dois aplicativos são então executados no cluster do AKS, cada um dos quais é acessível pelo endereço IP único.

Você também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de ingresso que usa seus próprios certificados TLS][aks-ingress-own-tls]
- Crie um controlador de ingresso que use Let's Encrypt para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] ou [com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo usa [Helm 3][helm] para instalar o controlador de entrada do nginx. Certifique-se de que você está usando a versão mais recente do Helm e tenha acesso ao repositório de *entrada-Nginx* Helm. Para obter mais informações sobre como configurar e usar o Helm, consulte [Instalar aplicativos com Helm no Serviço de Kubernetes do Azure (AKS)][use-helm].

Este artigo também requer que você esteja executando o CLI do Azure versão 2.0.64 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Por padrão, um controlador de entrada NGINX é criado com uma atribuição dinâmica de endereço IP público. Um requisito de configuração comum é usar um endereço IP e uma rede privada interna. Essa abordagem permite restringir o acesso a seus serviços a usuários internos, sem acesso externo.

Crie um arquivo chamado *internal-ingress.yaml* usando o arquivo de manifesto de exemplo a seguir. Este exemplo atribui *10.240.0.42* ao recurso *loadBalancerIP*. Forneça seu próprio endereço IP interno para uso com o controlador de entrada. Certifique-se de que esse endereço IP não esteja em uso em sua rede virtual. Além disso, se você estiver usando uma rede virtual e uma sub-rede existentes, deverá configurar o cluster AKS com as permissões corretas para gerenciar a rede virtual e a sub-rede. Consulte [usar a rede kubenet com seus próprios intervalos de endereços IP no AKs (serviço kubernetes do Azure)][aks-configure-kubenet-networking] ou [Configurar a rede CNI do Azure no serviço kubernetes do Azure (AKs)][aks-configure-advanced-networking] para obter mais informações.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Agora, implemente o gráfico *nginx -gresso* com o Helm. Para usar o arquivo de manifesto criado na etapa anterior, adicione o parâmetro `-f internal-ingress.yaml`. Para redundância adicional, duas réplicas dos controladores de entrada NGINX são implementadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó em seu cluster AKS.

O controlador de entrada também precisa ser agendado em um nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. Um seletor de nó é especificado usando o parâmetro `--set nodeSelector` para instruir o agendador do Kubernetes a executar o controlador de entrada NGINX em um nó baseado em Linux.

> [!TIP]
> O exemplo a seguir cria um namespace kubernetes para os recursos de entrada chamados *ingress-Basic*. Especifique um namespace para seu próprio ambiente, conforme necessário. Se o cluster AKS não for kubernetes RBAC habilitado, adicione `--set rbac.create=false` aos comandos Helm.

> [!TIP]
> Se você quiser habilitar a [preservação de IP de origem do cliente][client-source-ip] para solicitações a contêineres em seu cluster, adicione `--set controller.service.externalTrafficPolicy=Local` ao comando Helm install. O IP de origem do cliente é armazenado no cabeçalho da solicitação em *X-forwardd-for*. Ao usar um controlador de entrada com preservação de IP de origem do cliente habilitada, a passagem TLS não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando o serviço de balanceador de carga do kubernetes é criado para o controlador de entrada do NGINX, seu endereço IP interno é atribuído. Para obter o endereço IP público, use `kubectl get service` o comando de serviço kubectl get.

```console
kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller
```

Leva alguns minutos para que o endereço IP seja atribuído ao serviço, conforme mostrado na seguinte saída de exemplo:

```
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Nenhuma regra de ingresso foi criada ainda, portanto, a página 404 padrão do controlador de ingresso NGINX é exibida se você navega para o endereço IP interno. As regras de ingresso são configuradas nas etapas a seguir.

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Para ver o controlador de entrada em ação, execute dois aplicativos de demonstração no cluster AKS. Neste exemplo, você usa o `kubectl apply` para implantar duas instâncias de um aplicativo *Hello World* simples.

Crie um arquivo *AKs-HelloWorld. YAML* e copie no seguinte exemplo YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld
  template:
    metadata:
      labels:
        app: aks-helloworld
    spec:
      containers:
      - name: aks-helloworld
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "Welcome to Azure Kubernetes Service (AKS)"
---
apiVersion: v1
kind: Service
metadata:
  name: aks-helloworld
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld
```

Crie um arquivo de *entrada-demo. YAML* e copie no seguinte exemplo YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: ingress-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: ingress-demo
  template:
    metadata:
      labels:
        app: ingress-demo
    spec:
      containers:
      - name: ingress-demo
        image: mcr.microsoft.com/azuredocs/aks-helloworld:v1
        ports:
        - containerPort: 80
        env:
        - name: TITLE
          value: "AKS Ingress Demo"
---
apiVersion: v1
kind: Service
metadata:
  name: ingress-demo
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: ingress-demo
```

Execute os dois aplicativos de demonstração usando `kubectl apply` :

```console
kubectl apply -f aks-helloworld.yaml --namespace ingress-basic
kubectl apply -f ingress-demo.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Criar rota de entrada

Ambos os aplicativos agora estão em execução no cluster do Kubernetes. Para rotear o tráfego para cada aplicativo, crie um recurso de entrada do Kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o endereço `http://10.240.0.42/` é roteado para o serviço nomeado `aks-helloworld`. O tráfego para o endereço `http://10.240.0.42/hello-world-two` é roteado para o serviço `ingress-demo`.

Crie um arquivo chamado `hello-world-ingress.yaml` e copie-o no YAML de exemplo a seguir.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/use-regex: "true"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
```

Crie o recurso de entrada usando o `kubectl apply -f hello-world-ingress.yaml` comando.

```console
kubectl apply -f hello-world-ingress.yaml
```

A saída de exemplo a seguir mostra que o recurso de entrada é criado.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testar o controlador de entrada

Para testar as rotas para o controlador de entrada, navegue até os dois aplicativos com um cliente Web. Se necessário, você pode testar rapidamente essa funcionalidade somente interna de um pod no cluster do AKS. Crie um pod de teste e uma sessão de terminal a ele:

```console
kubectl run -it --rm aks-ingress-test --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --namespace ingress-basic
```

Instale `curl` no pod usando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Agora acesse o endereço do controlador de entrada do kubernetes usando `curl` , como *http://10.240.0.42* . Forneça seu próprio endereço IP interno especificado quando você implantou o controlador de entrada na primeira etapa deste artigo.

```console
curl -L http://10.240.0.42
```

Nenhum caminho adicional foi fornecido com o endereço, portanto, o controlador de entrada usa como padrão a */* rota. O primeiro aplicativo de demonstração é retornado, conforme mostrado na saída de exemplo condensada a seguir:

```
$ curl -L http://10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Agora, adicione o caminho */Hello-World-Two* ao endereço, como *http://10.240.0.42/hello-world-two* . O segundo aplicativo de demonstração com um título personalizado é retornado, conforme mostrado na saída de exemplo condensada a seguir:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de entrada. Quando você implanta um gráfico Helm, vários recursos do Kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, você pode excluir o namespace de exemplo inteiro ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Excluir o namespace de exemplo e todos os recursos

Para excluir o namespace de exemplo inteiro, use o `kubectl delete` comando e especifique o nome do namespace. Todos os recursos no namespace são excluídos.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Excluir recursos individualmente

Como alternativa, uma abordagem mais granular é excluir os recursos individuais criados. Liste as versões Helm com o `helm list` comando. 

```console
helm list --namespace ingress-basic
```

Procure gráficos nomeados *nginx -gresso* e *aks-helloworld*, conforme mostrado na saída do exemplo a seguir:

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Desinstale as versões com o `helm uninstall` comando.

```console
helm uninstall nginx-ingress --namespace ingress-basic
```

O exemplo a seguir desinstala a implantação de entrada do NGINX.

```
$ helm uninstall nginx-ingress --namespace ingress-basic

release "nginx-ingress" uninstalled
```

Em seguida, remova os dois aplicativos de exemplo:

```console
kubectl delete -f aks-helloworld.yaml --namespace ingress-basic
kubectl delete -f ingress-demo.yaml --namespace ingress-basic
```

Remova a rota de entrada que direcionava o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Por fim, você pode excluir o namespace em si. Use o `kubectl delete` comando e especifique o nome do namespace:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Próximas etapas

Este artigo incluído alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de entrada NGINX ][nginx-ingress]

Você também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada com um IP público dinâmico e configurar Vamos Criptografar para gerar certificados TLS automaticamente][aks-ingress-tls]
- [Criar um controlador de entrada com um endereço IP público estático e configurar Vamos Criptografar para gerar certificados TLS automaticamente][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md