---
title: Usar o dimensionador automático de cluster no AKS (Serviço de Kubernetes do Azure)
description: Saiba como usar o dimensionador automático de cluster para dimensionar automaticamente seu cluster e atender às demandas de aplicativo em um cluster do AKS (Serviço de Kubernetes do Azure).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 07/18/2019
ms.author: mlearned
ms.openlocfilehash: 09610782f211b4cfb80a1291b73ab543328376a3
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424187"
---
# <a name="preview---automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Visualização – dimensionar automaticamente um cluster para atender às demandas do aplicativo no serviço de kubernetes do Azure (AKS)

Para se manter atualizado com as demandas de aplicativo no AKS (Serviço de Kubernetes do Azure), talvez você precise ajustar o número de nós que executam as cargas de trabalho. O componente de dimensionador automático de cluster pode inspecionar os pods no cluster que não podem ser agendados devido a restrições de recursos. Quando forem detectados problemas, o número de nós em um pool de nós será aumentado para atender à demanda do aplicativo. Os nós também são regularmente verificados quanto à falta de pods em execução, com o número de nós diminuído posteriormente conforme necessário. Essa capacidade de escalar ou reduzir verticalmente o número de nós no cluster do AKS permite a execução de um cluster eficiente e econômico.

Este artigo mostra como habilitar e gerenciar o dimensionador automático de cluster em um cluster do AKS. O autoescalar do cluster só deve ser testado na visualização em clusters AKS.

> [!IMPORTANT]
> Os recursos de visualização do AKS são de autoatendimento e aceitação. Eles são fornecidos para reunir comentários e bugs de nossa comunidade. Na versão prévia, esses recursos não são destinados ao uso em produção. Os recursos na visualização pública se enquadram no suporte "melhor esforço". A assistência das equipes de suporte técnico do AKS está disponível durante o horário comercial do fuso horário do Pacífico (PST). Para obter informações adicionais, consulte os seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Este artigo requer que você esteja executando o CLI do Azure versão 2.0.65 ou posterior. Execute `az --version` para encontrar a versão. Se você precisa instalar ou atualizar, consulte [Instalar a CLI do Azure][azure-cli-install].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para usar o dimensionamento do cluster, você precisa da extensão da CLI do *AKs* versão 0.4.4 ou superior. Instale a extensão de CLI do Azure *de AKs-Preview* usando o comando [AZ Extension Add][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-scale-set-feature-provider"></a>Registrar o provedor de recursos do conjunto de dimensionamento

Para criar um AKS que usa os conjuntos de dimensionamentos, você também deve habilitar um sinalizador de recursos em sua assinatura. Para registrar o sinalizador de recurso *VMSSPreview* , use o comando [AZ Feature Register][az-feature-register] , conforme mostrado no exemplo a seguir:

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, não é possível cancelar o registro desse recurso no momento. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS, em seguida, criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e coletar comentários.

```azurecli-interactive
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status do registro usando o comando [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do provedor de recursos *Microsoft. ContainerService* usando o comando [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations"></a>Limitações

As seguintes limitações se aplicam quando você cria e gerencia clusters AKS que usam o dimensionamento de clusters:

* O complemento de roteamento de aplicativo HTTP não pode ser usado.

## <a name="about-the-cluster-autoscaler"></a>Sobre o dimensionador automático de cluster

Para se ajustar às mudanças nas demandas de aplicativo, como entre a jornada de trabalho e a noite ou em um fim de semana, os clusters geralmente precisam de uma maneira de serem dimensionados automaticamente. Os clusters do AKS podem ser dimensionados de uma das duas maneiras:

* O **dimensionador automático de cluster** inspeciona os pods que não podem ser agendados em nós devido a restrições de recursos. Em seguida, o cluster aumenta automaticamente o número de nós.
* O **dimensionador automático de pod horizontal** usa o Servidor de Métricas em um cluster do Kubernetes para monitorar a demanda de recursos dos pods. Se um serviço precisa de mais recursos, o número de pods é automaticamente aumentado para atender à demanda.

![O dimensionador automático de cluster e o dimensionador automático de pod horizontal geralmente trabalham juntos para dar suporte às demandas necessárias de aplicativo](media/autoscaler/cluster-autoscaler.png)

O dimensionador automático de pod horizontal e o dimensionador automático de cluster também podem, em seguida, diminuir o número de pods e nós conforme necessário. O dimensionador automático de cluster diminui o número de nós quando a capacidade não é utilizada por um período. Os pods em um nó a serem removidos pelo dimensionador automático de cluster são agendados com segurança em outro lugar no cluster. O dimensionador automático de cluster poderá não conseguir fazer a redução vertical se os pods não puderem ser movidos, como nas seguintes situações:

* Um pod criado diretamente e que não tem o suporte de um objeto de controlador, como uma implantação ou um conjunto de réplicas.
* Um PDB (orçamento de interrupção de pod) é muito restritivo e não permite que o número de pods fique abaixo de determinado limite.
* Um pod usa seletores de nó ou uma antiafinidade que não pode ser cumprida se ele está agendado em outro nó.

Para obter mais informações sobre como o dimensionador de cluster pode não ser capaz de reduzir verticalmente, consulte [quais tipos de pods podem impedir que o dimensionamento de um cluster remova um nó?][autoscaler-scaledown]

O dimensionador automático de cluster usa parâmetros de inicialização para itens como intervalos de tempo entre eventos de escala e limites de recursos. Esses parâmetros são definidos pela plataforma Azure e atualmente não são expostos para seu ajuste. Para obter mais informações sobre quais parâmetros são usados pelo dimensionador de cluster, consulte [quais são os parâmetros de dimensionamento de clusters?][autoscaler-parameters].

Os dois dimensionadores automáticos podem trabalhar juntos e geralmente são implantados em um cluster. Quando combinados, o dimensionador automático de pod horizontal se concentra na execução do número de pods necessários para atender à demanda de aplicativo. O dimensionador automático de cluster se concentra na execução do número de nós necessários para dar suporte aos pods agendados.

> [!NOTE]
> O dimensionamento manual é desabilitado quando o dimensionador automático de cluster é usado. Deixe o dimensionador automático de cluster determinar o número de nós necessários. Caso deseje dimensionar o cluster manualmente, [desabilite o dimensionador automático de cluster](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Criar um cluster do AKS e habilitar o dimensionador automático de cluster

Se você precisar criar um cluster AKS, use o comando [AZ AKs Create][az-aks-create] . Para habilitar e configurar o cluster de dimensionamento autoescalar no pool de nós para o cluster, use o parâmetro *--Enable-cluster-* AutoScaler e especifique um nó *--min-Count* e *--Max-Count*.

> [!IMPORTANT]
> O dimensionamento automático do cluster é um componente de Kubernetes. Embora o cluster do AKS use um conjunto de dimensionamento de máquinas virtuais para os nós, não habilite ou edite manualmente as configurações de dimensionamento automático do conjunto de dimensionamento no portal do Azure ou usando a CLI do Azure. Permita que o dimensionador automático do cluster de Kubernetes gerencie as configurações de dimensionamento necessárias. Para obter mais informações, consulte posso [modificar os recursos do AKS no grupo de recursos do nó?](faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group)

O exemplo a seguir cria um cluster AKS com o conjunto de dimensionamento de máquinas virtuais. Ele também habilita o dimensionador do cluster em conjunto de nós para o cluster e define um mínimo de *1* e o máximo de *3* nós:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-vmss \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!NOTE]
> Se você especificar uma *--kubernetes-Version* durante a `az aks create`execução, essa versão deverá atender ou exceder o número de versão mínimo necessário, conforme descrito na seção anterior [antes de começar](#before-you-begin) .

São necessários alguns minutos para criar o cluster e definir as configurações do dimensionador automático de cluster.

### <a name="enable-the-cluster-autoscaler-on-an-existing-node-pool-in-an-aks-cluster"></a>Habilitar o dimensionamento de cluster em um pool de nós existente em um cluster AKS

Você pode habilitar o conjunto de dimensionamento de clusters em um pool de nós em um cluster AKS que atenda aos requisitos conforme descrito na seção anterior [antes de começar](#before-you-begin) . Use o comando [AZ AKs nodepool Update][az-aks-nodepool-update] para habilitar o dimensionamento automática do cluster em seu pool de nós.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

O exemplo acima habilita o conjunto de dimensionamento de clusters no pool de nós *mynodepool* no *myAKSCluster* e define um mínimo de *1* e o máximo de *3* nós. Se a contagem de nós mínima for maior que o número existente de nós no pool de nós, levará alguns minutos para criar os nós adicionais.

## <a name="change-the-cluster-autoscaler-settings"></a>Alterar as configurações do dimensionador automático de cluster

Na etapa anterior para criar um cluster AKS ou atualizar um pool de nós existente, a contagem de nós mínimos do cluster de dimensionamento foi definida como *1*e a contagem máxima de nós foi definida como *3*. Conforme as demandas de aplicativo mudam, você pode precisar ajustar a contagem de nós do dimensionador automático de cluster.

Para alterar a contagem de nós, use o comando [AZ AKs nodepool Update][az-aks-nodepool-update] .

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

O exemplo acima atualiza o conjunto de dimensionamento de clusters no pool de nós *mynodepool* no *myAKSCluster* para um mínimo de *1* e o máximo de *5* nós.

> [!NOTE]
> Durante a versão prévia, você não pode definir uma contagem de nós mínima mais alta do que a atualmente definida para o pool de nós. Por exemplo, se atualmente você tiver uma contagem mínima definida como *1*, não poderá atualizar a contagem mínima para *3*.

Monitore o desempenho de seus aplicativos e serviços e ajuste as contagens de nós do dimensionador automático de cluster para que correspondam ao desempenho necessário.

## <a name="disable-the-cluster-autoscaler"></a>Desabilitar o dimensionador automático de cluster

Se você não quiser mais usar o dimensionamento automática do cluster, poderá desabilitá-lo usando o comando [AZ AKs nodepool Update][az-aks-nodepool-update] , especificando o parâmetro *--Disable-cluster-* AutoScaler. Os nós não são removidos quando o dimensionador automático de cluster é desabilitado.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --disable-cluster-autoscaler
```

Você pode dimensionar manualmente o cluster usando o comando [AZ AKs Scale][az-aks-scale] . Se você usar o dimensionador automático de pod horizontal, esse recurso continuará sendo executado com o dimensionador automático de cluster desabilitado, mas os pods poderão acabar não conseguindo ser agendados se os recursos do nó estiverem todos em uso.

## <a name="next-steps"></a>Próximas etapas

Este artigo mostrou como dimensionar automaticamente o número de nós do AKS. Você também pode usar o dimensionador automático de pod horizontal para ajustar automaticamente o número de pods que executam o aplicativo. Para obter as etapas sobre como usar a escalabilidade horizontal Pod, consulte [dimensionar aplicativos em AKs][aks-scale-apps].

<!-- LINKS - internal -->
[aks-upgrade]: upgrade-cluster.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-scale-apps]: tutorial-kubernetes-scale.md
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

<!-- LINKS - external -->
[az-aks-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
