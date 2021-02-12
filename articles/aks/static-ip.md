---
title: Usar IP estático com o balanceador de carga
titleSuffix: Azure Kubernetes Service
description: Saiba como criar e usar um endereço IP estático com o balanceador de carga do AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 11/14/2020
ms.openlocfilehash: 22fd099633556fa9ddce575c2ac238b4950667cb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94651882"
---
# <a name="use-a-static-public-ip-address-and-dns-label-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usar um endereço IP público estático e um rótulo DNS com o balanceador de carga do AKS (serviço de kubernetes do Azure)

Por padrão, o endereço IP público atribuído a um recurso de balanceador de carga criado por um cluster do AKS só é válido durante o tempo de vida do recurso. Se você excluir o serviço de Kubernetes, o balanceador de carga e o endereço IP associados também serão excluídos. Se você quiser atribuir um endereço IP específico ou manter um endereço IP para serviços de Kubernetes reimplantados, crie e use um endereço IP público estático.

Este artigo mostra como criar um endereço IP público estático e atribuí-lo ao serviço de Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.59 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

Este artigo aborda o uso de um IP de SKU *padrão* com um balanceador de carga SKU *padrão* . Para obter mais informações, confira [Tipos de endereço IP e métodos de alocação no Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Crie um endereço IP público estático com o comando [AZ Network Public IP Create][az-network-public-ip-create] . O seguinte cria um recurso de IP estático chamado *myAKSPublicIP* no grupo de recursos *MyResource* Group:

```azurecli-interactive
az network public-ip create \
    --resource-group myResourceGroup \
    --name myAKSPublicIP \
    --sku Standard \
    --allocation-method static
```

> [!NOTE]
> Se você estiver usando um balanceador de carga de SKU *básico* em seu cluster AKs, use *básico* para o parâmetro *SKU* ao definir um IP público. Somente os IPs de SKU *básicos* funcionam com o balanceador de carga de SKU *básico* e somente os IPs de SKU *padrão* funcionam com balanceadores de carga SKU *padrão* . 

O endereço IP é exibido, conforme mostrado na seguinte saída de exemplo condensada:

```json
{
  "publicIp": {
    ...
    "ipAddress": "40.121.183.52",
    ...
  }
}
```

Mais tarde, você poderá obter o endereço IP público usando o comando [az network public-ip list][az-network-public-ip-list]. Especifique o nome do grupo de recursos do nó e o endereço IP público que você criou e, em seguida, confira o *ipAddress*, conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip show --resource-group myResourceGroup --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço usando o endereço IP estático

Antes de criar um serviço, verifique se a entidade de serviço usada pelo cluster AKS tem permissões delegadas para outro grupo de recursos. Por exemplo:

```azurecli-interactive
az role assignment create \
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Como alternativa, você pode usar a identidade gerenciada atribuída pelo sistema para permissões em vez da entidade de serviço. Para obter mais informações, confira [Usar identidades gerenciadas](use-managed-identity.md).

> [!IMPORTANT]
> Se você personalizou seu IP de saída, verifique se a identidade do cluster tem permissões para o IP público de saída e esse IP público de entrada.

Para criar um serviço *Balancer* com o endereço IP público estático, adicione a `loadBalancerIP` propriedade e o valor do endereço IP público estático ao manifesto YAML. Crie um arquivo chamado `load-balancer-service.yaml` e copie no YAML a seguir. Forneça seu próprio endereço IP público criado na etapa anterior. O exemplo a seguir também define a anotação para o grupo de recursos chamado *MyResource* Group. Forneça seu próprio nome de grupo de recursos.

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-resource-group: myResourceGroup
  name: azure-load-balancer
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

Crie o serviço e a implantação com o comando `kubectl apply`.

```console
kubectl apply -f load-balancer-service.yaml
```

## <a name="apply-a-dns-label-to-the-service"></a>Aplicar um rótulo DNS ao serviço

Se o serviço estiver usando um endereço IP público dinâmico ou estático, você poderá usar a anotação de serviço `service.beta.kubernetes.io/azure-dns-label-name` para definir um rótulo DNS voltado ao público. Isso publica um nome de domínio totalmente qualificado para seu serviço usando os servidores DNS públicos do Azure e o domínio de nível superior. O valor da anotação deve ser exclusivo no local do Azure, portanto, é recomendável usar um rótulo suficientemente qualificado.   

Em seguida, o Azure acrescentará automaticamente uma sub-rede padrão, como `<location>.cloudapp.azure.com` (em que local é a região que você selecionou), para o nome que você fornece, para criar o nome DNS totalmente qualificado. Por exemplo:

```yaml
apiVersion: v1
kind: Service
metadata:
  annotations:
    service.beta.kubernetes.io/azure-dns-label-name: myserviceuniquelabel
  name: azure-load-balancer
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-load-balancer
```

> [!NOTE] 
> Para publicar o serviço em seu próprio domínio, consulte [Azure DNS][azure-dns-zone] e o projeto [external-DNS][external-dns] .

## <a name="troubleshoot"></a>Solucionar problemas

Se o endereço IP estático definido na propriedade *loadBalancerIP* do manifesto do serviço kubernetes não existir ou não tiver sido criado no grupo de recursos do nó e nenhuma delegação adicional configurada, a criação do serviço do balanceador de carga falhará. Para solucionar problemas, examine os eventos de criação do serviço com o comando [kubectl describe][kubectl-describe]. Forneça o nome do serviço, conforme especificado no manifesto do YAML, como é mostrado no exemplo a seguir:

```console
kubectl describe service azure-load-balancer
```

As informações sobre o recurso do serviço de Kubernetes são exibidas. Os *Eventos* no final da saída de exemplo a seguir indicam que o *endereço IP fornecido pelo usuário não foi encontrado*. Nesses cenários, verifique se você criou o endereço IP público estático no grupo de recursos do nó e se o endereço IP especificado no manifesto do serviço de Kubernetes está correto.

```
Name:                     azure-load-balancer
Namespace:                default
Labels:                   <none>
Annotations:              <none>
Selector:                 app=azure-load-balancer
Type:                     LoadBalancer
IP:                       10.0.18.125
IP:                       40.121.183.52
Port:                     <unset>  80/TCP
TargetPort:               80/TCP
NodePort:                 <unset>  32582/TCP
Endpoints:                <none>
Session Affinity:         None
External Traffic Policy:  Cluster
Events:
  Type     Reason                      Age               From                Message
  ----     ------                      ----              ----                -------
  Normal   CreatingLoadBalancer        7s (x2 over 22s)  service-controller  Creating load balancer
  Warning  CreatingLoadBalancerFailed  6s (x2 over 12s)  service-controller  Error creating load balancer (will retry): Failed to create load balancer for service default/azure-load-balancer: user supplied IP Address 40.121.183.52 was not found
```

## <a name="next-steps"></a>Próximas etapas

Para obter mais controle sobre o tráfego de rede para seus aplicativos, convém [criar um controlador de entrada][aks-ingress-basic]. Você também pode [criar um controlador de entrada com um endereço IP público estático][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[azure-dns-zone]: https://azure.microsoft.com/services/dns/
[external-dns]: https://github.com/kubernetes-sigs/external-dns

<!-- LINKS - Internal -->
[aks-faq-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[aks-ingress-basic]: ingress-basic.md
[aks-static-ingress]: ingress-static-ip.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[ip-sku]: ../virtual-network/public-ip-addresses.md#sku
