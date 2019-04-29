---
title: Usar módulos Az na Automação do Azure
description: Este artigo fornece informações sobre como usar módulos Az na Automação do Azure
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 02/08/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a076c924d57aadfae477a5df0d128aad8e67af60
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61305379"
---
# <a name="az-module-support-in-azure-automation"></a>Suporte ao módulo Az na Automação do Azure

A automação do Azure oferece suporte à capacidade de usar o [módulo AZ do Azure PowerShell](/powershell/azure/new-azureps-module-az?view=azps-1.1.0) em seus runbooks. O módulo Az não é importado automaticamente em Contas de Automação novas ou existentes. Este artigo discute como usar módulos Az com a Automação do Azure.

## <a name="considerations"></a>Considerações

Há muitos pontos a considerar ao usar o módulo Az na Automação do Azure. Runbooks e módulos podem ser usados pelas soluções de nível superior na sua Conta de Automação. A edição de runbooks ou a atualização de módulos podem potencialmente causar problemas com os runbooks. É preciso testar todos os runbooks e soluções cuidadosamente em uma Conta de Automação separada antes de importar os novos módulos `Az`. Todas as alterações aos módulos podem afetar negativamente soluções de nível superior, como o Gerenciamento de Atualizações e o Início/Interrupção de VMs durante as horas de folga. É recomendável não alterar módulos e runbooks em Contas de Automação que contêm qualquer solução. Esse comportamento não é específico dos módulos Az. Esse comportamento deve considerado ao introduzir as alterações em sua Conta de Automação.

Importar um módulo `Az` em sua Conta de Automação não importa automaticamente o módulo na sessão do PowerShell usada pelos runbooks. Os módulos são importados na sessão do PowerShell nas seguintes situações:

* Quando um cmdlet de um módulo é chamado de um runbook
* Quando um runbook o importa explicitamente com o cmdlet `Import-Module`
* Quando outro módulo dependente do módulo é importado em uma sessão do PowerShell

> [!IMPORTANT]
> É importante garantir que os runbooks em uma Conta de Automação importem somente os módulos `Az` ou `AzureRM` nas sessões do PowerShell usadas por runbooks, e não ambos. Se `Az` for importado antes de `AzureRM` em um runbook, o runbook será concluído, mas um [erro fazendo referência ao método get_SerializationSettings](troubleshoot/runbooks.md#get-serializationsettings) será exibido nos fluxos de trabalho, e os cmdlets podem não ser executados corretamente. Se você importar `AzureRM` e depois `Az`, o runbook ainda será concluído, mas um erro nos fluxos de trabalho será exibido informando que `Az` e `AzureRM` não podem ser importados na mesma sessão, nem usados no mesmo runbook.

## <a name="migrating-to-az-modules"></a>Migrar para módulos Az

É recomendável testar a migração para usar módulos Az ao invés de módulos AzureRM em uma Conta de Automação de teste. Depois que essa Conta de Automação for criada, use as seguintes etapas para garantir que a migração ocorra sem problemas:

### <a name="stop-and-unschedule-all-runbook-that-uses-azurerm-modules"></a>Parar e desfazer o agendamento de todos os runbooks que usam os módulos AzureRM

Para garantir que você não execute os runbooks existentes que usam os cmdlets `AzureRM`, é preciso parar e cancelar o agendamento de todos os runbooks que usam os módulos `AzureRM`. É possível ver quais agendamentos existem e quais devem ser removidos executando o seguinte exemplo:

  ```powershell-interactive
  Get-AzureRmAutomationSchedule -AutomationAccountName "<AutomationAccountName>" -ResourceGroupName "<ResourceGroupName>" | Remove-AzureRmAutomationSchedule -WhatIf
  ```

É importante examinar cada agendamento separadamente para garantir que você possa reagendá-los no futuro para seus runbooks, se necessário.

### <a name="import-the-az-modules"></a>Importar os módulos Az

Importe somente os módulos Az que são necessários para seus runbooks. Não importe o módulo `Az` de rollup, pois ele inclui todos os módulos `Az.*` a serem importados. Essa orientação é a mesma para todos os módulos.

O módulo [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) é uma dependência para os outros módulos `Az.*`. Por esse motivo, esse módulo precisa ser importado na sua Conta de Automação antes da importação de todos os outros módulos.

Na sua Conta de Automação, selecione **Módulos** em **Recursos Compartilhados**. Clique em **Procurar na Galeria** para abrir a página **Procurar na Galeria**.  Na barra de pesquisa, digite o nome do módulo (como `Az.Accounts`). Na página Módulo do PowerShell, clique em **Importar** para importar o módulo para a sua Conta de Automação.

![Importar módulos da Conta de Automação](media/az-modules/import-module.png)

Esse processo de importação também pode ser feito por meio da [Galeria do PowerShell](https://www.powershellgallery.com) pesquisando o módulo. Depois de encontrar o módulo, selecione-o e, na guia **Automação do Azure**, clique em **Implantar na Automação do Azure**.

![Importar módulos diretamente da galeria](media/az-modules/import-gallery.png)

## <a name="test-your-runbooks"></a>Testar seus runbooks

Assim que os módulos `Az` são importados em sua Conta de Automação, você pode começar a editar os runbooks para usar o módulo Az. A maioria dos cmdlets tem o mesmo nome, exceto `AzureRM`, que foi alterado para `Az`. Para obter uma lista de módulos que não seguem esse processo, consulte a [lista de exceções](/powershell/azure/migrate-from-azurerm-to-az?view=azps-1.1.0#change-module-imports-and-cmdlet-names).

Uma maneira de testar os runbooks antes de modificar o seu runbook para usar os novos cmdlets é usar `Enable-AzureRMAlias -Scope Process` no início de um runbook. Ao adicionar isso ao runbook, ele pode ser executado sem alterações.

## <a name="after-migration-details"></a>Detalhes pós-migração

Depois que a migração é concluída, não inicie mais runbooks usando módulos `AzureRM` na conta. Também é recomendável não importar nem atualizar módulos `AzureRM` nessa conta. A partir de agora, considere essa conta migrada para `Az` e opere-a apenas com módulos `Az`. Quando uma nova Conta de Automação for criada, os módulos `AzureRM` existentes continuarão sendo instalados e os runbooks de tutorial continuarão sendo criados com cmdlets `AzureRM`. Esses runbooks não deve ser executados.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como usar módulos Az, consulte [Introdução ao módulo Az](/powershell/azure/get-started-azureps?view=azps-1.1.0).
