---
title: Criar um balanceador de carga interno
titleSuffix: Azure Kubernetes Service
description: Saiba como criar e usar um balanceador de carga interno para expor seus serviços com o AKS (Serviço de Kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 03/04/2019
ms.openlocfilehash: 4c2c0866aa9a721a73e1eb8fa230f0022cf6b8ca
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102505623"
---
# <a name="use-an-internal-load-balancer-with-azure-kubernetes-service-aks"></a>Usar um balanceador de carga interno com o AKS (Serviço de Kubernetes do Azure)

Para restringir o acesso a seus aplicativos no Serviço de Kubernetes do Azure (AKS), você pode criar e usar um balanceador de carga interno. Um balanceador de carga interno torna um serviço do Kubernetes acessível somente a aplicativos em execução na mesma rede virtual que o cluster do Kubernetes. Este artigo mostra como criar e usar um balanceador de carga interno com o AKS (Serviço de Kubernetes do Azure).

> [!NOTE]
> O Balanceador de carga do Azure está disponível em dois SKUs - *Básico* e *Padrão*. Por padrão, o SKU Standard é usado quando você cria um cluster do AKS.  Ao criar um serviço com o tipo como Balancer, você obterá o mesmo tipo de lb que ao provisionar o cluster. Para obter mais informações, consulte [Comparação de SKU do balanceador de carga do Azure][azure-lb-comparison].

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].

A CLI do Azure versão 2.0.59 ou posterior também precisa estar instalada e configurada. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][install-azure-cli].

A identidade do cluster de cluster AKS precisará de permissão para gerenciar recursos de rede se você usar uma sub-rede ou grupo de recursos existente. Para obter informações, consulte [usar a rede kubenet com seus próprios intervalos de endereços IP no AKs (serviço kubernetes do Azure)][use-kubenet] ou [Configurar a rede CNI do Azure no serviço kubernetes do Azure (AKs)][advanced-networking]. Se você estiver configurando o balanceador de carga para usar um [endereço IP em uma sub-rede diferente][different-subnet], verifique se a identidade do cluster AKs também tem acesso de leitura a essa sub-rede.

Para obter mais informações sobre permissões, confira [Delegar acesso do AKS a outros recursos do Azure][aks-sp].

## <a name="create-an-internal-load-balancer"></a>Criar um balanceador de carga interno

Para criar um balanceador de carga interno, crie um manifesto de serviço denominado `internal-lb.yaml` com o tipo de serviço *LoadBalancer* e a anotação *azure-load-balancer-internal*, conforme mostrado no exemplo a seguir:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

Implante o balanceador de carga interno usando o [kubectl Apply][kubectl-apply] e especifique o nome do seu manifesto YAML:

```console
kubectl apply -f internal-lb.yaml
```

Um balanceador de carga do Azure é criado no grupo de recursos do nó e conectado à mesma rede virtual que o cluster AKS.

Quando você visualiza os detalhes do serviço, o endereço IP do balanceador de carga interno é mostrado na coluna *EXTERNAL-IP*. Nesse contexto, *external* está em relação à interface externa do balanceador de carga, e não ao recebimento de um endereço IP público e externo. Pode levar um minuto ou dois para que o endereço IP seja alterado de *\<pending\>* para um endereço IP interno real, conforme mostrado no exemplo a seguir:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.248.59   10.240.0.7    80:30555/TCP   2m
```

## <a name="specify-an-ip-address"></a>Especificar um endereço IP

Se você quiser usar um endereço IP específico com o balanceador de carga interno, adicione a propriedade *loadBalancerIP* ao manifesto YAML do balanceador de carga. Nesse cenário, o endereço IP especificado deve residir na mesma sub-rede que o cluster AKS e ainda não deve ser atribuído a um recurso. Por exemplo, você não deve usar um endereço IP no intervalo designado para a sub-rede kubernetes.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
spec:
  type: LoadBalancer
  loadBalancerIP: 10.240.0.25
  ports:
  - port: 80
  selector:
    app: internal-app
```

Quando implantado e você exibe os detalhes do serviço, o endereço IP na coluna *external-IP* reflete o endereço IP especificado:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.0.184.168   10.240.0.25   80:30225/TCP   4m
```

Para obter mais informações sobre como configurar o balanceador de carga em uma sub-rede diferente, consulte [especificar uma sub-rede diferente][different-subnet]

## <a name="use-private-networks"></a>Usar redes privadas

Quando você cria seu cluster do AKS, é possível especificar as configurações de rede avançadas. Essa abordagem permite implantar o cluster na rede virtual e nas sub-redes existentes do Azure. Um cenário é implantar o cluster do AKS em uma rede privada conectada ao seu ambiente local e executar serviços acessíveis apenas internamente. Para obter mais informações, consulte configurar suas próprias sub-redes de rede virtual com [Kubenet][use-kubenet] ou [CNI do Azure][advanced-networking].

Não é necessário realizar nenhuma alteração nas etapas anteriores para implantar um balanceador de carga interno em um cluster do AKS que usa uma rede privada. O balanceador de carga é criado no mesmo grupo de recursos que seu cluster do AKS, mas conectado à sua rede virtual e à sub-rede privadas, conforme mostrado no exemplo a seguir:

```
$ kubectl get service internal-app

NAME           TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
internal-app   LoadBalancer   10.1.15.188   10.0.0.35     80:31669/TCP   1m
```

> [!NOTE]
> Talvez seja necessário conceder a identidade do cluster para o cluster AKS a função de *colaborador de rede* para o grupo de recursos em que os recursos da rede virtual do Azure são implantados. Exiba a identidade do cluster com [AZ AKs show][az-aks-show], como `az aks show --resource-group myResourceGroup --name myAKSCluster --query "identity"` . Para criar uma atribuição de função, use o comando [az role assignment create][az-role-assignment-create].

## <a name="specify-a-different-subnet"></a>Especificar uma sub-rede diferente

Para especificar uma sub-rede para o balanceador de carga, adicione a anotação *azure-load-balancer-internal-subnet* ao seu serviço. A sub-rede especificada deve estar na mesma rede virtual que seu cluster do AKS. Quando implantado, endereço *EXTERNAL-IP* do balanceador de carga faz parte da sub-rede especificada.

```yaml
apiVersion: v1
kind: Service
metadata:
  name: internal-app
  annotations:
    service.beta.kubernetes.io/azure-load-balancer-internal: "true"
    service.beta.kubernetes.io/azure-load-balancer-internal-subnet: "apps-subnet"
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: internal-app
```

## <a name="delete-the-load-balancer"></a>Excluir o balanceador de carga

Quando todos os serviços que usam o balanceador de carga interno são excluídos, o próprio balanceador de carga também é excluído.

Você também pode excluir diretamente um serviço como qualquer recurso do Kubernetes, como `kubectl delete service internal-app`, que também exclui o balanceador de carga do Azure subjacente.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre os serviços do Kubernetes na [documentação dos serviços do Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb-comparison]: ../load-balancer/skus.md
[use-kubenet]: configure-kubenet.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[different-subnet]: #specify-a-different-subnet