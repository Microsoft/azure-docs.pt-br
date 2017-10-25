---
title: "Atualizar um cofre do Site Recovery para um cofre dos Serviços de Recuperação do Azure"
description: "Saiba como atualizar um cofre do Azure Site Recovery para um cofre dos Serviços de Recuperação"
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>Atualizar um cofre do Site Recovery para um cofre dos Serviços de Recuperação com base no Azure Resource Manager

Este artigo descreve como atualizar cofres do Azure Site Recovery para cofres dos Serviços de Recuperação com base no Azure Resource Manager sem afetar a replicação em andamento. Para saber mais sobre os recursos e benefícios do Azure Resource Manager, confira [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introdução
Um cofre dos Serviços de Recuperação é um recurso do Azure Resource Manager para gerenciar backup e recuperação de desastre de forma nativa na nuvem. É um cofre unificado que você pode usar no novo Portal do Azure e ele substitui o backup clássico e os cofres do Site Recovery.

Os cofres dos Serviços de Recuperação oferecem uma matriz de recursos, incluindo:

* Suporte do Azure Resource Manager: você pode proteger e realizar o failover de suas máquinas virtuais e computadores físicos em uma pilha do Azure Resource Manager.

* Excluir disco: se você tem arquivos temporários ou dados com uma variação alta, com os quais você não deseja gastar sua largura de banda, exclua os volumes da replicação. Essa capacidade está atualmente habilitada em *VMware para Azure* e *Hyper-V para Azure* e está estendido para outros cenários também.

* Suporte para armazenamento Premium e armazenamento com redundância local: agora você pode proteger servidores em contas de armazenamento Premium que permitem aos clientes proteger aplicativos com mais operações de entrada/saída por segundo (IOPS). Essa capacidade está atualmente habilitada em *VMware para o Azure*.

* Experiência de introdução simplificada: a experiência aprimorada de introdução foi projetada para facilitar a configuração da recuperação de desastres.

* Gerenciamento de Backup e Site Recovery por meio do mesmo cofre: agora você pode proteger servidores para recuperação de desastre ou realização de backup por meio do mesmo cofre, o que reduz consideravelmente a sobrecarga de gerenciamento.

Para obter mais informações sobre a experiência e os recursos atualizados, consulte o [Blog de Armazenamento, Backup e Recuperação](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Principais recursos

* Sem impacto na replicação em andamento: as replicações em andamento continuam sem qualquer interrupção durante e após a atualização.

* Sem custo adicional: obtenha um conjunto completo de recursos atualizados sem custo adicional.

* Sem perda de dados: como esse processo é uma atualização e não uma migração, os pontos de recuperação e as configurações de replicação existentes permanecem intactas durante e após a atualização.


## <a name="what-happens-during-the-vault-upgrade"></a>O que acontece durante a atualização do cofre?

Durante a atualização, você não pode realizar operações como registrar um novo servidor ou habilitar a replicação para uma VM (máquina virtual). As operações que envolvem a leitura ou gravação de dados no cofre, como a replicação em andamento de itens protegidos no cofre, continuam sem interrupções.

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>Alterações na automação e nas ferramentas depois da atualização
Ao atualizar o tipo de cofre do modelo de implantação clássico para o modelo de implantação do Resource Manager, atualize a automação ou as ferramentas existentes para garantir que continuem funcionando após a atualização.

### <a name="prepare-your-environment-for-the-upgrade"></a>Preparar o ambiente para a atualização

* [Instale o PowerShell ou atualize-o para a versão 5 ou posterior](https://www.microsoft.com/download/details.aspx?id=50395)
* [Instale a versão mais recente do MSI do Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Baixe o script de atualização do cofre dos Serviços de Recuperação](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>Pré-requisitos
Para atualizar de cofres do Site Recovery para cofres do Serviço de Recuperação com base no Azure Resource Manager, você deve atender aos seguintes pré-requisitos:

* Versão mínima do agente: a versão do Provedor do Azure Site Recovery instalado em seu servidor deve ser a 5.1.1700.0 ou posterior.

* Configuração com suporte: não é possível configurar o cofre com a rede SAN (rede de área de armazenamento ) ou Grupos de Disponibilidade AlwaysOn do SQL Server. Há suporte para todas as outras configurações.

    >[!NOTE]
    >Após a atualização, você pode gerenciar o mapeamento de armazenamento somente por meio do PowerShell.

* Cenário de implantação com suporte: seu cofre não deve ser do modelo de implantação herdado *VMware para Azure*. Antes de prosseguir, primeiro mude para o modelo de implantação avançado.

* Nenhum trabalho ativo iniciado pelo usuário que envolva operações do plano de gerenciamento: como o acesso ao plano de gerenciamento é restrito durante a atualização, conclua todas as ações do plano de gerenciamento antes de disparar a atualização. Esse processo não inclui a replicação em andamento.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

**Essa atualização afeta minha replicação em andamento?**

Não. A replicação em andamento continuará sem interrupção durante e após a atualização.

**O que acontece com as configurações de rede, como configurações de IP e VPN site a site?**

A atualização não afeta as configurações de rede. Todas as conexões Azure para local permanecem intactas.

**O que acontece com meus cofres se eu não planejo atualizar em breve?**

O suporte para o cofre do Site Recovery no Portal do Azure antigo será preterido a partir de setembro de 2017. É altamente recomendável que você use o recurso de atualização para mudar para o novo portal.

**O que este plano de migração significa para minhas ferramentas existentes?**  

Atualizar suas ferramentas para o modelo de implantação do Resource Manager é uma das alterações mais importantes que você deve considerar em seus planos de atualização. Os cofres dos Serviços de Recuperação são baseados no modelo de implantação do Resource Manager. 

**Qual será o tempo de inatividade do plano de gerenciamento?**

A atualização normalmente leva cerca de 15 a 30 minutos e poderá levar, no máximo, uma hora.

**Posso reverter após a atualização?**

Não. Não há suporte para reversão após os recursos terem sido atualizados com êxito.

**Posso validar minha assinatura ou meus recursos para ver se eles podem ser atualizados?**

Sim. Na opção de atualização com suporte da plataforma, a primeira etapa é validar se os recursos podem ser atualizados. Se a validação falhar, você receberá mensagens de erro ou avisos apropriados.

**Como faço para relatar um problema com a atualização?**

Se você enfrentar falhas durante a atualização, observe a ID da operação que estará listada no erro. O Suporte da Microsoft trabalhará proativamente para resolver o problema. Também é possível entrar em contato com a equipe do Suporte, informando sua ID de assinatura, o nome do cofre e a ID da operação. O suporte trabalhará para resolver o problema o mais rápido possível. Não repita a operação, a menos que seja explicitamente instruído a fazer isso pela Microsoft.

## <a name="run-the-script"></a>Execute o script

No PowerShell, execute o seguinte comando:

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionID: a ID da assinatura associada ao cofre que você está atualizando.

* VaultName: o nome do cofre que você está atualizando.

* Localização: a localização do cofre que você está atualizando.

* ResourceType: HyperVRecoveryManagerVault para cofres do Site Recovery.

* TargetResourceGroupName: o grupo de recursos no qual você deseja que o cofre atualizado seja colocado. O TargetResourceGroupName pode ser de um grupo de recursos existente no Azure Resource Manager ou um novo. Se o TargetResourceGroupName fornecido não existir, ele será criado como parte da atualização no mesmo local que o cofre. Para obter mais informações, consulte a seção "Grupos de recursos" da [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

    >[!NOTE]
    >A nomenclatura do grupo de recursos está sujeita a determinadas restrições. Para evitar a falha na atualização do cofre, certifique-se de observar a convenção de nomenclatura com cuidado.
    >
    >Por exemplo:
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc

Como alternativa, você pode executar o seguinte script. Insira os valores dos parâmetros necessários.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. O script do PowerShell solicita que você insira suas credenciais. Digite-as duas vezes, uma vez para a conta do modelo de implantação clássico e outra para a conta do Azure Resource Manager.

2. Depois de inserir suas credenciais, o script executará uma verificação para determinar se a sua configuração de infraestrutura atende aos requisitos mencionados anteriormente.

3. Depois que os pré-requisitos foram verificados e confirmados, você será solicitado a continuar com a atualização do cofre. O processo de atualização começará a atualizar seu cofre. Toda a atualização pode levar de 15 a 30 minutos para ser concluída.

4. Depois que a atualização for concluída com êxito, você poderá acessar o cofre atualizado no novo Portal do Azure.

## <a name="post-upgrade-vault-management"></a>Gerenciamento do cofre após a atualização

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>Replicar usando o Azure Site Recovery no cofre dos Serviços de Recuperação

* Agora você pode proteger suas VMs do Azure de uma região para outra. Para obter mais informações, consulte [Replicar VMs do Azure entre regiões com o Azure Site Recovery](site-recovery-azure-to-azure.md).

* Para obter mais informações sobre a replicação de VMs do VMware para o Azure, consulte [Replicar VMs do VMware no Azure com o Site Recovery](vmware-walkthrough-overview.md).

* Para obter mais informações sobre a replicação de VMs do Hyper-V (sem VMM) no Azure, consulte [Replicar máquinas virtuais do Hyper-V (sem o VMM) no Azure](hyper-v-site-walkthrough-overview.md).

* Para obter mais informações sobre a replicação de VMs do Hyper-V (com o VMM) no Azure, consulte [Replicar máquinas virtuais Hyper-V em nuvens VMM no Azure usando o Site Recovery no Portal do Azure](vmm-to-azure-walkthrough-overview.md).

* Para obter mais informações sobre a replicação de VMs do Hyper-V (com o VMM) em um site secundário, consulte [Replicar máquinas virtuais do Hyper-V em nuvens VMM para um site de VMM secundário usando o Portal do Azure](site-recovery-vmm-to-vmm.md).

* Para obter mais informações sobre a replicação de VMs do VMware para um site secundário, consulte [Replicar máquinas virtuais locais ou servidores físicos do VMware em um site secundário no Portal Clássico do Azure](site-recovery-vmware-to-vmware.md).

### <a name="view-your-replicated-items"></a>Exibir seus itens replicados

A imagem abaixo mostra a página de painel do cofre dos Serviços de Recuperação que exibe as principais entidades do cofre. Para exibir uma lista de entidades protegidas no cofre, selecione **Site Recovery** > **Itens replicados**.


![Itens replicados](./media/upgrade-site-recovery-vaults/replicateditems.png)

A imagem a seguir mostra o fluxo de trabalho para exibir seus itens replicados e o comando **Failover** para iniciar um failover.

![Itens replicados](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>Exibir suas configurações de replicação

No cofre do Site Recovery, cada grupo de proteção é definido com frequência de cópia, retenção do ponto de recuperação, frequência dos instantâneos consistentes com aplicativo e outras configurações de replicação. No cofre dos Serviços de Recuperação, essas configurações são definidas como uma política de replicação. O nome da política é o nome do grupo de proteção ou da *primarycloud_Policy*.

Para obter mais informações sobre a política de replicação, consulte [Gerenciar a política de replicação para VMware para Azure](site-recovery-setup-replication-settings-vmware.md).
