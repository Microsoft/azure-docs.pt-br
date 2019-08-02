---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-machines
author: singhkays
ms.service: virtual-machines
ms.topic: include
ms.date: 05/18/2018
ms.author: kasing
ms.custom: include file
ms.openlocfilehash: c4f9334a91e99c92e74af060d84308a8aaccc6e7
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67671273"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Perguntas frequentes sobre a migração clássica para a migração do Azure Resource Manager

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Este plano de migração afeta qualquer um de meus serviços existentes ou aplicativos executados em máquinas virtuais do Azure? 

Nº As VMs (clássicas) são serviços com suporte total na disponibilidade geral. É possível continuar usando esses recursos para expandir seu volume no Microsoft Azure.

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>O que acontecerá com minhas VMs se eu não planejar a migração no futuro próximo? 

Não estamos preterindo as APIs clássicas e o modelo de recursos existentes. Queremos simplificar a migração, considerando os recursos avançados disponíveis no modelo de implantação do Gerenciador de Recursos. É altamente recomendável que você examine [alguns dos avanços feitos](../articles/azure-resource-manager/resource-manager-deployment-model.md) como parte do IaaS no Gerenciador de Recursos.

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>O que este plano de migração significa para minhas ferramentas existentes? 

Atualizar suas ferramentas para o modelo de implantação do Gerenciador de Recursos é uma das alterações mais importantes que você precisa considerar em seus planos de migração.

## <a name="how-long-will-the-management-plane-downtime-be"></a>O tempo de inatividade do plano de gerenciamento será de quanto tempo? 

Isso depende do número de recursos que estão sendo migrados. Para implantações menores (algumas dezenas de VMs), a migração inteira deverá levar menos de uma hora. Para implantações de larga escala (centenas de VMs), a migração poderá levar algumas horas.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Poderei reverter depois que meus recursos de migração forem confirmados no Gerenciador de Recursos? 

É possível anular a migração, desde que os recursos estejam no estado preparado. Não há suporte para reversão depois que os recursos forem migrados com êxito por meio da operação de confirmação.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Poderei reverter minha migração se a operação de confirmação falhar? 

Não será possível anular a migração se a operação de confirmação falhar. Todas as operações de migração, incluindo a operação de confirmação, são idempotentes. Portanto, recomendamos que você repita a operação após um curto período. Se a falha ainda persistir, crie um tíquete de suporte ou uma postagem no fórum com a marcação ClassicIaaSMigration em nosso [fórum sobre VMs](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows).

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Será necessário comprar outro circuito de Rota Expressa se eu precisar aproveitar usar a IaaS no Gerenciador de Recursos? 

Nº Habilitamos recentemente [a movimentação dos circuitos da ExpressRoute do clássico para o modelo de implantação do Gerenciador de Recursos](../articles/expressroute/expressroute-move.md). Você não precisará comprar um novo circuito de ExpressRoute se já tiver um.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>E se eu tiver configurado políticas de Controle de Acesso Baseado em Função para meus recursos clássicos de IaaS? 

Durante a migração, os recursos se transformam do clássico para o Gerenciador de Recursos. Portanto, é recomendável planejar as atualizações da política de RBAC que precisam ocorrer após a migração.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Fiz backup de minhas VMs clássicas em um cofre. Posso migrar minhas VMs de modo clássico para modo do Resource Manager e protegê-los em um cofre dos Serviços de Recuperação?

<a name="vault">Quando</a> você mover uma VM do modo clássico para o modo do Resource Manager, os backups feitos antes da migração não serão migrados para a VM recém-migrada do Resource Manager. No entanto, caso deseje manter os backups das VMs clássicas, siga estas etapas antes da migração. 

1. No cofre dos Serviços de Recuperação, acesse a guia **Itens Protegidos** e selecione a VM. 
2. Clique em Parar Proteção. Deixe a opção *Excluir dados de backup associados* **desmarcada**.

> [!NOTE]
> Você será cobrado o custo da instância de backup enquanto mantiver os dados. As cópias de backup serão removidas de acordo com o período de retenção. No entanto, a última cópia de backup sempre é mantida até você excluir explicitamente os dados de backup. É recomendável verificar o período de retenção da Máquina virtual e o gatilho "Excluir Dados de Backup" no item protegido no cofre após o término do período de retenção. 
>
>

Para migrar a máquina virtual para o modo do Resource Manager, 

1. Exclua a extensão de backup/instantâneo da VM.
2. Migre a máquina virtual do modo clássico para o modo do Gerenciador de Recursos. As informações de armazenamento e de rede correspondentes à máquina virtual também precisam ser migradas para o modo do Resource Manager.

Além disso, caso deseje fazer backup da VM migrada, acesse a folha de gerenciamento da Máquina Virtual para [habilitar o backup](../articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Posso validar minha assinatura ou meus recursos para ver se eles podem fazer a migração? 

Sim. Na opção de migração com suporte de plataforma, a primeira etapa para preparar a migração é validar se os recursos podem fazer a migração. Caso a operação de validação falhe, você recebe todas as mensagens relativas a todos os motivos pelos quais a migração não pode ser concluída.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>O que acontecerá se eu encontrar um erro de cota ao preparar os recursos de IaaS para migração? 

É recomendável anular a migração e, em seguida, registrar uma solicitação de suporte para aumentar as cotas na região onde você está migrando as VMs. Depois que a solicitação de cota for aprovada, você poderá iniciar a execução das etapas de migração novamente.

## <a name="how-do-i-report-an-issue"></a>Como faço para relatar um problema? 

Poste suas perguntas e dúvidas sobre migração em nosso [fórum sobre VMs](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesforWindows), com a palavra-chave ClassicIaaSMigration. É recomendável postar todas as suas dúvidas neste fórum. Caso você tenha um contrato de suporte, será possível registrar um tíquete de suporte.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>E se eu não gostar dos nomes dos recursos que a plataforma escolheu durante a migração? 

Todos os recursos para os quais você fornecer nomes explicitamente no modelo de implantação clássica são retidos durante a migração. Em alguns casos, novos recursos são criados. Por exemplo: uma interface de rede é criada para cada VM. No momento, não há suporte para a capacidade de controlar os nomes dos novos recursos criados durante a migração. Registre seus votos para esse recurso no [fórum de comentários do Azure](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Posso migrar circuitos de ExpressRoute usados em assinaturas com links de autorização? 

Os circuitos de ExpressRoute que usam links de autorização entre assinaturas não podem ser migrados automaticamente sem tempo de inatividade. Temos orientações sobre como eles podem ser migrados usando as etapas manuais. Confira [Migrar circuitos de ExpressRoute e redes virtuais associadas do modelo de implantação clássico para o Resource Manager](../articles/expressroute/expressroute-migration-classic-resource-manager.md) para obter etapas e mais informações.

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Recebi a mensagem *"A VM está informando o status geral do agente como Não Pronto. Portanto, a VM não pode ser migrada. Certifique-se de que o Agente da VM esteja informando o status geral do agente como Pronto"* ou *"A VM contém uma Extensão cujo status não está sendo informado. Portanto, esta VM não pode ser migrada."*

Essa mensagem é recebida quando a VM não tem conectividade de saída com a Internet. O agente de VM utiliza conectividade de saída para acessar a conta de armazenamento do Azure para atualizar o status do agente a cada cinco minutos.
