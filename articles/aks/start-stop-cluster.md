---
title: Iniciar e parar um serviço de kubernetes do Azure (AKS)
description: Saiba como parar ou iniciar um cluster do AKS (serviço kubernetes do Azure).
services: container-service
ms.topic: article
ms.date: 09/24/2020
author: palma21
ms.openlocfilehash: 026da4237fe14726766b265e55930346293c71df
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662890"
---
# <a name="stop-and-start-an-azure-kubernetes-service-aks-cluster-preview"></a>Parar e iniciar um cluster do AKS (serviço kubernetes do Azure) (visualização)

Suas cargas de trabalho do AKS podem não precisar ser executadas continuamente, por exemplo, um cluster de desenvolvimento que é usado somente durante o horário comercial. Isso leva a tempos em que o cluster AKS (serviço kubernetes do Azure) pode estar ocioso, executando não mais do que os componentes do sistema. Você pode reduzir a superfície do cluster [dimensionando todos os `User` pools de nós para 0](scale-cluster.md#scale-user-node-pools-to-0), mas seu [ `System` pool](use-system-pools.md) ainda é necessário para executar os componentes do sistema enquanto o cluster está em execução. Para otimizar ainda mais os custos durante esses períodos, você pode desativar completamente (parar) o cluster. Essa ação interromperá o plano de controle e os nós de agente, permitindo que você economize em todos os custos de computação, mantendo todos os seus objetos e o estado do cluster armazenados para quando você iniciá-lo novamente. Em seguida, você pode selecionar o ponto em que parou após um final de semana ou para que o cluster seja executado somente enquanto você executa seus trabalhos em lotes.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Antes de começar

Este artigo considera que já existe um cluster do AKS. Se precisar de um cluster do AKS, veja o guia de início rápido do AKS [usando a CLI do Azure][aks-quickstart-cli] ou [usando o portal do Azure][aks-quickstart-portal].


### <a name="limitations"></a>Limitações

Ao usar o recurso iniciar/parar do cluster, as seguintes restrições se aplicam:

- Este recurso só tem suporte para clusters com backup de conjuntos de dimensionamento de máquinas virtuais.
- O estado do cluster de um cluster AKS interrompido é preservado por até 12 meses. Se o cluster for interrompido por mais de 12 meses, o estado do cluster não poderá ser recuperado. Para obter mais informações, consulte as [políticas de suporte do AKS](support-policies.md).
- Durante a versão prévia, você precisa parar o cluster de dimensionamento (CA) de modo a interromper antes de tentar parar o cluster.
- Você só pode iniciar ou excluir um cluster AKS interrompido. Para executar qualquer operação como escala ou atualização, inicie o cluster primeiro.

### <a name="install-the-aks-preview-azure-cli"></a>Instalar o `aks-preview` CLI do Azure 

Você também precisa do *AKs-preview* CLI do Azure versão 0.4.64 ou posterior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] . Ou instale todas as atualizações disponíveis usando o comando [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
``` 

### <a name="register-the-startstoppreview-preview-feature"></a>Registrar o `StartStopPreview` recurso de visualização

Para usar o recurso iniciar/parar cluster, você deve habilitar o `StartStopPreview` sinalizador de recurso em sua assinatura.

Registre o `StartStopPreview` sinalizador de recurso usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "StartStopPreview"
```

Demora alguns minutos para o status exibir *Registrado*. Verifique o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/StartStopPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="stop-an-aks-cluster"></a>Parar um cluster AKS

Você pode usar o `az aks stop` comando para interromper um plano de controle e nós de um cluster AKS em execução. O exemplo a seguir interrompe um cluster chamado *myAKSCluster*:

```azurecli-interactive
az aks stop --name myAKSCluster --resource-group myResourceGroup
```

Você pode verificar quando o cluster é interrompido usando o comando [AZ AKs show][az-aks-show] e confirmando os `powerState` programas como `Stopped` na saída abaixo:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Stopped"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se o `provisioningState` Mostrar `Stopping` que significa que o cluster ainda não foi totalmente interrompido.

> [!IMPORTANT]
> Se você estiver usando [orçamentos de interrupção de Pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) , a operação de parada poderá levar mais tempo, uma vez que o processo de drenagem demorará mais para ser concluído.


## <a name="start-an-aks-cluster"></a>Iniciar um cluster AKS

Você pode usar o `az aks start` comando para iniciar um plano de controle e nós do cluster AKs interrompido. O cluster é reiniciado com o estado do plano de controle anterior e o número de nós do agente.  
O exemplo a seguir inicia um cluster chamado *myAKSCluster*:

```azurecli-interactive
az aks start --name myAKSCluster --resource-group myResourceGroup
```

Você pode verificar quando o cluster foi iniciado usando o comando [AZ AKs show][az-aks-show] e confirmando os `powerState` programas `Running` como na saída abaixo:

```json
{
[...]
  "nodeResourceGroup": "MC_myResourceGroup_myAKSCluster_westus2",
  "powerState":{
    "code":"Running"
  },
  "privateFqdn": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
[...]
}
```

Se o `provisioningState` Mostrar `Starting` que significa que o cluster ainda não foi totalmente iniciado.


## <a name="next-steps"></a>Próximas etapas

- Para saber como dimensionar os `User` pools para 0, consulte [dimensionar `User` pools para 0](scale-cluster.md#scale-user-node-pools-to-0).
- Para saber como economizar custos usando instâncias de spot, consulte [Adicionar um pool de nós de spot a AKs](spot-node-pool.md).
- Para saber mais sobre as políticas de suporte do AKS, consulte [políticas de suporte do AKS](support-policies.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli&preserve-view=true
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest#az-extension-add&preserve-view=true
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest#az-extension-update&preserve-view=true
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest#az-feature-register&preserve-view=true
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[az-aks-show]: /cli/azure/aks?view=azure-cli-latest#az_aks_show
