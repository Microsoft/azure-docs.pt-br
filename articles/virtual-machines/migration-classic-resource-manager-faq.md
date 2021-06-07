---
title: Perguntas frequentes sobre a migração clássica para a migração do Azure Resource Manager
description: Perguntas frequentes sobre a migração do clássico para o Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 034f09a8b616450e472dcc11427191f187cf489f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615152"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Perguntas frequentes sobre a migração clássica para a migração do Azure Resource Manager

> [!IMPORTANT]
> Hoje, cerca de 90% das VMs de IaaS estão usando [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). A partir de 28 de fevereiro de 2020, as VMs clássicas foram preteridas e serão totalmente desativadas em 1º de março de 2023. [Saiba mais]( https://aka.ms/classicvmretirement) sobre essa reprovação e [como ela afeta você](./classic-vm-deprecation.md#how-does-this-affect-me).

## <a name="what-is-azure-service-manager-and-what-does-it-mean-by-classic"></a>O que é o Service Manager do Azure e o que isso significa por clássico?

A palavra "clássico" na VM IaaS (clássica) refere-se a VMs gerenciadas pelo ASM (Service Manager do Azure). O ASM (Service Manager do Azure) é o antigo plano de controle do Azure responsável por criar, gerenciar, excluir VMs e executar outras operações de plano de controle. 

## <a name="what-is-azure-resource-manager"></a>O que é o Azure Resource Manager?

[Azure Resource Manager](../azure-resource-manager/management/overview.md) é o plano de controle mais recente do Azure responsável por criar, gerenciar, excluir VMs e executar outras operações de plano de controle. 

## <a name="what-is-the-time-required-for-migration"></a>Qual é o tempo necessário para a migração?

O planejamento e a execução da migração dependem muito da complexidade da arquitetura e pode levar alguns meses.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Qual é a definição de um novo cliente em VMs de IaaS (clássicas)?

Os clientes que não tiverem VMs IaaS (clássicas) em suas assinaturas no mês de fevereiro de 2020 (um mês antes da reprovação iniciada) serão considerados como novos clientes. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Qual é a definição de um cliente existente em Máquinas Virtuais de IaaS (clássicas)?

Os clientes que tinham VMs de IaaS (clássicas) ativas ou paradas, mas alocadas, em suas assinaturas no mês de fevereiro de 2020 são considerados como clientes existentes. Somente esses clientes tem até 1º de março de 2023 para migrar suas VMs do Azure Service Manager para o Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Por que estou recebendo um erro informando "NewClassicVMCreationNotAllowedForSubscription"?

Como parte do processo de desativação, a VM de IaaS (clássica) não está mais disponível para novos clientes. Identificamos você como novo cliente e, portanto, sua operação não foi autorizada. É altamente recomendável usar Azure Resource Manager. Se você não puder usar as VMs do Azure usando Azure Resource Manager, entre em contato com o suporte para adicionar sua assinatura à lista de permissões.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Este plano de migração afeta qualquer um de meus serviços existentes ou aplicativos executados em máquinas virtuais do Azure? 

Não até 1º de março de 2023 para VMs de IaaS (clássicas). As VMs de IaaS (clássicas) são serviços com suporte total na disponibilidade geral. É possível continuar usando esses recursos para expandir seu volume no Microsoft Azure. Em 1º de março de 2023, essas VMs serão totalmente desativadas e todas as VMs ativas ou alocadas serão interrompidas e desalocadas.

Não haverá nenhum impacto para outros recursos clássicos, como Serviços de Nuvem (clássicos), Contas de Armazenamento (clássicas) etc.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>O que acontecerá com minhas VMs se eu não planejar a migração no futuro próximo? 

Em 1º de março de 2023, as VMs de IaaS (clássicas) serão totalmente desativadas e todas as VMs ativas ou alocadas serão interrompidas e desalocadas. Para evitar o impacto nos negócios, nós recomentamos começar a planejar sua migração hoje mesmo e concluí-la antes de 1º de março de 2023. Não estamos preterindo as APIs, os Serviços de Nuvem e o modelo de recursos clássicos existentes. Queremos simplificar a migração, considerando os recursos avançados disponíveis no modelo de implantação do Gerenciador de Recursos. Recomendamos que você comece a planejar a migração desses recursos para o Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>O que este plano de migração significa para minhas ferramentas existentes? 

Atualizar suas ferramentas para o modelo de implantação do Gerenciador de Recursos é uma das alterações mais importantes que você precisa considerar em seus planos de migração.

## <a name="how-long-will-the-management-plane-downtime-be"></a>O tempo de inatividade do plano de gerenciamento será de quanto tempo? 

Isso depende do número de recursos que estão sendo migrados. Para implantações menores (algumas dezenas de VMs), a migração inteira deverá levar menos de uma hora. Para implantações de larga escala (centenas de VMs), a migração poderá levar algumas horas.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Poderei reverter depois que meus recursos de migração forem confirmados no Gerenciador de Recursos? 

É possível anular a migração, desde que os recursos estejam no estado preparado. Não há suporte para reversão depois que os recursos forem migrados com êxito por meio da operação de confirmação.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Poderei reverter minha migração se a operação de confirmação falhar? 

Não será possível anular a migração se a operação de confirmação falhar. Todas as operações de migração, incluindo a operação de confirmação, são idempotentes. Portanto, recomendamos que você repita a operação após um curto período. Se você ainda encontrar um erro, crie um tíquete de suporte.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Será necessário comprar outro circuito de Rota Expressa se eu precisar aproveitar usar a IaaS no Gerenciador de Recursos? 

Não. Habilitamos recentemente [a movimentação dos circuitos da ExpressRoute do clássico para o modelo de implantação do Gerenciador de Recursos](../expressroute/expressroute-move.md). Você não precisará comprar um novo circuito de ExpressRoute se já tiver um.

## <a name="what-if-i-had-configured-azure-role-based-access-control-policies-for-my-classic-iaas-resources"></a>E se eu tiver configurado políticas de controle de acesso baseado em função do Azure para meus recursos clássicos de IaaS? 

Durante a migração, os recursos se transformam do clássico para o Gerenciador de Recursos. Portanto, recomendamos que você planeje as atualizações de política do RBAC do Azure que precisam ocorrer após a migração.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Fiz backup de minhas VMs clássicas em um cofre. Posso migrar minhas VMs de modo clássico para modo do Resource Manager e protegê-los em um cofre dos Serviços de Recuperação?

Quando você mover uma VM do modo clássico para o modo do Resource Manager, os backups feitos antes da migração não serão migrados para a VM recém-migrada do Resource Manager. No entanto, caso deseje manter os backups das VMs clássicas, siga estas etapas antes da migração. 

1. No cofre dos serviços de recuperação, vá para a folha **itens de backup** e selecione a VM. 
2. Clique em parar backup. Selecione "reter dados de backup" no menu suspenso.

> [!NOTE]
> Esta opção impedirá que todos os trabalhos de backup futuros protejam sua VM. No entanto, o serviço de backup do Azure manterá os pontos de recuperação que foram armazenados em backup.  Você precisará pagar para manter os pontos de recuperação no cofre (consulte [preços de backup do Azure](https://azure.microsoft.com/pricing/details/backup/) para obter detalhes). Você poderá restaurar a VM, se necessário. Se você decidir retomar a proteção da VM, poderá usar a opção *retomar backup* .
>
>

Para migrar a máquina virtual para o modo do Resource Manager, 

1. Exclua a extensão de backup/instantâneo da VM.
2. Migre a máquina virtual do modo clássico para o modo do Gerenciador de Recursos. As informações de armazenamento e de rede correspondentes à máquina virtual também precisam ser migradas para o modo do Resource Manager.

Além disso, caso deseje fazer backup da VM migrada, acesse a folha de gerenciamento da Máquina Virtual para [habilitar o backup](../backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Posso validar minha assinatura ou meus recursos para ver se eles podem fazer a migração? 

Sim. Na opção de migração com suporte de plataforma, a primeira etapa para preparar a migração é validar se os recursos podem fazer a migração. Caso a operação de validação falhe, você recebe todas as mensagens relativas a todos os motivos pelos quais a migração não pode ser concluída.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>O que acontecerá se eu encontrar um erro de cota ao preparar os recursos de IaaS para migração? 

É recomendável anular a migração e, em seguida, registrar uma solicitação de suporte para aumentar as cotas na região onde você está migrando as VMs. Depois que a solicitação de cota for aprovada, você poderá iniciar a execução das etapas de migração novamente.

## <a name="how-do-i-report-an-issue"></a>Como faço para relatar um problema? 

Poste suas perguntas e dúvidas sobre migração em nossa [Página de P e R da Microsoft para VM](/answers/topics/azure-virtual-machines.html), com a palavra-chave ClassicIaaSMigration. É recomendável postar todas as suas dúvidas neste fórum. Caso você tenha um contrato de suporte, será possível registrar um tíquete de suporte.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>E se eu não gostar dos nomes dos recursos que a plataforma escolheu durante a migração? 

Todos os recursos para os quais você fornecer nomes explicitamente no modelo de implantação clássica são retidos durante a migração. Em alguns casos, novos recursos são criados. Por exemplo: uma interface de rede é criada para cada VM. No momento, não há suporte para a capacidade de controlar os nomes dos novos recursos criados durante a migração. Registre seus votos para esse recurso no [fórum de comentários do Azure](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Posso migrar circuitos de ExpressRoute usados em assinaturas com links de autorização? 

Os circuitos de ExpressRoute que usam links de autorização entre assinaturas não podem ser migrados automaticamente sem tempo de inatividade. Temos orientações sobre como eles podem ser migrados usando as etapas manuais. Confira [Migrar circuitos de ExpressRoute e redes virtuais associadas do modelo de implantação clássico para o Resource Manager](../expressroute/expressroute-migration-classic-resource-manager.md) para obter etapas e mais informações.

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Recebi a mensagem *"A VM está informando o status geral do agente como Não Pronto. Portanto, a VM não pode ser migrada. Certifique-se de que o Agente da VM esteja informando o status geral do agente como Pronto"* ou *"A VM contém uma Extensão cujo status não está sendo informado. Portanto, esta VM não pode ser migrada."*

Essa mensagem é recebida quando a VM não tem conectividade de saída com a Internet. O agente de VM utiliza conectividade de saída para acessar a conta de armazenamento do Azure para atualizar o status do agente a cada cinco minutos.


## <a name="next-steps"></a>Próximas etapas

* [Visão geral da migração de recursos de IaaS com suporte da plataforma do clássico para o Azure Resource Manager](./migration-classic-resource-manager-overview.md)
* [Análise técnica aprofundada sobre a migração com suporte da plataforma do clássico para o Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planejamento para a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Usar o PowerShell para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [Usar a CLI para migrar recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Ferramentas da comunidade para ajudar com a migração de recursos de IaaS do clássico para o Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Examinar os erros de migração mais comuns](migration-classic-resource-manager-errors.md)
