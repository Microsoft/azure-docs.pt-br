---
title: Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)
description: Encontre respostas para algumas perguntas comuns sobre o serviço de Kubernetes do Azure (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 495f182ed450d0fac69b31ea2996bacc60863fea
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672771"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Perguntas frequentes sobre o Serviço de Kubernetes do Azure (AKS)

Este artigo aborda as perguntas frequentes sobre o AKS (Serviço de Kubernetes do Azure).

## <a name="which-azure-regions-currently-provide-aks"></a>Quais regiões do Azure atualmente fornecem AKS?

Para obter uma lista completa das regiões disponíveis, consulte [AKS regiões e disponibilidade][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>O AKS é compatível com o dimensionamento automático de nó?

Sim, a capacidade de dimensionar automaticamente nós de agente horizontalmente em AKS está disponível atualmente em visualização. Ver [dimensionar automaticamente um cluster para atender às demandas do aplicativo no AKS][aks-cluster-autoscaler] for instructions. AKS autoscaling is based on the [Kubernetes autoscaler][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Posso implantar AKS em minha rede virtual existente?

Sim, você pode implantar um cluster do AKS em uma rede virtual existente usando o [recurso de rede avançado][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Pode limitar quem tem acesso ao servidor de API do Kubernetes?

Sim, você pode limitar o acesso ao servidor de API do Kubernetes usando [API Server autorizados de intervalos de IP][api-server-authorized-ip-ranges], que está atualmente em visualização.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Posso tornar o servidor de API do Kubernetes acessível somente dentro de minha rede virtual?

Não desta vez, mas isso está planejado. Você pode acompanhar o progresso de [repositório GitHub do AKS][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Pode ter diferentes tamanhos de VM em um único cluster?

Sim, você pode usar tamanhos de máquina virtual diferente no cluster do AKS, criando [vários pools de nó][multi-node-pools], que está atualmente em visualização.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>As atualizações de segurança são aplicadas aos nós do agente do AKS?

Azure aplica automaticamente os patches de segurança para os nós do Linux no seu cluster em uma agenda noturna. No entanto, você é responsável por garantir que esses nós são reiniciados como de Linux necessários. Você tem várias opções para nós de reinicialização:

- Manualmente, por meio do portal do Azure ou da CLI do Azure.
- Atualizando o cluster AKS. As atualizações de cluster [nós cordon e drain][cordon-drain] automatically and then bring a new node online with the latest Ubuntu image and a new patch version or a minor Kubernetes version. For more information, see [Upgrade an AKS cluster][aks-upgrade].
- Usando [Kured](https://github.com/weaveworks/kured), um daemon de reinicialização do código-fonte aberto para o Kubernetes. Kured é executado como um [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) e monitora cada nó para a presença de um arquivo que indica que uma reinicialização é necessária. Em todo o cluster, as reinicializações do sistema operacional são gerenciadas pelo mesmo [cordon e drain processo][cordon-drain] como uma atualização de cluster.

Para obter mais informações sobre como usar o kured, consulte [aplicar atualizações de segurança e o kernel para nós do AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Nós do Windows Server

Para nós do Windows Server (atualmente em visualização no AKS), atualização do Windows executado automaticamente e aplicar as atualizações mais recentes. Em um agendamento regular em todo o ciclo de lançamento do Windows Update e seu próprio processo de validação, você deve realizar uma atualização sobre os pools de nó do Windows Server no cluster do AKS. Este processo de atualização cria nós que executam a imagem mais recente do Windows Server e os patches, em seguida, remove os nós mais antigos. Para obter mais informações sobre esse processo, consulte [Upgrade de um pool de nós no AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Por que são criados dois grupos de recursos com o AKS?

Cada implementação do AKS abrange dois grupos de recursos:

1. Criar o primeiro grupo de recursos. Esse grupo contém apenas o recurso de serviço do Kubernetes. O provedor de recursos AKS cria automaticamente o segundo grupo de recursos durante a implantação. É um exemplo do segundo grupo de recursos *MC_myResourceGroup_myAKSCluster_eastus*. Para obter informações sobre como especificar o nome do segundo grupo de recursos, consulte a próxima seção.
1. O segundo grupo de recursos, conhecido como o *grupo de recursos do nó*, contém todos os recursos de infraestrutura associados ao cluster. Esses recursos incluem as máquinas virtuais do nó do Kubernetes, rede virtual e armazenamento. Por padrão, o grupo de recursos de nó tem um nome como *MC_myResourceGroup_myAKSCluster_eastus*. AKS exclui automaticamente o recurso de nó sempre que o cluster é excluído, portanto, ele só deve ser usado para os recursos que compartilham um ciclo de vida do cluster.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Posso fornecer meu próprio nome para o grupo de recursos de nó do AKS?

Sim. Por padrão, o AKS nomeará o grupo de recursos do nó *MC_clustername_resourcegroupname_location*, mas você também pode fornecer seu próprio nome.

Para especificar seu próprio nome do grupo de recursos, instale o [versão prévia do aks][aks-preview-cli] versão da extensão da CLI do Azure *0.3.2* ou posterior. Quando você cria um cluster AKS usando o [criar az aks][az-aks-create] de comando, use o *– grupo de recursos de nó* parâmetro e especifique um nome para o grupo de recursos. Se você [usar um modelo do Azure Resource Manager][aks-rm-template] para implantar um cluster do AKS, você pode definir o nome do grupo de recursos usando o *nodeResourceGroup* propriedade.

* O grupo de recursos secundário é criado automaticamente pelo provedor de recursos do Azure em sua própria assinatura.
* Você pode especificar um nome de grupo de recursos personalizados somente quando você estiver criando o cluster.

Conforme você trabalha com o grupo de recursos do nó, tenha em mente que você não pode:

* Especifique um grupo de recursos existente para o grupo de recursos do nó.
* Especifique uma assinatura diferente para o grupo de recursos do nó.
* Depois que o cluster tiver sido criado, altere o nome de grupo de recursos do nó.
* Especifica nomes para os recursos gerenciados dentro do grupo de recursos do nó.
* Modificar ou excluir marcas de recursos gerenciados dentro do grupo de recursos do nó. (Consulte informações adicionais na próxima seção).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Posso modificar marcas e outras propriedades dos recursos no grupo de recursos do nó AKS?

Se você modificar ou excluir marcas criada pelo Azure e outras propriedades de recursos no grupo de recursos de nó, você poderá ter resultados inesperados, como o dimensionamento e erros de atualização. AKS permite que você criar e modificar marcas personalizadas. Você pode desejar para criar ou modificar marcas personalizadas, por exemplo, para atribuir um centro de custo ou de unidade de negócios. Modificando os recursos sob o grupo de recursos do nó no cluster AKS, você deve interromper o objetivo de nível de serviço (SLO). Para obter mais informações, consulte [AKS faz oferecem um contrato de nível de serviço?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Quais os controles de admissão de Kubernetes que o AKS suporta? Controladores de admissão podem ser adicionados ou removidos?

AKS oferece suporte à seguinte [controladores de admissão][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

No momento, você não pode modificar a lista de controladores de admissão no AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>O Azure Key Vault é integrado com o AKS?

AKS atualmente nativamente não está integrado com o Azure Key Vault. No entanto, o [FlexVolume de Cofre de chave do Azure para o projeto Kubernetes][keyvault-flexvolume] permite direcionar a integração de pods Kubernetes para os segredos do Cofre de chaves.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Posso executar contêineres do Windows Server no AKS?

Sim, os contêineres do Windows Server estão disponíveis na visualização. Para executar contêineres do Windows Server no AKS, você deve criar um pool de nós que executa o Windows Server como o sistema operacional convidado. Contêineres do Windows Server podem usar apenas o Windows Server 2019. Para começar, consulte [criar um cluster do AKS com um pool de nós do Windows Server][aks-windows-cli].

Suporte do servidor de janela para o pool de nós inclui algumas limitações que fazem parte do Windows Server no projeto Kubernetes upstream. Para obter mais informações sobre essas limitações, consulte [contêineres do Windows Server nas limitações de AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>O AKS oferece um contrato de nível de serviço?

Em um contrato de nível de serviço (SLA), o provedor concorda em reembolse o cliente para o custo do serviço se o nível de serviço publicado não for atendido. Como AKS é gratuito, sem nenhum custo está disponível para reembolsar, portanto, o AKS não tem nenhum SLA formal. No entanto, o AKS buscas manter a disponibilidade de pelo menos de 99,5% para o servidor de API do Kubernetes.

## <a name="why-cant-i-set-maxpods-below-30"></a>Por que não é possível definir maxPods abaixo de 30?

No AKS, você pode definir o `maxPods` valor quando você cria o cluster usando os modelos da CLI do Azure e o Azure Resource Manager. No entanto, Kubenet e CNI do Azure exigem um *valor mínimo* (validado no momento da criação):

| Rede | Mínimo | Máximo |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Como AKS é um serviço gerenciado, podemos implantar e gerenciar complementos e pods como parte do cluster. No passado, os usuários poderiam definir um `maxPods` valor menor do que o valor que os pods gerenciados necessários para executar (por exemplo, 30). AKS agora calcula o número mínimo de pods usando esta fórmula: ((maxPods ou (maxPods * vm_count)) > mínimo de pods de complemento gerenciado.

Os usuários não podem substituir o mínimo `maxPods` validação.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Pode aplicar descontos de reserva do Azure para Meus nós de agente do AKS?

Nós de agente do AKS são cobrados como máquinas virtuais do Azure padrão, portanto, se você tiver comprado [reservas Azure][reservation-discounts] para o tamanho VM que você está usando no AKS, esses descontos são aplicados automaticamente.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948