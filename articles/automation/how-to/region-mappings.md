---
title: Mapeamentos de espaço de trabalho de automação e Log Analytics do Azure
description: Este artigo descreve os mapeamentos permitidos entre uma conta de automação e um Log Analytics espaço de trabalho para dar suporte à solução
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 05/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cd4500b4ef6492f0b6499bb1e9aa1a773313e860
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/25/2019
ms.locfileid: "68498362"
---
# <a name="workspace-mappings"></a>Mapeamentos de espaço de trabalho

Ao habilitar soluções como Gerenciamento de Atualizações, Controle de Alterações e inventário ou a solução iniciar/parar VMs fora do horário comercial, somente determinadas regiões têm suporte para vincular um espaço de trabalho Log Analytics e uma conta de automação. Esse mapeamento só se aplica à conta de automação e ao espaço de trabalho Log Analytics. Os recursos que se reportam à sua conta de automação ou Log Analytics espaço de trabalho podem residir em outras regiões.

## <a name="supported-mappings"></a>Mapeamentos com suporte

A tabela abaixo mostra os mapeamentos com suporte:

|**Região do Espaço de Trabalho do Log Analytics**|**Região da Automação do Azure**|
|---|---|
|**EUA**||
|EastUS<sup>1</sup>|EastUS2|
|WestUS2|WestUS2|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|**Canadá**||
|Canadá Central|Canadá Central|
|**Pacífico Asiático**||
|AustraliaSoutheast|AustraliaSoutheast|
|SoutheastAsia|SoutheastAsia|
|CentralIndia|CentralIndia|
|JapanEast|JapanEast|
|**Europa**||
|UKSouth|UKSouth|
|WestEurope|WestEurope|
|**US Gov**||
|USGovVirginia|USGovVirginia|

<sup>1</sup> o mapeamento de lesteus para log Analytics espaços de trabalho a contas de automação não é uma região exata para o mapeamento de região, mas é o mapeamento correto.

<sup>2</sup> devido a restrições de capacidade, a região não está disponível durante a criação de novos recursos. Isso inclui contas de automação e espaços de trabalho do Log Analytics. No entanto, os recursos vinculados preexistentes na região devem continuar funcionando.

## <a name="unlink-workspace"></a>Desvincular o workspace

Se você decidir que não deseja mais integrar sua conta de automação a um espaço de trabalho Log Analytics, você poderá desvincular sua conta diretamente da portal do Azure. Antes de prosseguir, primeiro você precisa remover as Gerenciamento de Atualizações, Controle de Alterações e inventário, ou as soluções iniciar/parar VMs fora do horário comercial se você as estiver usando. Se você não removê-los, esse processo será impedido de continuar. Examine o artigo sobre a solução específica que você importou para entender as etapas necessárias para removê-la.

Depois de remover essas soluções, você poderá executar as etapas a seguir para desvincular sua conta de Automação.

> [!NOTE]
> Algumas soluções, incluindo versões anteriores da solução de monitoramento do Azure SQL, podem ter criado ativos de automação e também podem precisar ser removidas antes de desvincular o workspace.

1. No portal do Azure, abra sua conta da Automação e, na página da conta da Automação, selecione **Workspace vinculado** na seção **Recursos Relacionados** à esquerda.

2. Na página Desvincular o workspace, clique em **Desvincular o workspace**. Você receberá uma solicitação verificando se deseja continuar.

3. Enquanto a Automação do Azure tenta desvincular a conta do seu espaço de trabalho do Log Analytics, você pode acompanhar o progresso no menu **Notificações**.

Se você tiver usado a solução Gerenciamento de Atualizações, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Atualizar agendas - Cada uma terá nomes que correspondam às implantações de atualizações que você criou)

* Grupos de Hybrid Worker criados para a solução-cada um será nomeado de `machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8`forma semelhante a).

Se você tiver usado a solução Iniciar/parar VMs durante os horários fora de pico, como opção, convém remover os itens a seguir que não serão mais necessários após a remoção da solução.

* Iniciar e parar agendas de runbook da VM
* Iniciar e parar runbooks da VM
* Variáveis

Como alternativa, você também pode desvincular seu espaço de trabalho de sua conta de automação de seu espaço de trabalho Log Analytics. Em seu espaço de trabalho, selecione **conta de automação** em **recursos relacionados**. Na página conta de automação, selecione **desvincular conta**.

## <a name="next-steps"></a>Próximas etapas

Saiba como carregar as seguintes soluções:

Gerenciamento de Atualizações e Controle de Alterações e inventário:

* De uma [máquina virtual](../automation-onboard-solutions-from-vm.md)
* Da sua [conta de automação](../automation-onboard-solutions-from-automation-account.md)
* Ao [procurar vários computadores](../automation-onboard-solutions-from-browse.md)
* De um [runbook](../automation-onboard-solutions.md)

Iniciar/Parar VMs durante os horários inativos

* [Implantar VMs de início/parada fora do horário de expediente](../automation-solution-vm-management.md)
