---
title: Criar um controlador de entrada para uma rede interna no AKS (Serviço de Kubernetes do Azure)
description: Aprenda a instalar e configurar um controlador de entrada NGINX para uma rede privada interna em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 935b96bd553c9ae73b55086483baa0ea7c4aeaa4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615463"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Criar um controlador de entrada para uma rede virtual interna no AKS (Serviço de Kubernetes do Azure)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação TLS para serviços de Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras de entrada e as rotas para os serviços de Kubernetes individuais. Usando um controlador de entrada e regras de ingresso, um único endereço IP pode ser usado para rotear tráfego a vários serviços em um cluster de Kubernetes.

Este artigo mostra como implantar o [controlador de ingresso NGINX][nginx-ingress] em um cluster do serviço de Kubernetes do Azure (AKS). O controlador de entrada é configurado em um endereço IP e uma rede virtual interna privada. Nenhum acesso externo é permitido. Dois aplicativos são então executados no cluster do AKS, cada um dos quais é acessível pelo endereço IP único.

Você também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa seus próprios certificados TLS][aks-ingress-own-tls]
- Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] or [with a static public IP address][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo usa o Helm para instalar o NGINX ingress controller, Gerenciador de certificado e um aplicativo web de exemplo. Você precisa ter o Helm inicializado dentro do cluster do AKS e usar uma conta de serviço para Tiller. Para obter mais informações sobre como configurar e usar o Helm, consulte [instalar aplicativos com Helm no serviço de Kubernetes do Azure (AKS)][use-helm].

Este artigo também requer que você está executando a CLI do Azure versão 2.0.64 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Por padrão, um controlador de entrada NGINX é criado com uma atribuição dinâmica de endereço IP público. Um requisito de configuração comum é usar um endereço IP e uma rede privada interna. Essa abordagem permite restringir o acesso a seus serviços a usuários internos, sem acesso externo.

Crie um arquivo chamado *internal-ingress.yaml* usando o arquivo de manifesto de exemplo a seguir. Este exemplo atribui *10.240.0.42* ao recurso *loadBalancerIP*. Forneça seu próprio endereço IP interno para uso com o controlador de entrada. Certifique-se de que esse endereço IP não esteja em uso em sua rede virtual.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Agora, implemente o gráfico *nginx -gresso* com o Helm. Para usar o arquivo de manifesto criado na etapa anterior, adicione o parâmetro `-f internal-ingress.yaml`. Para redundância adicional, duas réplicas dos controladores de entrada NGINX são implementadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó em seu cluster AKS.

O controlador de entrada também precisa ser agendada em um nó do Linux. Nós do Windows Server (atualmente em visualização no AKS) não devem executar o controlador de entrada. Um seletor de nó é especificado usando o `--set nodeSelector` parâmetro para informar o Agendador Kubernetes para executar o controlador de entrada NGINX em um nó com base em Linux.

> [!TIP]
> O exemplo a seguir cria um namespace de Kubernetes para os recursos de entrada denominado *ingresso basic*. Especifique um namespace para o seu próprio ambiente conforme necessário. Se seu cluster do AKS não for habilitado o RBAC, adicione `--set rbac.create=false` para os comandos do Helm.

> [!TIP]
> Se você deseja habilitar [preservação de IP de origem do cliente][client-source-ip] para solicitações para contêineres no cluster, adicione `--set controller.service.externalTrafficPolicy=Local` comando de instalação para o Helm. A origem do cliente IP é armazenado no cabeçalho da solicitação sob *X-Forwarded-For*. Ao usar um controlador de entrada com preservação de IP de origem de cliente habilitada, a passagem SSL não funcionará.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    -f internal-ingress.yaml \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Quando o serviço de balanceador de carga do Kubernetes é criado para o controlador de entrada NGINX, seu endereço IP interno é atribuído, conforme mostrado na seguinte saída de exemplo:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Nenhuma regra de ingresso foi criada ainda, portanto, a página 404 padrão do controlador de ingresso NGINX é exibida se você navega para o endereço IP interno. As regras de ingresso são configuradas nas etapas a seguir.

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Para ver o controlador de entrada em ação, executaremos dois aplicativos de demonstração no cluster do AKS. Neste exemplo, o Helm é utilizado para executar várias instâncias de um aplicativo simples hello world.

Antes de instalar os gráficos do Helm de exemplo, adicione o repositório de exemplos do Azure para seu ambiente de Helm da seguinte maneira:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Crie o primeiro aplicativo de demonstração de um gráfico do Helm com o seguinte comando:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Em seguida, instale uma segunda instância do aplicativo demo. Para a segunda instância, especifique um novo título para que os dois aplicativos sejam visualmente distintos. Você também precisa especificar um nome de serviço exclusivo:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Criar rota de entrada

Ambos os aplicativos agora estão em execução no cluster do Kubernetes. Para rotear o tráfego para cada aplicativo, crie um recurso de entrada do Kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o endereço `http://10.240.0.42/` é roteado para o serviço nomeado `aks-helloworld`. O tráfego para o endereço `http://10.240.0.42/hello-world-two` é roteado para o serviço `ingress-demo`.

Crie um arquivo chamado `hello-world-ingress.yaml` e copie-o no YAML de exemplo a seguir.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Crie o recurso de entrada usando o `kubectl apply -f hello-world-ingress.yaml` comando.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Testar o controlador de entrada

Para testar as rotas para o controlador de entrada, navegue até os dois aplicativos com um cliente Web. Se necessário, você pode testar rapidamente essa funcionalidade somente interna de um pod no cluster do AKS. Crie um pod de teste e uma sessão de terminal a ele:

```console
kubectl run -it --rm aks-ingress-test --image=debian --namespace ingress-basic
```

Instale `curl` no pod usando `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Agora, acesse o endereço de seu controlador de entrada d o Kubernetes usando `curl`, como *http://10.240.0.42* . Forneça seu próprio endereço IP interno especificado quando você implantou o controlador de entrada na primeira etapa deste artigo.

```console
curl -L http://10.240.0.42
```

Nenhum caminho adicional foi fornecido com o endereço, portanto, o controlador de entrada padrão será a rota */* . O primeiro aplicativo de demonstração é retornado, conforme mostrado na saída de exemplo condensada a seguir:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Agora, adicione o caminho */hello-world-two* ao endereço, como *http://10.240.0.42/hello-world-two* . O segundo aplicativo de demonstração com um título personalizado é retornado, conforme mostrado na saída de exemplo condensada a seguir:

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

Este artigo usou o Helm para instalar os componentes de ingresso e os aplicativos de amostra. Quando você implanta um gráfico Helm, vários recursos do Kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, você pode excluir o namespace de exemplo inteiro ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Excluir o namespace de exemplo e todos os recursos

Para excluir o namespace de exemplo completo, use o `kubectl delete` de comando e especifique o nome do namespace. Todos os recursos no namespace são excluídos.

```console
kubectl delete namespace ingress-basic
```

Em seguida, remova o repositório do Helm para o aplicativo hello world do AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Excluir recursos individualmente

Como alternativa, uma abordagem mais granular é excluir os recursos individuais criados. Lista o Helm é lançado com o `helm list` comando. Procure gráficos nomeados *nginx -gresso* e *aks-helloworld*, conforme mostrado na saída do exemplo a seguir:

```
$ helm list

NAME                REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
kissing-ferret      1           Tue Oct 16 17:13:39 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
intended-lemur      1           Tue Oct 16 17:20:59 2018    DEPLOYED    aks-helloworld-0.1.0                default
pioneering-wombat   1           Tue Oct 16 17:21:05 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Excluir as versões com o `helm delete` comando. O exemplo a seguir exclui a implantação de ingresso NGINX e os dois aplicativos exemplo AKS hello world.

```
$ helm delete kissing-ferret intended-lemur pioneering-wombat

release "kissing-ferret" deleted
release "intended-lemur" deleted
release "pioneering-wombat" deleted
```

Em seguida, remova o repositório do Helm para o aplicativo hello world do AKS:

```console
helm repo remove azure-samples
```

Remova a rota de entrada que direcionava o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Por fim, você pode excluir o próprio namespace. Use o `kubectl delete` de comando e especifique o nome do namespace:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Próximas etapas

Este artigo incluído alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de ingresso NGINX][nginx-ingress]

Você também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada com um IP público dinâmico e configurar vamos criptografar para gerar automaticamente certificados TLS][aks-ingress-tls]
- [Criar um controlador de entrada com um endereço IP público estático e configurar vamos criptografar para gerar automaticamente certificados TLS][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
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