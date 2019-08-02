---
title: Use seus próprios certificados TLS para ingressar com o cluster do Serviço Azure Kubernetes (AKS)
description: Aprenda como instalar e configurar um controlador de ingresso NGINX que usa seus próprios certificados em um cluster do Serviço Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 2b30ade9971ede6f9544b618504033553392e9bd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615430"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Crie um controlador de ingresso HTTPS e use seus próprios certificados TLS no Serviço de Kubernetes do Azure (AKS)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação TLS para serviços de Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras de entrada e as rotas para os serviços de Kubernetes individuais. Usando um controlador de entrada e regras de ingresso, um único endereço IP pode ser usado para rotear tráfego a vários serviços em um cluster de Kubernetes.

Este artigo mostra como implantar o [controlador de ingresso NGINX][nginx-ingress] em um cluster do serviço de Kubernetes do Azure (AKS). Você gera seus próprios certificados e cria um segredo do Kubernetes para uso com a rota de ingresso. Finalmente, dois aplicativos são executados no cluster do AKS, cada um dos quais é acessível em um único endereço IP.

Você também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede interna, privada e o endereço IP][aks-ingress-internal]
- Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] or [with a static public IP address][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo usa o Helm para instalar o controlador de ingresso NGINX e um aplicativo da web de amostra. Você precisa ter o Helm inicializado dentro do cluster do AKS e usar uma conta de serviço para Tiller. Verifique se você está usando a versão mais recente do Helm. Para obter instruções, consulte a [Helm instalar docs][helm-install]. For more information on configuring and using Helm, see [Install applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Este artigo também requer que você está executando a CLI do Azure versão 2.0.64 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, use `Helm` para instalar *nginx-ingress*. Para redundância adicional, duas réplicas dos controladores de entrada NGINX são implementadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó em seu cluster AKS.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Durante a instalação, um endereço IP público do Azure é criado para o controlador de entrada. Esse endereço IP público é estático por todo o período de vida do controlador de entrada. Se você excluir o controlador de entrada, a atribuição de endereço IP pública será perdida. Se, em seguida, você criar outro controlador de entrada, um novo endereço IP público será atribuído. Se você deseja reter o uso do endereço IP público, você pode em vez disso [criar um controlador de entrada com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, use `kubectl get service` o comando de serviço kubectl get. Pode levar alguns minutos até que o endereço IP seja atribuído ao serviço.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Anote esse endereço IP público, pois ele é usado na última etapa para testar a implantação.

Nenhuma regra de entrada foi criada ainda. Se você navegar até o endereço IP público, a página 404 padrão do controlador de ingresso NGINX será exibida.

## <a name="generate-tls-certificates"></a>Gerar certificados TLS

Para este artigo, vamos gerar um certificado autoassinado com `openssl`. Para uso em produção, você deve solicitar um certificado assinado e confiável por meio de um provedor ou de sua própria autoridade de certificação (CA). Na próxima etapa, você gera um *Segredo* do Kubernetes usando o certificado TLS e a chave privada gerados pelo OpenSSL.

O exemplo a seguir gera um certificado RSA X509 de 2048 bits válido por 365 dias chamado *aks-ingress-tls.crt*. O arquivo de chave privada é denominado *aks-entrada-tls.key*. Um segredo de TLS do Kubernetes requer esses dois arquivos.

Este artigo trabalha com *demo.azure.com* assunto nome comum e não precisa ser alterado. Para uso em produção, especifique seus próprios valores organizacionais para o parâmetro `-subj`:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Crie o segredo do Kubernetes para o certificado TLS

Para permitir que o Kubernetes use o certificado TLS e a chave privada para o controlador de entrada, crie e use um segredo. O segredo é definido uma vez e usa o certificado e o arquivo de chaves criados na etapa anterior. Você então faz referência a esse segredo quando define rotas de ingresso.

O exemplo a seguir cria um nome do segredo *aks-entrada-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Um controlador de entrada e um segredo com seu certificado tem sido configurado. Agora, vamos executar duas demonstrações de aplicativos no cluster do AKS. Neste exemplo, o Helm é utilizado para executar várias instâncias de um aplicativo simples hello world.

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

Ambos os aplicativos estão em execução no cluster de Kubernetes, no entanto, foram configurados com um serviço do tipo `ClusterIP`. Dessa forma, os aplicativos não são acessíveis a partir da Internet. Para torná-los disponíveis publicamente, crie um recurso de entrada de Kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o endereço `https://demo.azure.com/` é roteado para o serviço nomeado `aks-helloworld`. O tráfego para o endereço `https://demo.azure.com/hello-world-two` é roteado para o serviço `ingress-demo`. Para este artigo, você não precisa alterar esses nomes de host de demonstração. Para uso em produção, forneça os nomes especificados como parte do processo de solicitação e geração de certificado.

> [!TIP]
> Se o nome do host especificado durante o processo de solicitação de certificado, o nome CN, não corresponder ao host definido na rota de ingresso, o controlador de entrada exibirá um *certificado falso do controlador de entrada do Kubernetes*. Certifique-se de que seus nomes de host de certificado e de rota de ingresso correspondam.

A seção *tls* diz à rota de ingresso para usar o Segredo chamado *aks-ingress-tls* para o host *demo.azure.com*. Novamente, para uso em produção, especifique seu próprio endereço de host.

Crie um arquivo chamado `hello-world-ingress.yaml` e copie-o no YAML de exemplo a seguir.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
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

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Para testar os certificados com nosso host *demo.azure.com* falso, use `curl` e especifique o parâmetro *--resolve*. Este parâmetro permite que você mapeie o *nome demo.azure.com* para o endereço IP público do seu controlador de entrada. Especifique o endereço IP público do seu próprio controlador de entrada, conforme mostrado no exemplo a seguir:

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

Nenhum caminho adicional foi fornecido com o endereço, portanto, o controlador de entrada padrão será a rota */* . O primeiro aplicativo de demonstração é retornado, conforme mostrado na saída de exemplo condensada a seguir:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

O parâmetro *-v* em nosso comando `curl` gera informações detalhadas, incluindo o certificado TLS recebido. Na metade da saída de curvas, você pode verificar se seu próprio certificado TLS foi usado. O parâmetro *-k* continua carregando a página, embora estejamos usando um certificado autoassinado. O exemplo a seguir mostra que o certificado do *emissor: CN=demo.azure.com; O=aks-ingress-tls* foi usado:

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Agora adicione o caminho */hello-world-two* ao endereço, como `https://demo.azure.com/hello-world-two`. O segundo aplicativo de demonstração com um título personalizado é retornado, conforme mostrado na saída de exemplo condensada a seguir:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
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

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Excluir as versões com o `helm delete` comando. O exemplo a seguir exclui a implantação de ingresso NGINX e os dois aplicativos exemplo AKS hello world.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Em seguida, remova o repositório do Helm para o aplicativo hello world do AKS:

```console
helm repo remove azure-samples
```

Remova a rota de entrada que direcionava o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml
```

Exclua o certificado de segredo:

```console
kubectl delete secret aks-ingress-tls
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
- [Criar um controlador de entrada que usa uma rede interna, privada e o endereço IP][aks-ingress-internal]
- Criar um controlador de entrada que usa vamos criptografar para gerar automaticamente certificados TLS [com um endereço IP público dinâmico][aks-ingress-tls] or [with a static public IP address][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers