---
title: Usar zonas de disponibilidade no serviço de Kubernetes do Azure (AKS)
description: Saiba como criar um cluster que distribui nós entre zonas de disponibilidade no serviço de Kubernetes do Azure (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840674"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Preview - criar um cluster do serviço de Kubernetes do Azure (AKS) que use zonas de disponibilidade

Um cluster do serviço de Kubernetes do Azure (AKS) distribui os recursos, como a infraestrutura de computação a nós e o armazenamento em seções lógicas do Azure subjacente. Esse modelo de implantação garante que os nós de execução entre domínios de falha e atualização separados em um único datacenter do Azure. Clusters AKS implantados com esse comportamento padrão fornecem um alto nível de disponibilidade para proteger contra uma falha de hardware ou planejado de evento de manutenção.

Para fornecer um nível mais alto de disponibilidade para seus aplicativos, os clusters AKS podem ser distribuídos entre zonas de disponibilidade. Essas zonas sejam datacenters fisicamente separados dentro de uma determinada região. Quando os componentes de cluster são distribuídos em várias zonas, o cluster do AKS é capaz de tolerar uma falha em uma dessas zonas. Seus aplicativos e operações de gerenciamento continuam disponíveis, mesmo se um datacenter inteiro tem um problema.

Este artigo mostra como criar um cluster do AKS e distribuir os componentes do nó em zonas de disponibilidade. Esse recurso está atualmente na visualização.

> [!IMPORTANT]
> Recursos de visualização do AKS são Self-service, inscreva-se no. Eles são fornecidos para reunir opiniões e bugs de nossa comunidade. Na visualização, esses recursos não são destinados ao uso em produção. Recursos em visualização pública se encaixam em suporte "melhor esforço". Assistência de AKS equipes de suporte técnico está disponível durante o horário comercial do Pacífico (PST) apenas timezone. Para obter mais informações, consulte as seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

## <a name="before-you-begin"></a>Antes de começar

Você precisa da CLI do Azure versão 2.0.66 ou posterior instalado e configurado. Execute  `az --version` para encontrar a versão. Se você precisar instalar ou atualizar, consulte [instalar a CLI do Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalar a extensão da CLI aks-preview

Para criar clusters AKS que usam zonas de disponibilidade, é necessário o *versão prévia do aks* CLI versão da extensão 0.4.1 ou superior. Instalar o *versão prévia do aks* extensão de CLI do Azure usando o [Adicionar extensão az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Registrar os sinalizadores de recursos para sua assinatura

Para criar um cluster AKS que as zonas de disponibilidade, primeiro habilite alguns sinalizadores de recursos em sua assinatura. Os clusters usam um conjunto para gerenciar a implantação e configuração de nós do Kubernetes de dimensionamento de máquina virtual. O *standard* SKU do balanceador de carga do Azure também é necessária para fornecer resiliência para os componentes de rede para rotear o tráfego em seu cluster. Registre-se a *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*, e *VMSSPreview* sinalizadores de recursos usando o [registro de recurso az][az-feature-register] comando conforme mostrado no exemplo a seguir:

> [!CAUTION]
> Quando você registra um recurso em uma assinatura, você não pode atualmente cancelar o registro desse recurso. Depois de habilitar alguns recursos de visualização, os padrões podem ser usados para todos os clusters AKS, em seguida, é criados na assinatura. Não habilite os recursos de visualização em assinaturas de produção. Use uma assinatura separada para testar recursos de visualização e Reúna comentários.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Demora alguns minutos para o status exibir *Registrado*. Você pode verificar o status de registro usando o [lista de recursos az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando estiver pronto, atualize o registro do *containerservice* provedor de recursos usando o [registro de provedor az][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Limitações e disponibilidade de região

Clusters AKS atualmente podem ser criados usando zonas de disponibilidade nas seguintes regiões:

* Leste dos EUA 2
* Norte da Europa
* Sudeste Asiático
* Europa Ocidental
* Oeste dos EUA 2

As seguintes limitações se aplicam quando você cria um cluster AKS usando zonas de disponibilidade:

* Só é possível habilitar as zonas de disponibilidade quando o cluster é criado.
* Configurações de zona de disponibilidade não podem ser atualizadas depois que o cluster é criado. Também é possível atualizar um cluster de zona existente, não são de disponibilidade para usar zonas de disponibilidade.
* Depois de ele ter sido criado, é possível desabilitar as zonas de disponibilidade para um cluster do AKS.
* O tamanho do nó (SKU de VM) selecionado deve estar disponível em todas as zonas de disponibilidade.
* Clusters com disponibilidade zonas habilitado exigem usam dos balanceadores de carga padrão do Azure para a distribuição entre zonas.
* Você deve usar o Kubernetes versão 1.13.5 ou maior para implantar balanceadores de carga padrão.

Clusters AKS que usam zonas de disponibilidade devem usar o Azure load balancer *standard* SKU. O padrão *básica* SKU do balanceador de carga do Azure não dá suporte a distribuição entre zonas de disponibilidade. Para obter mais informações e as limitações do padrão de Balanceador de carga, consulte [limitações de visualização SKU standard do Azure load balancer][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitações de discos do Azure

Volumes que usam o Azure managed disks atualmente não são recursos de zona. Pods reagendados em uma região diferente da sua região original não é possível reanexar seus discos anteriores. É recomendável executar cargas de trabalho sem monitoração de estado que não exigem o armazenamento persistente que pode se deparar com zonal emite.

Se você precisar executar cargas de trabalho com monitoração de estado, use taints e tolerations em suas especificações de pod para informar o Agendador Kubernetes para criar os pods na mesma zona de seus discos. Como alternativa, use o armazenamento baseado em rede como arquivos do Azure que pode se conectar com os pods conforme estão agendadas entre regiões.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Visão geral das zonas de disponibilidade para o AKS clusters

Zonas de Disponibilidade é uma oferta de alta disponibilidade que protege os aplicativos e dados contra falhas do datacenter. As zonas são locais físicos exclusivos em uma região do Azure. Cada zona é composta por um ou mais datacenters equipados com energia, resfriamento e rede independentes. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões habilitadas. A separação física das Zonas de Disponibilidade dentro de uma região protege os aplicativos e dados contra falhas do datacenter. Serviços com redundância de zona replicam os aplicativos e dados entre Zonas de Disponibilidade para proteger dos pontos únicos de falha.

Para obter mais informações, consulte [o que são zonas de disponibilidade no Azure?][az-overview].

Clusters AKS que são implantados usando zonas de disponibilidade podem distribuir nós em várias zonas em uma única região. Por exemplo, um cluster na *Leste dos EUA 2* região pode criar nós em todas as três zonas de disponibilidade *Leste dos EUA 2*. Essa distribuição de recursos de cluster AKS melhora a disponibilidade do cluster conforme eles são resilientes a falha de uma zona específica.

![Distribuição de nó do AKS em zonas de disponibilidade](media/availability-zones/aks-availability-zones.png)

Em uma interrupção de zona, os nós podem ser reequilibrados manualmente ou usando o dimensionador automático de cluster. Se uma única zona ficar indisponível, seus aplicativos continuam em execução.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Criar um cluster AKS em zonas de disponibilidade

Quando você cria um cluster usando o [criar az aks][az-aks-create] comando, o `--node-zones` parâmetro define quais nós de agente de zonas são implantados em. Os componentes de plano de controle do AKS para o cluster também sejam distribuídos entre zonas na configuração mais alta disponível quando você cria um cluster que especifica o `--node-zones` parâmetro.

Se você não definir nenhuma zona para o pool de agentes padrão quando você cria um cluster do AKS, os componentes de plano de controle do AKS para seu cluster não poderá usar as zonas de disponibilidade. Você pode adicionar os pools de nó adicional (atualmente em visualização no AKS) usando o [adicionar az aks nodepool][az-aks-nodepool-add] comando e especifique `--node-zones` para os novos nós de agente, no entanto os componentes do plano de controle permanecem sem zona de disponibilidade reconhecimento. Não é possível alterar a percepção de zona para um pool de nós ou o AKS controlar os componentes de plano depois de serem implantados.

O exemplo a seguir cria um cluster do AKS denominado *myAKSCluster* no grupo de recursos denominado *myResourceGroup*. Um total de *3* nós são criados - um agente na zona *1*, um na *2*e, em seguida, uma na *3*. Os componentes de plano de controle AKS também são distribuídos entre zonas na configuração mais alta disponível, pois elas são definidas como parte do cluster criar processo.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

Leva alguns minutos para o cluster do AKS ser criado.

## <a name="verify-node-distribution-across-zones"></a>Verifique se a distribuição de nó entre zonas

Quando o cluster estiver pronto, liste os nós de agente no conjunto de dimensionamento para ver qual zona de disponibilidade que eles estiverem implantados em.

Primeiro, obtenha as credenciais de cluster AKS usando o [az aks get-credentials][az-aks-get-credentials] comando:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Em seguida, use o [kubectl descrevem][kubectl-describe] comando para listar os nós no cluster. Filtrar os *failure-domain.beta.kubernetes.io/zone* valor conforme mostrado no exemplo a seguir:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

A saída de exemplo a seguir mostra os três nós distribuídas em toda a região especificada e zonas de disponibilidade, tal como *1 eastus2* para a primeira zona de disponibilidade e *eastus2 2* para o segundo zona de disponibilidade:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Conforme você adiciona nós adicionais a um pool de agentes, a plataforma do Azure distribui automaticamente as VMs subjacentes entre as zonas de disponibilidade especificado.

## <a name="next-steps"></a>Próximas etapas

Este artigo detalhou como criar um cluster AKS que use zonas de disponibilidade. Para obter mais considerações sobre clusters altamente disponíveis, consulte [práticas recomendadas para recuperação de desastre e continuidade de negócios no AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
