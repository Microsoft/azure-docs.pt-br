---
title: Perguntas frequentes sobre pools de nós do Windows Server
titleSuffix: Azure Kubernetes Service
description: Consulte as perguntas frequentes ao executar pools de nós do Windows Server e cargas de trabalho de aplicativo no serviço kubernetes do Azure (AKS).
services: container-service
ms.topic: article
ms.date: 10/12/2020
ms.openlocfilehash: cc5a5ec2bbfb64a1e787277bf67579bad0543cd6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101739569"
---
# <a name="frequently-asked-questions-for-windows-server-node-pools-in-aks"></a>Perguntas frequentes sobre pools de nós do Windows Server no AKS

No serviço de kubernetes do Azure (AKS), você pode criar um pool de nós que executa o Windows Server como o sistema operacional convidado nos nós. Esses nós podem executar aplicativos de contêiner do Windows nativos, como os criados no .NET Framework. Há diferenças em como o sistema operacional Linux e Windows fornece suporte a contêineres. Alguns recursos comuns relacionados a kubernetes e Pod do Linux não estão disponíveis atualmente para pools de nós do Windows.

Este artigo descreve algumas das perguntas frequentes e dos conceitos de sistema operacional para nós do Windows Server no AKS.

## <a name="which-windows-operating-systems-are-supported"></a>Quais sistemas operacionais Windows têm suporte?

O AKS usa o Windows Server 2019 como a versão do sistema operacional do host e só dá suporte ao isolamento do processo. Não há suporte para imagens de contêiner criadas usando outras versões do Windows Server. Para obter mais informações, consulte [compatibilidade de versão do contêiner do Windows][windows-container-compat].

## <a name="is-kubernetes-different-on-windows-and-linux"></a>O kubernetes é diferente no Windows e no Linux?

O suporte ao pool de nós do servidor de janelas inclui algumas limitações que fazem parte do Windows Server upstream no projeto kubernetes. Essas limitações não são específicas do AKS. Para obter mais informações sobre esse suporte upstream para o Windows Server no kubernetes, consulte a seção [funcionalidade e limitações com suporte][upstream-limitations] do [introdução ao suporte do Windows no documento kubernetes][intro-windows] , do projeto kubernetes.

O kubernetes é historicamente focado em Linux. Muitos exemplos usados no site do upstream [kubernetes.Io][kubernetes] são destinados para uso em nós do Linux. Quando você cria implantações que usam contêineres do Windows Server, as seguintes considerações no nível do sistema operacional se aplicam:

- **Identidade** – o Linux identifica um usuário por um UID (identificador de usuário inteiro). Um usuário também tem um nome de usuário alfanumérico para fazer logon, que o Linux traduz para a UID do usuário. Da mesma forma, o Linux identifica um grupo de usuários por um GID (identificador de grupo inteiro) e converte um nome de grupo em seu GID correspondente.
    - O Windows Server usa um SID (identificador de segurança binário) maior que é armazenado no banco de dados SAM (Gerenciador de acesso à segurança do Windows). Esse banco de dados não é compartilhado entre o host e os contêineres ou entre contêineres.
- **Permissões de arquivo** -o Windows Server usa uma lista de controle de acesso baseada em SIDs, em vez de um bitmask de permissões e uid + gid
- **Caminhos de arquivo** -a Convenção no Windows Server é usar \ em vez de/.
    - Em especificações de Pod que montam volumes, especifique o caminho corretamente para contêineres do Windows Server. Por exemplo, em vez de um ponto de montagem de */mnt/volume* em um contêiner do Linux, especifique uma letra de unidade e um local como */K/volume* para montar como a unidade *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Que tipos de discos têm suporte para o Windows?

Os discos do Azure e os arquivos do Azure são os tipos de volume com suporte. Eles são acessados como volumes NTFS no contêiner do Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Posso executar clusters somente do Windows no AKS?

Os nós mestres (o plano de controle) em um cluster AKS são hospedados pelo AKS do serviço, você não será exposto ao sistema operacional dos nós que hospedam os componentes mestres. Todos os clusters AKS são criados com um pool de primeiro nó padrão, que é baseado em Linux. Esse pool de nós contém serviços do sistema, que são necessários para que o cluster funcione. É recomendável executar pelo menos dois nós no primeiro pool de nós para garantir a confiabilidade do cluster e a capacidade de realizar operações de cluster. O primeiro pool de nós baseado em Linux não pode ser excluído, a menos que o próprio cluster AKS seja excluído.

## <a name="how-do-i-patch-my-windows-nodes"></a>Como fazer para aplicar patch aos nós do Windows?

Para obter os patches mais recentes para os nós do Windows, você pode [atualizar o pool de nós][nodepool-upgrade] ou [atualizar a imagem do nó][upgrade-node-image]. As atualizações do Windows não estão habilitadas em nós no AKS. O AKS lança novas imagens de pool de nós assim que os patches estão disponíveis, e é responsabilidade do usuário atualizar pools de nós para manter-se atualizado sobre patches e hotfixes. Isso também é verdadeiro para a versão kubernetes que está sendo usada. [Notas de versão do AKS][aks-release-notes] indicam quando novas versões estão disponíveis. Para obter mais informações sobre como atualizar todo o pool de nós do Windows Server, consulte [atualizar um pool de nós no AKs][nodepool-upgrade]. Se você estiver interessado apenas em atualizar a imagem do nó, consulte [AKs node Image upgrades][upgrade-node-image].

> [!NOTE]
> A imagem atualizada do Windows Server será usada somente se uma atualização de cluster (atualização do plano de controle) tiver sido executada antes da atualização do pool de nós.
>

## <a name="what-network-plug-ins-are-supported"></a>Quais plug-ins de rede têm suporte?

Clusters AKS com pools de nós do Windows devem usar o modelo de rede CNI do Azure (avançado). Não há suporte para a rede Kubenet (básica). Para obter mais informações sobre as diferenças em modelos de rede, consulte [conceitos de rede para aplicativos no AKs][azure-network-models]. O modelo de rede CNI do Azure requer planejamento e considerações adicionais para o gerenciamento de endereços IP. Para obter mais informações sobre como planejar e implementar o Azure CNI, consulte [Configurar a rede CNI do Azure no AKs][configure-azure-cni].

Os nós do Windows em clusters AKS também têm o [DSR (Direct Server Return)][dsr] habilitado por padrão quando Calico está habilitado.

## <a name="is-preserving-the-client-source-ip-supported"></a>Há suporte para a preservação do IP de origem do cliente?

Neste momento, não há suporte para a [preservação do IP de origem do cliente][client-source-ip] com nós do Windows.

## <a name="can-i-change-the-max--of-pods-per-node"></a>Posso alterar o Max. # de pods por nó?

Sim. Para as implicações e opções disponíveis, consulte [número máximo de pods][maximum-number-of-pods].

## <a name="why-am-i-seeing-an-error-when-i-try-to-create-a-new-windows-agent-pool"></a>Por que estou vendo um erro ao tentar criar um novo pool do agente do Windows?

Se você criou o cluster antes de fevereiro de 2020 e nunca fez nenhuma operação de atualização de cluster, o cluster ainda usa uma imagem antiga do Windows. Você pode ter visto um erro semelhante a:

"A seguinte lista de imagens referenciadas do modelo de implantação não foi encontrada: Editor: MicrosoftWindowsServer, oferta: WindowsServer, SKU: 2019-datacenter-Core-smalldisk-2004, versão: mais recente. Consulte https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage para obter instruções sobre como localizar imagens disponíveis. "

Para corrigir esse erro: 

1. Atualize o [plano de controle de cluster][upgrade-cluster-cp] para atualizar a oferta de imagem e o Publicador.
1. Crie novos pools do agente do Windows.
1. Mova os pods do Windows de pools existentes do agente do Windows para novos pools do agente do Windows.
1. Exclua pools antigos do agente do Windows.

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Como fazer girar a entidade de serviço para meu pool de nós do Windows?

Os pools de nós do Windows não dão suporte à rotação de entidade de serviço. Para atualizar a entidade de serviço, crie um novo pool de nós do Windows e migre o pods do pool mais antigo para o novo. Quando isso for concluído, exclua o pool de nós mais antigo.

Em vez disso, use identidades gerenciadas, que são essencialmente wrappers em volta de entidades de serviço. Para obter mais informações, confira [Usar identidades gerenciadas no Serviço de Kubernetes do Azure][managed-identity].

## <a name="how-many-node-pools-can-i-create"></a>Quantos pools de nós posso criar?

O cluster AKS pode ter um máximo de dez pools de nós. Você pode ter um máximo de 1000 nós entre esses pools de nós. [Limitações do pool de nós][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>O que posso nomear meus pools de nós do Windows?

Você precisa manter o nome em um máximo de 6 (seis) caracteres. Essa é uma limitação atual do AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Todos os recursos têm suporte com nós do Windows?

No momento, não há suporte para Kubenet com nós do Windows.

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Posso executar controladores de entrada em nós do Windows?

Sim, um controlador de entrada que dá suporte a contêineres do Windows Server pode ser executado em nós do Windows no AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Posso usar Azure Dev Spaces com nós do Windows?

Atualmente, o Azure Dev Spaces está disponível apenas para pools de nós baseados em Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Meus contêineres do Windows Server podem usar a gMSA?

O suporte ao grupo de contas de serviço gerenciado (gMSA) não está disponível no momento no AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Posso usar Azure Monitor para contêineres com nós e contêineres do Windows?

Sim, você pode, no entanto, Azure Monitor está em visualização pública para coletar logs (stdout, stderr) e métricas de contêineres do Windows. Você também pode anexar à transmissão ao vivo de logs stdout de um contêiner do Windows.

## <a name="are-there-any-limitations-on-the-number-of-services-on-a-cluster-with-windows-nodes"></a>Há limitações quanto ao número de serviços em um cluster com nós do Windows?

Um cluster com nós do Windows pode ter aproximadamente 500 serviços antes de encontrar a exaustão da porta.

## <a name="can-i-use-azure-hybrid-benefit-with-windows-nodes"></a>Posso usar Benefício Híbrido do Azure com nós do Windows?

Sim. O Benefício Híbrido do Azure para Windows Server reduz os custos operacionais, permitindo que você traga sua licença do Windows Server local para os nós do Windows AKS.

Benefício Híbrido do Azure pode ser usado em todo o cluster AKS ou em nós individuais. Para nós individuais, você precisa navegar até o [grupo de recursos do nó][resource-groups] e aplicar o benefício híbrido do Azure aos nós diretamente. Para obter mais informações sobre como aplicar Benefício Híbrido do Azure a nós individuais, consulte [benefício híbrido do Azure para Windows Server][hybrid-vms]. 

Para usar Benefício Híbrido do Azure em um novo cluster AKS, use o `--enable-ahub` argumento.

```azurecli
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-sku Standard \
    --windows-admin-password 'Password1234$' \
    --windows-admin-username azure \
    --network-plugin azure
    --enable-ahub
```

Para usar Benefício Híbrido do Azure em um cluster AKS existente, atualize o cluster usando o `--enable-ahub` argumento.

```azurecli
az aks update \
    --resource-group myResourceGroup
    --name myAKSCluster
    --enable-ahub
```

Para verificar se Benefício Híbrido do Azure está definido no cluster, use o seguinte comando:

```azurecli
az vmss show --name myAKSCluster --resource-group MC_CLUSTERNAME
```

Se o cluster tiver Benefício Híbrido do Azure habilitado, a saída do `az vmss show` será semelhante ao seguinte:

```console
"platformFaultDomainCount": 1,
  "provisioningState": "Succeeded",
  "proximityPlacementGroup": null,
  "resourceGroup": "MC_CLUSTERNAME"
```

## <a name="can-i-use-the-kubernetes-web-dashboard-with-windows-containers"></a>Posso usar o painel da Web do kubernetes com contêineres do Windows?

Sim, você pode usar o [painel da Web do kubernetes][kubernetes-dashboard] para acessar informações sobre contêineres do Windows, mas, neste momento, não é possível executar o *kubectl exec* em um contêiner do Windows em execução diretamente do painel da Web do kubernetes. Para obter mais detalhes sobre como se conectar ao seu contêiner do Windows em execução, consulte [conectar-se com o RDP para os nós do Windows Server do cluster do AKS (serviço kubernetes do Azure) para manutenção ou solução de problemas][windows-rdp].

## <a name="what-if-i-need-a-feature-thats-not-supported"></a>E se eu precisar de um recurso que não tenha suporte?

Trabalhamos muito para reunir todos os recursos de que você precisa para o Windows no AKS, mas se você encontrar lacunas, o projeto de [AKs-Engine][aks-engine] de software livre fornecerá uma maneira fácil e totalmente personalizável de executar o kubernetes no Azure, incluindo o suporte do Windows. Lembre-se de conferir nosso roteiro de recursos que estão chegando ao [roteiro do AKS][aks-roadmap].

## <a name="next-steps"></a>Próximas etapas

Para começar a usar contêineres do Windows Server no AKS, [crie um pool de nós que executa o Windows Server em AKs][windows-node-cli].

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1
[aks-release-notes]: https://github.com/Azure/AKS/releases

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[upgrade-cluster]: upgrade-cluster.md
[upgrade-cluster-cp]: use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
[azure-monitor]: ../azure-monitor/containers/container-insights-overview.md#what-does-azure-monitor-for-containers-provide
[client-source-ip]: concepts-network.md#ingress-controllers
[kubernetes-dashboard]: kubernetes-dashboard.md
[windows-rdp]: rdp.md
[upgrade-node-image]: node-image-upgrade.md
[managed-identity]: use-managed-identity.md
[hybrid-vms]: ../virtual-machines/windows/hybrid-use-benefit-licensing.md
[resource-groups]: faq.md#why-are-two-resource-groups-created-with-aks
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip