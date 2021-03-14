---
title: Usar um controlador de entrada do traefik personalizado e configurar o HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Saiba como configurar Azure Dev Spaces para usar um controlador de entrada do traefik personalizado e configurar o HTTPS usando esse controlador de entrada
keywords: Docker, Kubernetes, Azure, AKS, Serviço de Kubernetes do Azure, contêineres, Helm, malha de serviço, roteamento de malha de serviço, kubectl, k8s
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: 036725f3c1eb909407e157d33ece05b1f55213ce
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204092"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Usar um controlador de entrada do traefik personalizado e configurar o HTTPS

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Este artigo mostra como configurar Azure Dev Spaces para usar um controlador de entrada do traefik personalizado. Este artigo também mostra como configurar esse controlador de entrada personalizado para usar HTTPS.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Se você não tiver uma, poderá [criar uma conta gratuita][azure-account-create].
* A [CLI do Azure][az-cli] instalada.
* [Cluster do serviço de kubernetes do Azure (AKS) com Azure Dev Spaces habilitado] [QS-CLI].
* [kubectl][kubectl] instalado.
* [Helm 3 instalado][helm-installed].
* [Um domínio personalizado][custom-domain] com uma [zona DNS][dns-zone]. Este artigo pressupõe que o domínio personalizado e a zona DNS estejam no mesmo grupo de recursos que o cluster AKS, mas é possível usar um domínio personalizado e uma zona DNS em um grupo de recursos diferente.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Configurar um controlador de entrada do traefik personalizado

Conecte-se ao cluster usando o [kubectl][kubectl], o cliente de linha de comando do kubernetes. Para configurar o `kubectl` para se conectar ao cluster do Kubernetes, use o comando [az aks get-credentials][az-aks-get-credentials]. Este comando baixa as credenciais e configura a CLI do Kubernetes para usá-las.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Para verificar a conexão com o cluster, use o comando [kubectl get][kubectl-get] para retornar uma lista dos nós de cluster.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Adicione o [repositório Helm estável oficial][helm-stable-repo], que contém o gráfico Helm do controlador de entrada traefik.

```console
helm repo add stable https://charts.helm.sh/stable
```

Crie um namespace kubernetes para o controlador de entrada traefik e instale-o usando `helm` .

> [!NOTE]
> Se o cluster AKS não tiver o RBAC do kubernetes habilitado, remova o parâmetro *--set RBAC. Enabled = true* .

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> O exemplo acima cria um ponto de extremidade público para seu controlador de entrada. Se você precisar usar um ponto de extremidade privado para seu controlador de entrada, adicione o *--Set Service. Annotations. " Service \\ . beta \\ . kubernetes \\ . Io/Azure-Load-balancer-interno "= true* parâmetro para o comando de *instalação Helm* .
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Esse ponto de extremidade privado é exposto na rede virtual onde o cluster AKS é implantado.

Obtenha o endereço IP do serviço do controlador de entrada traefik usando o [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

A saída de exemplo mostra os endereços IP para todos os serviços no espaço de nome *traefik* .

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adicione um *registro a* à zona DNS com o endereço IP externo do serviço traefik usando [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

O exemplo acima adiciona um *registro a* à zona DNS *MY_CUSTOM_DOMAIN* .

Neste artigo, você usa o [Aplicativo de exemplo de Compartilhamento de Bicicletas do Azure Dev Spaces](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) para demonstrar o uso do Azure Dev Spaces. Clone o aplicativo do GitHub e navegue para seu diretório:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Abra [Values. YAML][values-yaml] e faça as seguintes atualizações:
* Substitua todas as instâncias de *<REPLACE_ME_WITH_HOST_SUFFIX>* com *traefik. MY_CUSTOM_DOMAIN* usando seu domínio para *MY_CUSTOM_DOMAIN*. 
* Substitua *kubernetes.Io/ingress.class: traefik-azds # dev Spaces – specific* com *kubernetes.Io/ingress.class: Traefik # entrada personalizada*. 

Abaixo está um exemplo de um `values.yaml` arquivo atualizado:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Salve suas alterações e feche o arquivo.

Crie o espaço de *desenvolvimento* com o aplicativo de exemplo usando `azds space select` .

```console
azds space select -n dev -y
```

Implante o aplicativo de exemplo usando `helm install` .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

O exemplo acima implanta o aplicativo de exemplo no namespace do *dev* .

Exiba as URLs para acessar o aplicativo de exemplo usando `azds list-uris` .

```console
azds list-uris
```

A saída abaixo mostra as URLs de exemplo de `azds list-uris` .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue até o serviço *bikesharingweb* abrindo a URL pública com o comando `azds list-uris`. No exemplo acima, a URL pública para o serviço *bikesharingweb* é `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

> [!NOTE]
> Se você vir uma página de erro em vez do serviço *bikesharingweb* , verifique se **você atualizou** a anotação *kubernetes.Io/ingress.Class* e o host no arquivo *Values. YAML* .

Use o `azds space select` comando para criar um espaço filho em *dev* e liste as URLs para acessar o espaço de desenvolvimento filho.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

A saída abaixo mostra as URLs de exemplo de `azds list-uris` para acessar o aplicativo de exemplo no espaço de desenvolvimento de *azureuser1* filho.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Navegue até o serviço *bikesharingweb* no espaço de desenvolvimento filho do *AZUREUSER1* abrindo a URL pública do `azds list-uris` comando. No exemplo acima, a URL pública para o serviço *bikesharingweb* no espaço de desenvolvimento filho *azureuser1* é `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/` .

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Configurar o controlador de entrada traefik para usar HTTPS

Use o [Gerenciador de certificados][cert-manager] para automatizar o gerenciamento do certificado TLS ao configurar o controlador de entrada do traefik para usar HTTPS. Use `helm` para instalar o gráfico *CertManager* .

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Crie um `letsencrypt-clusterissuer.yaml` arquivo e atualize o campo de email com seu endereço de email.

```yaml
apiVersion: cert-manager.io/v1alpha2
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
            class: traefik
```

> [!NOTE]
> Para teste, também há um [servidor de preparo][letsencrypt-staging-issuer] que você pode usar para seu *ClusterIssuer*.

Use `kubectl` para aplicar `letsencrypt-clusterissuer.yaml` .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Remova os *traefik* *ClusterRole* e *ClusterRoleBinding* anteriores e, em seguida, atualize traefik para usar HTTPS usando `helm` .

> [!NOTE]
> Se o cluster AKS não tiver o RBAC do kubernetes habilitado, remova o parâmetro *--set RBAC. Enabled = true* .

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Obtenha o endereço IP atualizado do serviço do controlador de entrada do traefik usando o [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

A saída de exemplo mostra os endereços IP para todos os serviços no espaço de nome *traefik* .

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Adicione um *registro a* à zona DNS com o novo endereço IP externo do serviço traefik usando [AZ Network DNS Record-set a Add-Record][az-network-dns-record-set-a-add-record] e remova o registro *a anterior usando* [AZ Network DNS Record-set a remove-Record][az-network-dns-record-set-a-remove-record].

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

O exemplo acima *atualiza o registro a na* zona DNS *MY_CUSTOM_DOMAIN* para usar *PREVIOUS_EXTERNAL_IP*.

Atualize [Values. YAML][values-yaml] para incluir os detalhes de uso de *CERT-Manager* e HTTPS. Abaixo está um exemplo de um `values.yaml` arquivo atualizado:

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Atualize o aplicativo de exemplo usando `helm` :

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Navegue até o aplicativo de exemplo no espaço filho *dev/azureuser1* e observe que você é redirecionado para usar HTTPS.

> [!IMPORTANT]
> Pode levar 30 minutos ou mais para que as alterações de DNS sejam concluídas e o aplicativo de exemplo esteja acessível.

Observe também que a página é carregada, mas o navegador mostra alguns erros. Abrir o console do navegador mostra o erro relacionado a uma página HTTPS tentando carregar recursos HTTP. Por exemplo: 

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Para corrigir esse erro, atualize [BikeSharingWeb/azds. YAML][azds-yaml] para usar *traefik* para *kubernetes.Io/ingress.Class* e seu domínio personalizado para *$ (hostSuffix)*. Por exemplo: 

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Atualize [BikeSharingWeb/package.js][package-json] com uma dependência para o pacote de *URL* .

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Atualize o método *getApiHostAsync* em [BikeSharingWeb/lib/helpers.js][helpers-js] para usar https:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Navegue até o `BikeSharingWeb` diretório e use `azds up` para executar o serviço BikeSharingWeb atualizado.

```console
cd ../BikeSharingWeb/
azds up
```

Navegue até o aplicativo de exemplo no espaço filho *dev/azureuser1* e observe que você é redirecionado para usar HTTPS sem erros.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre como funciona o Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Como o Azure Dev Spaces funciona](../how-dev-spaces-works.md)


[az-cli]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-an-app-service-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml
