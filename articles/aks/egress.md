---
title: Usar o IP estático para o tráfego de saída
titleSuffix: Azure Kubernetes Service
description: Saiba como criar e usar um endereço IP público estático para o tráfego de saída em um cluster do AKS (Serviço de Kubernetes do Azure)
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: e1f81bf4c4d35108557449a8bebd126bdf744191
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104592363"
---
# <a name="use-a-static-public-ip-address-for-egress-traffic-with-a-basic-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Usar um endereço IP público estático para o tráfego de saída com um balanceador de carga de SKU *básico* no serviço kubernetes do Azure (AKs)

Por padrão, o endereço IP de saída de um cluster do AKS (Serviço de Kubernetes do Azure) é atribuído aleatoriamente. Essa configuração não é ideal quando você precisa identificar um endereço IP para acesso a serviços externos, por exemplo. Em vez disso, talvez seja necessário atribuir um endereço IP estático a ser adicionado a umalist de permissão para acesso ao serviço.

Este artigo mostra como criar e usar um endereço IP público estático para ser usado com o tráfego de saída em um cluster do AKS.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você esteja usando o Load Balancer básico do Azure.  É recomendável usar o [Standard Load Balancer do Azure](../load-balancer/load-balancer-overview.md), e você pode usar recursos mais avançados para [controlar o tráfego de saída AKs](./limit-egress-traffic.md).

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.59 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

> [!IMPORTANT]
> Este artigo usa o balanceador de carga de SKU *básico* com um único pool de nós. Essa configuração não está disponível para vários pools de nós, pois o balanceador de carga de SKU *básico* não tem suporte com vários pools de nós. Consulte [usar um Standard Load Balancer público no AKs (serviço kubernetes do Azure)][slb] para obter mais detalhes sobre como usar o balanceador de carga SKU *padrão* .

## <a name="egress-traffic-overview"></a>Visão geral do tráfego de saída

O tráfego de saída de um cluster do AKS segue as [Convenções do Azure Load Balancer][outbound-connections]. Até que o primeiro serviço de Kubernetes do tipo `LoadBalancer` seja criado, os nós do agente em um cluster do AKS não fazem parte de nenhum pool do Azure Load Balancer. Nessa configuração, os nós não têm nenhum endereço IP público no nível da instância. O Azure converte o fluxo de saída para um endereço IP de origem pública que não é configurável ou determinístico.

Após o serviço Kubernetes do tipo `LoadBalancer` ser criado, os nós do agente são adicionados a um pool do Azure Load Balancer. O Load Balancer Basic escolhe um único front-end para ser utilizado em fluxos de saída quando vários front-ends de IP (público) forem candidatos para fluxos de saída. Essa seleção não é configurável e você deverá considerar o algoritmo de seleção como aleatório. Esse endereço IP público só é válido durante o tempo de vida do recurso. Se você excluir o serviço de Kubernetes LoadBalancer, o balanceador de carga e o endereço IP associados também serão excluídos. Se você quiser atribuir um endereço IP específico ou manter um endereço IP para serviços de Kubernetes reimplantados, crie e use um endereço IP público estático.

## <a name="create-a-static-public-ip"></a>Criar um IP público estático

Obtenha o nome do grupo de recursos com o comando [az aks show][az-aks-show] e adicione o parâmetro de consulta `--query nodeResourceGroup`. O exemplo a seguir obtém o grupo de recursos do nó do nome do cluster do AKS *myAKSCluster* no nome do grupo de recursos *myResourceGroup*:

```azurecli-interactive
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query nodeResourceGroup -o tsv

MC_myResourceGroup_myAKSCluster_eastus
```

Agora, crie um endereço IP público estático com o comando [az network public ip create][az-network-public-ip-create]. Especifique o nome de grupo de recursos do nó obtido no comando anterior e, em seguida, um nome para o recurso do endereço IP, como *myAKSPublicIP*:

```azurecli-interactive
az network public-ip create \
    --resource-group MC_myResourceGroup_myAKSCluster_eastus \
    --name myAKSPublicIP \
    --allocation-method static
```

O endereço IP é mostrado, como na saída de exemplo reduzida a seguir:

```json
{
  "publicIp": {
    "dnsSettings": null,
    "etag": "W/\"6b6fb15c-5281-4f64-b332-8f68f46e1358\"",
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/MC_myResourceGroup_myAKSCluster_eastus/providers/Microsoft.Network/publicIPAddresses/myAKSPublicIP",
    "idleTimeoutInMinutes": 4,
    "ipAddress": "40.121.183.52",
    [..]
  }
```

Mais tarde, você poderá obter o endereço IP público usando o comando [az network public-ip list][az-network-public-ip-list]. Especifique o nome do grupo de recursos do nó e, em seguida, confira o *ipAddress* como é mostrado no exemplo a seguir:

```azurecli-interactive
$ az network public-ip list --resource-group MC_myResourceGroup_myAKSCluster_eastus --query [0].ipAddress --output tsv

40.121.183.52
```

## <a name="create-a-service-with-the-static-ip"></a>Criar um serviço com o IP estático

Para criar um serviço com o endereço IP público estático, adicione a propriedade `loadBalancerIP` e o valor do endereço IP público estático no manifesto YAML. Crie um arquivo chamado `egress-service.yaml` e copie no YAML a seguir. Forneça seu próprio endereço IP público criado na etapa anterior.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-egress
spec:
  loadBalancerIP: 40.121.183.52
  type: LoadBalancer
  ports:
  - port: 80
```

Crie o serviço e a implantação com o comando `kubectl apply`.

```console
kubectl apply -f egress-service.yaml
```

Esse serviço configura um novo IP de front-end no Azure Load Balancer. Se você não tiver nenhum outro IP configurado, **todo** tráfego de saída deverá usar esse endereço. Quando vários endereços são configurados no Azure Load Balancer, qualquer um desses endereços IP públicos é um candidato para fluxos de saída e um é selecionado aleatoriamente.

## <a name="verify-egress-address"></a>Verificar o endereço de saída

Para verificar se o endereço IP público estático está sendo usado, você pode usar o serviço de pesquisa de DNS, como `checkip.dyndns.org`.

Inicie e anexe a um pod *Debian* básico:

```console
kubectl run -it --rm aks-ip --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
```

Para acessar um site de dentro do contêiner, use `apt-get` para instalar o `curl` no contêiner.

```console
apt-get update && apt-get install curl -y
```

Agora, usaremos o curl para acessar o site *checkip.dyndns.org*. O endereço IP de saída é mostrado, conforme exibido na saída de exemplo a seguir. Esse endereço IP corresponde ao endereço IP público estático criado e definido para o serviço loadBalancer:

```console
$ curl -s checkip.dyndns.org

<html><head><title>Current IP Check</title></head><body>Current IP Address: 40.121.183.52</body></html>
```

## <a name="next-steps"></a>Próximas etapas

Para evitar que vários endereços IP públicos sejam mantidos no Azure Load Balancer, é possível usar um controlador de entrada. Os controladores de entrada fornecem benefícios adicionais como terminação SSL/TLS, o suporte para regravações de URI e a criptografia SSL/TLS upstream. Para obter mais informações, veja [Criação de um controlador de entrada básico no AKS][ingress-aks-cluster].

<!-- LINKS - internal -->
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[az-network-public-ip-list]: /cli/azure/network/public-ip#az-network-public-ip-list
[az-aks-show]: /cli/azure/aks#az-aks-show
[azure-cli-install]: /cli/azure/install-azure-cli
[ingress-aks-cluster]: ./ingress-basic.md
[outbound-connections]: ../load-balancer/load-balancer-outbound-connections.md#scenarios
[public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[slb]: load-balancer-standard.md
