---
title: Regiões compatíveis com o workspace do Log Analytics vinculado
description: Este artigo descreve os mapeamentos de região com suporte entre uma conta de automação e um Log Analytics espaço de trabalho, pois ele se relaciona a determinados recursos da automação do Azure.
ms.date: 02/17/2021
services: automation
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: 0599dcb57b46d1e48b4035acac8b64edbbe06912
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101720164"
---
# <a name="supported-regions-for-linked-log-analytics-workspace"></a>Regiões compatíveis com o workspace do Log Analytics vinculado

Na automação do Azure, você pode habilitar o Gerenciamento de Atualizações, Controle de Alterações e inventário e recursos de Iniciar/Parar VMs fora do horário comercial para seus servidores e máquinas virtuais. Esses recursos têm uma dependência em um espaço de trabalho Log Analytics e, portanto, exigem a vinculação do espaço de trabalho com uma conta de automação. No entanto, somente determinadas regiões têm suporte para vinculá-las. Em geral, o mapeamento *não* é aplicável se você planeja vincular uma conta de automação a um espaço de trabalho que não terá esses recursos habilitados.

Os mapeamentos discutidos aqui se aplicam apenas à vinculação do espaço de trabalho Log Analytics a uma conta de automação. Eles não se aplicam às máquinas virtuais (VMs) que estão conectadas ao espaço de trabalho vinculado à conta de automação. As VMs não são limitadas às regiões com suporte em determinado espaço de trabalho Log Analytics. Eles podem estar em qualquer região. Tenha em mente que ter as VMs em uma região diferente pode afetar os requisitos regulatórios de estado, local e país ou os requisitos de conformidade da sua empresa. Ter VMs em uma região diferente também poderia introduzir cobranças de largura de banda de dados.

Antes de conectar as VMs a um espaço de trabalho em uma região diferente, você deve examinar os requisitos e os custos potenciais para confirmar e entender as implicações legais e de custo.

Este artigo fornece os mapeamentos com suporte para habilitar e usar com êxito esses recursos em sua conta de automação.

Para obter mais informações, consulte [log Analytics espaço de trabalho e conta de automação](../../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account).

## <a name="supported-mappings"></a>Mapeamentos com suporte

> [!NOTE]
> Conforme mostrado na tabela a seguir, somente um mapeamento pode existir entre Log Analytics e a automação do Azure.

A tabela abaixo mostra os mapeamentos com suporte:

|**Região do workspace do Log Analytics**|**Região da Automação do Azure**|
|---|---|
|**EUA**||
|EastUS<sup>1</sup>|EastUS2|
|EastUS2<sup>2</sup>|EastUS|
|WestUS|WestUS|
|WestUS2|WestUS2|
|CentralUS|CentralUS|
|SouthCentralUS|SouthCentralUS|
|WestCentralUS|WestCentralUS|
|**Canadá**||
|Canadá Central|Canadá Central|
|**Pacífico Asiático**||
|AustraliaEast|AustraliaEast|
|AustraliaSoutheast|AustraliaSoutheast|
|EastAsia|EastAsia|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|ChinaEast2<sup>3</sup>|ChinaEast2|
|JapanEast|JapanEast|
|**Europa**||
|NorthEurope|NorthEurope|
|FranceCentral|FranceCentral|
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|SwitzerlandNorth|SwitzerlandNorth|
|**US Gov**||
|USGovVirginia|USGovVirginia|
|USGovArizona<sup>3</sup>|USGovArizona|



<sup>1</sup> O mapeamento EastUS para workspaces do Log Analytics para contas de Automação não é um mapeamento exato de região para região, mas é o mapeamento correto.

<sup>2</sup> o mapeamento EastUS2 para os espaços de trabalho log Analytics para contas de automação não é um mapeamento de região para região exato, mas é o mapeamento correto.

<sup>3</sup> nesta região, somente gerenciamento de atualizações tem suporte e outros recursos como controle de alterações e inventário não estão disponíveis no momento.

## <a name="unlink-a-workspace"></a>Desvincular um workspace

Caso decida que não quer mais integrar sua conta de Automação ao workspace do Log Analytics, você pode desvincular a conta diretamente no portal do Azure. Antes de continuar, primeiro [remova](move-account.md#remove-features) os recursos Gerenciamento de Atualizações, Controle de Alterações e inventário, e Iniciar/Parar VMs fora do horário comercial se estiver usando-os. Se não os remover, você não poderá concluir a operação de desvinculação.

Com os recursos removidos, siga as etapas abaixo para desvincular sua conta de Automação.

> [!NOTE]
> Alguns recursos, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de Automação que precisam ser removidos antes de desvincular o workspace.

1. No portal do Azure, abra sua conta de Automação. Na página da conta de Automação, selecione **Workspace vinculado** em **Recursos relacionados**.

2. Na página Desvincular workspace, selecione **Desvincular workspace**. Você receberá um aviso perguntando se você deseja continuar.

3. Enquanto a Automação do Azure desvincula a conta do seu workspace do Log Analytics, você pode acompanhar o progresso em **Notificações** no menu.

4. Se usou o Gerenciamento de Atualizações, convém remover, opcionalmente, os seguintes itens que não são mais necessários:

    * Agendamentos de atualização: cada um tem um nome que corresponde à implantação de atualização que você criou.
    * Grupos de trabalho híbridos criados para o recurso: Cada um tem um nome semelhante a `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`.

5. Se usou o recurso Iniciar/Parar VMs fora do horário comercial, opcionalmente, você pode remover os seguintes itens que não são mais necessários:

    * Iniciar e parar agendas de runbook da VM
    * Iniciar e parar runbooks da VM
    * Variáveis

Como alternativa, desvincule o workspace da sua conta da Automação no workspace.

1. No workspace, selecione **Conta de Automação** em **Recursos relacionados**.
2. Na página Conta de Automação, selecione **Desvincular conta**.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre o Gerenciamento de Atualizações, veja [Visão geral do recurso Gerenciamento de Atualizações](../update-management/overview.md).
* Para obter informações sobre os recursos Controle de Alterações e Inventário, veja [Visão geral do recurso Controle de Alterações e Inventário](../change-tracking/overview.md).
* Saiba mais sobre o recurso Iniciar/Parar VMs fora do horário comercial em [Visão geral do recurso Iniciar/Parar VMs fora do horário comercial](../automation-solution-vm-management.md).