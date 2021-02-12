---
title: Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)
description: Saiba como atualizar um cluster do AKS (serviço kubernetes do Azure) para obter os recursos e as atualizações de segurança mais recentes.
services: container-service
ms.topic: article
ms.date: 12/17/2020
ms.openlocfilehash: 1d3c275758a1e241a531b65d1897903153efab94
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567460"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Atualizar um cluster do Serviço de Kubernetes do Azure (AKS)

Parte do ciclo de vida do cluster AKS envolve a execução de atualizações periódicas para a versão mais recente do kubernetes. É importante que você aplique as últimas versões de segurança ou atualize para obter os recursos mais recentes. Este artigo mostra como atualizar os componentes mestres ou um único pool de nós padrão em um cluster AKS.

Para clusters AKS que usam vários pools de nó ou nós do Windows Server, consulte [atualizar um pool de nós em AKs][nodepool-upgrade].

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.65 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

> [!WARNING]
> Uma atualização do cluster AKS dispara um Cordon e dreno de seus nós. Se você tiver uma cota de computação baixa disponível, a atualização poderá falhar. Para obter mais informações, consulte [aumentar cotas](../azure-portal/supportability/resource-manager-core-quotas-request.md)

## <a name="check-for-available-aks-cluster-upgrades"></a>Verificação de atualizações disponíveis do cluster do AKS

Para verificar quais versões do Kubernetes estão disponíveis para seu cluster, use o comando [az aks get-upgrades][az-aks-get-upgrades]. O exemplo a seguir verifica se há atualizações disponíveis para *myAKSCluster* no *MyResource*:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> Quando você atualiza um cluster AKS com suporte, as versões secundárias do kubernetes não podem ser ignoradas. Todas as atualizações devem ser executadas sequencialmente pelo número de versão principal. Por exemplo, as atualizações entre *1.14. x*  ->  *1.15. x* ou *1.15. x*  ->  *1.16.* x são permitidas, no entanto, *1.14. x*  ->  *1.16. x* não é permitido. 
> > Ignorar várias versões só pode ser feito ao atualizar de uma _versão sem suporte_ de volta para uma _versão com suporte_. Por exemplo, uma atualização de um *1,10. x* sem suporte > um *1.15. x* com suporte pode ser concluído.

A saída de exemplo a seguir mostra que o cluster pode ser atualizado para as versões *1.19.1* e *1.19.3*:

```console
Name     ResourceGroup    MasterVersion    Upgrades
-------  ---------------  ---------------  --------------
default  myResourceGroup  1.18.10          1.19.1, 1.19.3
```

Se nenhuma atualização estiver disponível, você receberá a mensagem:

```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade"></a>Personalizar a atualização do surto do nó

> [!Important]
> As sobretensões de nó exigem a cota de assinatura para a contagem máxima de surtos solicitada para cada operação de atualização. Por exemplo, um cluster que tem cinco pools de nós, cada um com uma contagem de quatro nós, tem um total de 20 nós. Se cada pool de nós tiver um valor máximo de surto de 50%, a cota de computação e IP adicional de 10 nós (2 nós * 5 pools) será necessária para concluir a atualização.
>
> Se estiver usando o Azure CNI, valide se há IPs disponíveis na sub-rede também para [atender aos requisitos de IP do Azure CNI](configure-azure-cni.md).

Por padrão, o AKS configura atualizações para surtos com um nó adicional. Um valor padrão de um para as configurações de surto máximo permitirá que o AKS Minimize a interrupção da carga de trabalho criando um nó adicional antes de Cordon/dreno de aplicativos existentes para substituir um nó com controle de versão mais antigo. O valor máximo de surto pode ser personalizado por pool de nós para permitir uma compensação entre a velocidade de atualização e a interrupção da atualização. Ao aumentar o valor máximo de surtos de tensão, o processo de atualização é concluído mais rapidamente, mas a definição de um valor grande para o surto máximo pode causar interrupções durante o processo de atualização. 

Por exemplo, um valor máximo de surto de 100% fornece o processo de atualização mais rápido possível (duplicando a contagem de nós), mas também faz com que todos os nós no pool de nós sejam drenados simultaneamente. Talvez você queira usar um valor mais alto, como este para os ambientes de teste. Para pools de nós de produção, recomendamos uma configuração de max_surge de 33%.

AKS aceita ambos os valores inteiros e um valor percentual para pico máximo. Um inteiro, como "5", indica cinco nós adicionais para fazer um surto. Um valor de "50%" indica um valor de surto da metade da contagem de nós atual no pool. Os valores de porcentagem máxima de surtos podem ser um mínimo de 1% e um máximo de 100%. Um valor percentual é arredondado para a contagem de nós mais próxima. Se o valor máximo de surto for menor que a contagem de nós atual no momento da atualização, a contagem de nós atual será usada para o valor máximo de surtos de tensão.

Durante uma atualização, o valor máximo de surto pode ser um mínimo de 1 e um valor máximo igual ao número de nós no pool de nós. Você pode definir valores maiores, mas o número máximo de nós usados para picos máximos não será maior do que o número de nós no pool no momento da atualização.

> [!Important]
> A configuração de surto máximo em um pool de nós é permanente.  Atualizações de kubernetes subsequentes ou atualizações de versão de nó usarão essa configuração. Você pode alterar o valor máximo de surtos para seus pools de nós a qualquer momento. Para pools de nós de produção, recomendamos uma configuração de pico máximo de 33%.

Use os comandos a seguir para definir valores de pico máximo para pools de nós novos ou existentes.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Atualizar um cluster AKS

Com uma lista de versões disponíveis para o cluster do AKS, use o comando [az aks upgrade][az-aks-upgrade] para atualizar. Durante o processo de atualização, o AKS irá: 
- Adicione um novo nó de buffer (ou quantos nós forem configurados em [pico máximo](#customize-node-surge-upgrade)) ao cluster que executa a versão de kubernetes especificada. 
- [Cordon e dissipe][kubernetes-drain] um dos nós antigos para minimizar a interrupção na execução de aplicativos (se você estiver usando o surto máximo, ele será [cordondo e esgotado][kubernetes-drain] como muitos nós ao mesmo tempo que o número de nós de buffer especificados). 
- Quando o nó antigo estiver totalmente descarregada, a imagem será recriada para receber a nova versão e ele se tornará o nó de buffer para o seguinte nó a ser atualizado. 
- Esse processo se repete até que todos os nós no cluster tenham sido atualizados. 
- No final do processo, o último nó de buffer será excluído, mantendo a contagem de nós do agente existente e o saldo da zona.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

O cluster demora alguns minutos para ser atualizado, dependendo de quantos nós que você tem.

> [!IMPORTANT]
> Verifique se qualquer `PodDisruptionBudgets` (PDBs) permite que pelo menos 1 réplica de Pod seja movida de cada vez, caso contrário, a operação de descarga/remoção falhará.
> Se a operação de descarga falhar, a operação de atualização falhará por design para garantir que os aplicativos não sejam interrompidos. Corrija o que causou a interrupção da operação (PDBs incorreto, falta de cota e assim por diante) e tente novamente a operação.

Para confirmar se a atualização teve êxito, use o comando [az aks show][az-aks-show]:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

A saída de exemplo a seguir mostra que o cluster agora executa *1.18.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ----------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.18.10              Succeeded            myakscluster-dns-379cbbb9.hcp.eastus.azmk8s.io
```

## <a name="set-auto-upgrade-channel"></a>Definir canal de atualização automática

Além de atualizar manualmente um cluster, você pode definir um canal de atualização automática em seu cluster. Os seguintes canais de atualização estão disponíveis:

|Canal| Ação | Exemplo
|---|---|---|
| `none`| desabilita atualizações automáticas e mantém o cluster em sua versão atual do kubernetes| Configuração padrão se deixado inalterada|
| `patch`| atualize automaticamente o cluster para a versão de patch mais recente com suporte quando ele estiver disponível enquanto mantém a versão secundária a mesma.| Por exemplo, se um cluster estiver executando a versão *1.17.7* e as versões *1.17.9*, *1.18.4*, *1.18.6* e *1.19.1* estiverem disponíveis, o cluster será atualizado para *1.17.9*|
| `stable`| atualize automaticamente o cluster para a versão de patch mais recente com suporte na versão secundária *n-1*, em que *N* é a versão secundária mais recente com suporte.| Por exemplo, se um cluster estiver executando a versão *1.17.7* e as versões *1.17.9*, *1.18.4*, *1.18.6* e *1.19.1* estiverem disponíveis, o cluster será atualizado para *1.18.6*.
| `rapid`| atualize automaticamente o cluster para a versão mais recente do patch com suporte na versão secundária mais recente com suporte.| Nos casos em que o cluster está em uma versão de kubernetes que está em uma versão secundária *n-2* em que *n* é a versão secundária mais recente com suporte, o cluster primeiro atualiza para a versão de patch mais recente com suporte em *n-1* versão secundária. Por exemplo, se um cluster estiver executando a versão *1.17.7* e as versões *1.17.9*, *1.18.4*, *1.18.6* e *1.19.1* estiverem disponíveis, o cluster primeiro será atualizado para *1.18.6* e, em seguida, será atualizado para *1.19.1*.

> [!NOTE]
> Atualização automática de cluster atualiza somente para versões GA do kubernetes e não será atualizada para versões de visualização.

A atualização automática de um cluster segue o mesmo processo que a atualização manual de um cluster. Para obter mais detalhes, consulte [atualizar um cluster AKs][upgrade-cluster].

A atualização automática de cluster para clusters AKS é um recurso de visualização.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Registre o `AutoUpgradePreview` sinalizador de recurso usando o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n AutoUpgradePreview
```

Demora alguns minutos para o status exibir *Registrado*. Verifique o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AutoUpgradePreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Para definir o canal de atualização automática ao criar um cluster, use o parâmetro de *canal de atualização automática* , semelhante ao exemplo a seguir.

```azurecli-interactive
az aks create --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable --generate-ssh-keys
```

Para definir o canal de atualização automática no cluster existente, atualize o parâmetro de *canal de atualização automática* , semelhante ao exemplo a seguir.

```azurecli-interactive
az aks update --resource-group myResourceGroup --name myAKSCluster --auto-upgrade-channel stable
```

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como atualizar um cluster do AKS existente. Para saber mais sobre como implantar e gerenciar clusters do AKS, confira os tutoriais.

> [!div class="nextstepaction"]
> [Tutoriais do AKS][aks-tutorial-prepare-app]

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest#az-feature-list&preserve-view=true
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest#az-provider-register&preserve-view=true
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[upgrade-cluster]:  #upgrade-an-aks-cluster
