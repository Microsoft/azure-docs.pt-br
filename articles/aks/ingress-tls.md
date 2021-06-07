---
title: Criar entrada com TLS automático
titleSuffix: Azure Kubernetes Service
description: Saiba como instalar e configurar um controlador de entrada NGINX que usa vamos criptografar para a geração automática de certificado TLS em um cluster AKS (serviço de kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: aa03558196527e320ecba249dd22af19fec7a90c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592329"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Criar um controlador de ingresso HTTPS no Serviço de Kubernetes do Azure (AKS)

Um controlador de entrada é uma parte do software que fornece proxy reverso, roteamento de tráfego configurável e terminação TLS para serviços de Kubernetes. Os recursos de entrada de Kubernetes são usados para configurar as regras de entrada e as rotas para os serviços de Kubernetes individuais. Usando um controlador de entrada e regras de ingresso, um único endereço IP pode ser usado para rotear tráfego a vários serviços em um cluster de Kubernetes.

Este artigo mostra como implantar o [controlador de ingresso NGINX][nginx-ingress] em um Cluster do Serviço de Kubernetes do Azure (AKS0) O projeto [cert-manager][cert-manager] é usado para gerar e configurar automaticamente certificados [Vamos Criptografar][lets-encrypt]. Finalmente, dois aplicativos são executados no cluster do AKS, cada um dos quais é acessível em um único endereço IP.

Você também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede privada interna e um endereço IP][aks-ingress-internal]
- [Criar um controlador de ingresso que usa seus próprios certificados TLS][aks-ingress-own-tls]
- [Crie um controlador de ingresso que use o Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

Este artigo também pressupõe que você tenha [um domínio personalizado][custom-domain] com uma [zona DNS][dns-zone] no mesmo grupo de recursos que o cluster AKs.

Este artigo usa [Helm 3][helm] para instalar o controlador de entrada e o Gerenciador de certificados do nginx. Certifique-se de que você está usando a versão mais recente do Helm e tenha acesso aos repositórios de *entrada-Nginx* e *jetstack* Helm. Para obter instruções de atualização, consulte o [Helm instalar documentos][helm-install]. Para obter mais informações sobre como configurar e usar o Helm, consulte [instalar aplicativos com Helm no serviço kubernetes do Azure (AKs)][use-helm].

Este artigo também requer que você esteja executando o CLI do Azure versão 2.0.64 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Criar um controlador de entrada

Para criar o controlador de entrada, use o `helm` comando para instalar o *Nginx-ingress*. Para redundância adicional, duas réplicas dos controladores de entrada NGINX são implementadas com o parâmetro `--set controller.replicaCount`. Para se beneficiar totalmente da execução de réplicas do controlador de entrada, verifique se há mais de um nó em seu cluster AKS.

O controlador de entrada também precisa ser agendado em um nó do Linux. Os nós do Windows Server não devem executar o controlador de entrada. Um seletor de nó é especificado usando o parâmetro `--set nodeSelector` para instruir o agendador do Kubernetes a executar o controlador de entrada NGINX em um nó baseado em Linux.

> [!TIP]
> O exemplo a seguir cria um namespace kubernetes para os recursos de entrada chamados *ingress-Basic*. Especifique um namespace para seu próprio ambiente, conforme necessário.

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
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Durante a instalação, um endereço IP público do Azure é criado para o controlador de entrada. Esse endereço IP público é estático por todo o período de vida do controlador de entrada. Se você excluir o controlador de entrada, a atribuição de endereço IP pública será perdida. Se, em seguida, você criar outro controlador de entrada, um novo endereço IP público será atribuído. Se você deseja reter o uso do endereço IP público, você pode [criar um controlador de entrada com um endereço IP público estático][aks-ingress-static-tls].

Para obter o endereço IP público, use `kubectl get service` o comando de serviço kubectl get. Pode levar alguns minutos até que o endereço IP seja atribuído ao serviço.

```console
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Nenhuma regra de entrada foi criada ainda. Se você navegar até o endereço IP público, a página 404 padrão do controlador de ingresso NGINX será exibida.

## <a name="add-an-a-record-to-your-dns-zone"></a>Adicionar um registro a à zona DNS

Adicione um *registro a* à zona DNS com o endereço IP externo do serviço Nginx usando [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Opcionalmente, você pode configurar um FQDN para o endereço IP do controlador de entrada em vez de um domínio personalizado. Observe que esse exemplo é para um shell bash.
> 
> ```bash
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Instalar o cert-manager

O controlador de entrada NGINX dá suporte para terminação TLS. Há várias maneiras de recuperar e configurar certificados para HTTPS. Este artigo demonstra como usar o [manager cert][cert-manager], que fornece a geração automática do certificado [Vamos Criptografar][lets-encrypt] e funcionalidade de gerenciamento.

Para instalar o controlador CERT-Manager:

```console
# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install cert-manager jetstack/cert-manager \
  --namespace ingress-basic \
  --set installCRDs=true \
  --set nodeSelector."kubernetes\.io/os"=linux \
  --set webhook.nodeSelector."kubernetes\.io/os"=linux \
  --set cainjector.nodeSelector."kubernetes\.io/os"=linux
```

Para obter mais informações sobre a configuração docert-manager, consulte o projeto [cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Criar o emissor de cluster de CA

Antes dos certificados poderem ser emitidos, o cert-manager requer um [Emissor][cert-manager-issuer] ou recurso [ClusterIssuer][cert-manager-cluster-issuer]. Esses recursos Kubernetes são idênticos na funcionalidade porém `Issuer` funcionam em um único namespace onde `ClusterIssuer` funciona em todos os namespaces. Para obter mais informações, consulte a [documentação do emissor do cert-manager][cert-manager-issuer].

Crie um emissor de cluster, como `cluster-issuer.yaml`, usando o manifesto a seguir. Atualize o endereço de email com um endereço válido da sua organização:

```yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
          podTemplate:
            spec:
              nodeSelector:
                "kubernetes.io/os": linux
```

Para criar a coleção, use o `kubectl apply`comando: 

```console
kubectl apply -f cluster-issuer.yaml
```

## <a name="run-demo-applications"></a>Executar aplicativos de demonstração

Um controlador de entrada e uma solução de gerenciamento de certificados foi configurado. Agora, vamos executar duas demonstrações de aplicativos no cluster do AKS. Neste exemplo, Helm é usado para implantar duas instâncias de um aplicativo *Hello World* simples.

Para ver o controlador de entrada em ação, execute dois aplicativos de demonstração no cluster AKS. Neste exemplo, você usa o `kubectl apply` para implantar duas instâncias de um aplicativo *Hello World* simples.

Crie um arquivo *AKs-HelloWorld-One. YAML* e copie no seguinte exemplo YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-one
  template:
    metadata:
      labels:
        app: aks-helloworld-one
    spec:
      containers:
      - name: aks-helloworld-one
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
  name: aks-helloworld-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-one
```

Crie um arquivo *AKs-HelloWorld-Two. YAML* e copie no seguinte exemplo YAML:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: aks-helloworld-two
spec:
  replicas: 1
  selector:
    matchLabels:
      app: aks-helloworld-two
  template:
    metadata:
      labels:
        app: aks-helloworld-two
    spec:
      containers:
      - name: aks-helloworld-two
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
  name: aks-helloworld-two
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: aks-helloworld-two
```

Execute os dois aplicativos de demonstração usando `kubectl apply` :

```console
kubectl apply -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl apply -f aks-helloworld-two.yaml --namespace ingress-basic
```

## <a name="create-an-ingress-route"></a>Criar rota de entrada

Ambos os aplicativos agora estão em execução no cluster do Kubernetes. No entanto, eles são configurados com um serviço do tipo `ClusterIP` e não podem ser acessados pela Internet. Para torná-los disponíveis publicamente, crie um recurso de entrada de Kubernetes. O recurso de entrada configura as regras que roteiam o tráfego para um dos dois aplicativos.

No exemplo a seguir, o tráfego para o endereço *Hello-World-ingress. MY_CUSTOM_DOMAIN* é roteado para o serviço *AKs-HelloWorld-One* . Tráfego para o endereço *Hello-World-ingress. MY_CUSTOM_DOMAIN/Hello-World-Two* é roteado para o serviço *AKs-HelloWorld-dois* . Tráfego para *Hello-World-Ingres. MY_CUSTOM_DOMAIN/static* é roteado para o serviço chamado *AKs-HelloWorld-One* para ativos estáticos.

> [!NOTE]
> Se você configurou um FQDN para o endereço IP do controlador de entrada em vez de um domínio personalizado, use o FQDN em vez de *Hello-World-Ingres. MY_CUSTOM_DOMAIN*. Por exemplo, se o seu FQDN for *demo-AKs-ingress.eastus.cloudapp.Azure.com*, substitua *Hello-World-Ingres. MY_CUSTOM_DOMAIN* com *demo-AKs-ingress.eastus.cloudapp.Azure.com* no `hello-world-ingress.yaml` .

Crie um arquivo chamado `hello-world-ingress.yaml` usando o exemplo abaixo de YAML. Atualize o *dnsNames* e *domínios* para o nome DNS que você criou na etapa anterior.

```yaml
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /hello-world-one(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /(.*)
---
apiVersion: networking.k8s.io/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    nginx.ingress.kubernetes.io/use-regex: "true"
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld-one
          servicePort: 80
        path: /static(/|$)(.*)
```

Crie o recurso de entrada usando o `kubectl apply` comando.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Verifique se um objeto de certificado foi criado

Em seguida, um recurso de certificado deve ser criado. O recurso de certificado define o certificado X.509 desejado. Para obter mais informações, consulte [certificados do Gerenciador de][cert-manager-certificates]certificados. O Gerenciador de certificados criou automaticamente um objeto de certificado para você usando o Shim de entrada, que é implantado automaticamente com o CERT-Manager desde o v 0.2.2. Para obter mais informações, consulte a [Documentação do ingress-shim][ingress-shim].

Para verificar se o certificado foi criado com êxito, use o `kubectl get certificate --namespace ingress-basic` comando e verifique se *pronto* é *verdadeiro*, o que pode levar vários minutos.

```console
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Testar a configuração de entrada

Abra um navegador da Web para *Hello-World-ingress. MY_CUSTOM_DOMAIN* do controlador de entrada do kubernetes. Observe que você está Redirecionado para usar HTTPS e o certificado é confiável e o aplicativo de demonstração é mostrado no navegador da Web. Adicione o caminho */Hello-World-Two* e observe que o segundo aplicativo de demonstração com o título personalizado é mostrado.

## <a name="clean-up-resources"></a>Limpar recursos

Este artigo usou o Helm para instalar os componentes de ingresso, os certificados e os aplicativos de exemplo. Quando você implanta um gráfico Helm, vários recursos do Kubernetes são criados. Esses recursos incluem pods, implantações e serviços. Para limpar esses recursos, você pode excluir o namespace de exemplo inteiro ou os recursos individuais.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Excluir o namespace de exemplo e todos os recursos

Para excluir o namespace de exemplo inteiro, use o `kubectl delete` comando e especifique o nome do namespace. Todos os recursos no namespace são excluídos.

```console
kubectl delete namespace ingress-basic
```

### <a name="delete-resources-individually"></a>Excluir recursos individualmente

Como alternativa, uma abordagem mais granular é excluir os recursos individuais criados. Primeiro, remova os recursos do emissor do cluster:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Liste as versões Helm com o `helm list` comando. Procure gráficos chamados *Nginx* e *CERT-Manager*, conforme mostrado na seguinte saída de exemplo:

```console
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Desinstale as versões com o `helm uninstall` comando. O exemplo a seguir desinstala as implantações NGINX ingress e CERT-Manager.

```console
$ helm uninstall cert-manager nginx --namespace ingress-basic

release "cert-manager" uninstalled
release "nginx" uninstalled
```

Em seguida, remova os dois aplicativos de exemplo:

```console
kubectl delete -f aks-helloworld-one.yaml --namespace ingress-basic
kubectl delete -f aks-helloworld-two.yaml --namespace ingress-basic
```

Remova a rota de entrada que direcionava o tráfego para os aplicativos de exemplo:

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Por fim, você pode excluir o namespace em si. Use o `kubectl delete` comando e especifique o nome do namespace:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Próximas etapas

Este artigo incluído alguns componentes externos no AKS. Para saber mais sobre esses componentes, consulte as seguintes páginas do projeto:

- [CLI do Helm][helm-cli]
- [Controlador de entrada NGINX ][nginx-ingress]
- [cert-manager][cert-manager]

Você também pode:

- [Criar um controlador de entrada básico com conectividade de rede externa][aks-ingress-basic]
- [Habilitar o complemento de roteamento de aplicativo HTTP][aks-http-app-routing]
- [Criar um controlador de entrada que usa uma rede privada interna e um endereço IP][aks-ingress-internal]
- [Criar um controlador de ingresso que usa seus próprios certificados TLS][aks-ingress-own-tls]
- [Crie um controlador de ingresso que use o Let's Encrypt para gerar automaticamente certificados TLS com um endereço IP público estático][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm]: https://helm.sh/
[helm-cli]: ./kubernetes-helm.md
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
