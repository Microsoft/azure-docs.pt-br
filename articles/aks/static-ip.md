---
title: Usar um endereço IP estático com o balanceador de carga do Serviço do Kubernetes do Azure (AKS)
description: Saiba como criar e usar um endereço IP estático com o balanceador de carga do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 03/04/2019
ms.author: mlearned
ms.openlocfilehash: 9e32715766734bcbb150d70aeed2dc5b06a4bcbb
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614461"
---
# <a name="use-a-static-public-ip-address-with-the-azure-kubernetes-service-aks-load-balancer"></a>Usar um endereço IP público estático com o balanceador de carga do AKS (Serviço de Kubernetes do Azure)

Por padrão, o endereço IP público atribuído a um recurso de balanceador de carga criado por um cluster do AKS só é válido durante o tempo de vida do recurso. Se você excluir o serviço de Kubernetes, o balanceador de carga e o endereço IP associados também serão excluídos. Se você quiser atribuir um endereço IP específico ou manter um endereço IP para serviços de Kubernetes reimplantados, crie e use um endereço IP público estático.

Este artigo mostra como criar um endereço IP público estático e atribuí-lo ao serviço de Kubernetes.

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se você precisar um cluster do AKS, consulte o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

Você também precisa da CLI do Azure versão 2.0.59 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [instalar a CLI do Azure][install-azure-cli].

Atualmente, apenas *SKU básico do IP*tem suporte. Trabalho está em andamento para dar suporte a *IP padrão* SKU de recurso. Para obter mais informações, consulte [endereço IP, tipos e métodos de alocação no Azure][ip-sku].

## <a name="create-a-static-ip-address"></a>Criar um endereço IP estático

Quando você cria um endereço IP público estático para uso com o AKS, o recurso de endereço IP deve ser criado no grupo de recursos **node**. Se você deseja separar os recursos, consulte a seção a seguir para [usar um endereço IP estático fora do grupo de recursos de nó](#use-a-static-ip-address-outside-of-the-node-resource-group).

Primeiro, obtenha o nome de grupo de recursos do nó com o [show do az aks][az-aks-show] de comando e adicione o `--query nodeResourceGroup` parâmetro de consulta. O exemplo a seguir obtém o grupo de recursos do nó do nome do cluster do AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora, crie um endereço IP público estático com o [criar ip público do az rede][az-network-public-ip-create] comando. Especifique o nome de grupo de recursos do nó obtido no comando anterior e, em seguida, um nome para o recurso do endereço IP, como *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

O endereço IP é exibido, conforme mostrado na saída de exemplo condensado a seguir:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [...]
  }
}
```

Que mais tarde você pode obter o endereço IP público usando o [lista do az network public-ip][az-network-public-ip-list] comando. Especifique o nome do grupo de recursos do nó e o endereço IP público que você criou e, em seguida, confira o *ipAddress*, conforme mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip show --resource-group MC_myResourceGroup_myAKSCluster_eastus --name myAKSPublicIP --query ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-using-the-static-ip-address"></a>Criar um serviço usando o endereço IP estático

Para criar um serviço com o endereço IP público estático, adicione a propriedade `loadBalancerIP` e o valor do endereço IP público estático no manifesto YAML. Crie um arquivo chamado `load-balancer-service.yaml` e copie no YAML a seguir. Forneça seu próprio endereço IP público criado na etapa anterior.

```yaml
apiVersion: v1
kind: Service
metadata:
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

## <a name="use-a-static-ip-address-outside-of-the-node-resource-group"></a>Use um endereço IP estático fora do grupo de recursos de nó

Com o Kubernetes 1.10 ou posterior, você pode usar um endereço IP estático que é criado fora do grupo de recursos do nó. A entidade de serviço usada pelo cluster do AKS deverá ter permissões delegadas para outro grupo de recursos, conforme mostrado no exemplo a seguir:

```azurecli-interactive
az role assignment create\
    --assignee <SP Client ID> \
    --role "Network Contributor" \
    --scope /subscriptions/<subscription id>/resourceGroups/<resource group name>
```

Para usar um endereço IP fora do grupo de recursos do nó, adicione uma anotação para a definição de serviço. O exemplo a seguir define a anotação para o grupo de recursos chamado *myResourceGroup*. Forneça o nome do seu grupo de recursos:

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

## <a name="troubleshoot"></a>Solução de problemas

Se o endereço IP estático definido na *loadBalancerIP* propriedade do manifesto do serviço Kubernetes não existe ou não tiver sido criada no grupo de recursos de nó e não há delegações adicionais configuradas, o serviço de Balanceador de carga Falha na criação. Para solucionar problemas, examine os eventos de criação de serviço com o [kubectl descrevem][kubectl-describe] comando. Forneça o nome do serviço, conforme especificado no manifesto do YAML, como é mostrado no exemplo a seguir:

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

Para obter controle adicional sobre o tráfego de rede para seus aplicativos, você talvez queira em vez disso [criar um controlador de ingresso][aks-ingress-basic]. You can also [create an ingress controller with a static public IP address][aks-static-ingress].

<!-- LINKS - External -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

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
[ip-sku]: ../virtual-network/virtual-network-ip-addresses-overview-arm.md#sku
