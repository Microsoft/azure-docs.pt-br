---
title: Limitações para pools de nós do Windows Server no serviço de Kubernetes do Azure (AKS)
description: Saiba mais sobre as limitações conhecidas quando você executar cargas de trabalho de aplicativo e pools de nós do Windows Server no serviço de Kubernetes do Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 0d79b4d76249bd4a79f8adbd5df0cfa1ae133760
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2019
ms.locfileid: "67613728"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Limitações atuais para pools de nós do Windows Server e cargas de trabalho do aplicativo no serviço de Kubernetes do Azure (AKS)

No serviço de Kubernetes do Azure (AKS), você pode criar um pool de nós que executa o Windows Server como o sistema operacional convidado em nós. Esses nós podem executar aplicativos de contêiner Windows nativos, como aqueles criados no .NET Framework. Como há diferenças em como o sistema operacional do Windows e Linux fornece suporte de contêiner, alguns Kubernetes comuns e os recursos relacionados ao pod não são atualmente disponíveis para pools de nós do Windows.

Este artigo descreve algumas das limitações e conceitos do sistema operacional para os nós do Windows Server no AKS. Pools de nós para o Windows Server estão atualmente em visualização.

> [!IMPORTANT]
> Recursos de visualização do AKS são Self-service, inscreva-se no. Eles são fornecidos para reunir opiniões e bugs de nossa comunidade. Na visualização, esses recursos não são destinados ao uso em produção. Recursos em visualização pública se encaixam em suporte "melhor esforço". Assistência de AKS equipes de suporte técnico está disponível durante o horário comercial do Pacífico (PST) apenas timezone. Para obter mais informações, consulte as seguintes artigos de suporte:
>
> * [Políticas de suporte do AKS][aks-support-policies]
> * [Perguntas frequentes sobre o suporte do Azure.][aks-faq]

## <a name="limitations-for-windows-server-in-kubernetes"></a>Limitações para o Windows Server no Kubernetes

Contêineres do Windows Server devem executar em um host de contêiner com base em Windows. Para executar contêineres do Windows Server no AKS, você pode [criar um pool de nós que executa o Windows Server][windows-node-cli] como o sistema operacional convidado. Suporte de pool de nós de servidor janela inclui algumas limitações que fazem parte do Windows Server no projeto Kubernetes upstream. Essas limitações não são específicas para o AKS. Para obter mais informações sobre esse suporte upstream para o Windows Server em Kubernetes, consulte [contêineres do Windows Server em Kubernetes limitações](https://docs.microsoft.com/azure/aks/windows-node-limitations).

As seguintes limitações de upstream para contêineres do Windows Server no Kubernetes são relevantes para o AKS:

- Contêineres do Windows Server só podem usar o Windows Server 2019, que corresponde ao nó do Windows Server subjacente do sistema operacional.
    - Imagens de contêiner criadas usando o Windows Server 2016 como não há suporte para o sistema operacional base.
- Contêineres com privilégios não podem ser usados.
- Recursos específicos do Linux como recursos RunAsUser, SELinux, AppArmor ou POSIX não estão disponíveis em contêineres do Windows Server.
    - Limitações do sistema de arquivos que são específicos do Linux como UUI/GUID, por permissões de usuário também não estão disponíveis em contêineres do Windows Server.
- Os arquivos do Azure e os discos do Azure são os tipos de volume com suporte, acessados como volumes NTFS no contêiner do Windows Server.
    - Armazenamento baseado em NFS / volumes não têm suporte.

## <a name="aks-limitations-for-windows-server-node-pools"></a>Limitações do AKS para pools de nós do Windows Server

As seguintes limitações adicionais se aplicam ao suporte de pool de nós do Windows Server no AKS:

- Um cluster do AKS sempre contém um pool de nós do Linux como o primeiro pool de nós. Este primeiro pool de nós baseados em Linux não pode ser excluído, a menos que o próprio cluster do AKS é excluído.
- Clusters AKS devem usar o modelo de rede (Avançado) CNI do Azure.
    - Não há suporte para a rede Kubenet (basic). Você não pode criar um cluster do AKS que usa kubenet. Para obter mais informações sobre as diferenças nos modelos de rede, consulte [rede conceitos para aplicativos no AKS][azure-network-models].
    - O modelo de rede CNI do Azure requer planejamento adicional e considerações sobre o gerenciamento de endereço IP. Para obter mais informações sobre como planejar e implementar o CNI do Azure, consulte [rede configurar CNI do Azure no AKS][configure-azure-cni].
- Nós do Windows Server no AKS devem ser *atualizado* para uma versão mais recente do Windows Server 2019 para manter o patch mais recente correções e atualizações. Atualizações do Windows não estão habilitadas na imagem base do nó no AKS. Em um agendamento regular em todo o ciclo de lançamento do Windows Update e seu próprio processo de validação, você deve realizar uma atualização sobre os pools de nó do Windows Server no cluster do AKS. Para obter mais informações sobre como atualizar um pool de nós do Windows Server, consulte [Upgrade de um pool de nós no AKS][nodepool-upgrade].
    - Essas atualizações de nó do Windows Server temporariamente consumam mais endereços IP na sub-rede da rede virtual como um novo nó é implantado, antes que o nó antigo é removido.
    - cotas de vCPU temporariamente também são consumidas na assinatura como um novo nó é implantado, o nó antigo é removido.
    - Automaticamente, você não pode atualizar e gerenciar as reinicializações usando `kured` assim como acontece conosco do Linux no AKS.
- O cluster do AKS pode ter um máximo de oito pools de nó.
    - Você pode ter um máximo de 400 nós entre esses pools de oito nós.
- O nome do pool de nó do Windows Server tem um limite de 6 caracteres.
- Recursos de visualização no AKS, como o dimensionador automático do cluster e a política de rede não são aprovados para nós do Windows Server.
- Controladores de entrada devem ser agendadas somente em nós do Linux usando um NodeSelector.
- Espaços de desenvolvimento do Azure só está disponível para pools de nós baseados em Linux no momento.
- Grupo de contas de serviço gerenciado (gMSA) suporte quando os nós do Windows Server não estão associados a um domínio do Active Directory não está disponível atualmente no AKS.
    - O código-fonte aberto, upstream [aks-mecanismo][aks-engine] projeto no momento, oferece suporte a gMSA se você precisar usar esse recurso.

## <a name="os-concepts-that-are-different"></a>Conceitos de sistema operacional que são diferentes

O Kubernetes é historicamente focada em Linux. Muitos exemplos usados upstream [Kubernetes.io][kubernetes] site são destinadas para uso em nós do Linux. Quando você cria implantações que usam contêineres do Windows Server, as seguintes considerações ao aplicar nível de sistema operacional:

- **Identidade** -Linux usa a ID de usuário (UID) e ID do grupo (GID), representadas como tipos de inteiro. Nomes de usuário e grupo não canônicos, eles são apenas um alias no */etc/grupos* ou */etc/passwd* para UID + GID.
    - Windows Server usa um identificador de segurança binário (SID) maior, que é armazenado no banco de dados do Gerenciador de acesso de segurança do Windows (SAM). Este banco de dados não é compartilhado entre o host e contêineres ou entre contêineres.
- **Permissões de arquivo** -Windows Server usa uma lista de controle de acesso com base em SIDs, em vez de um bitmask de permissões e UID + GID
- **Caminhos de arquivo** -convenção no Windows Server é usar \ em vez de /.
    - Nas especificações de pod que montagem volumes, especifique o caminho corretamente para contêineres do Windows Server. Por exemplo, em vez de uma montagem de ponto da */mnt/Retention/ volume* em um contêiner do Linux, especifique uma letra de unidade e o local, como *K/Volume* montar como o *k:* unidade.

## <a name="next-steps"></a>Próximas etapas

Para obter uma introdução a contêineres do Windows Server no AKS, [criar um pool de nós que executa o Windows Server no AKS][windows-node-cli].

<!-- LINKS - external -->
[upstream-limitations]: https://kubernetes.io/docs/setup/windows/#limitations
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
