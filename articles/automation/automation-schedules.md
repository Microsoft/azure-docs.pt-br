---
title: "Agendas na Automação do Azure | Microsoft Docs"
description: "As agendas de automação são usadas para agendar o início automático de runbooks na Automação do Azure. Descreve como criar e gerenciar uma agenda para que você possa iniciar um runbook automaticamente em um determinado momento ou em um agendamento recorrente."
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: 446dc79202938ee96cbb090345d9c191060afe76
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Agendando um runbook na Automação do Azure
Para agendar um runbook na Automação do Azure para iniciar em um horário específico, você deve vinculá-lo a uma ou mais agendas. Uma agenda pode ser configurada para ser executada uma vez ou em um agendamento recorrente horário ou diário para runbooks no portal clássico do Azure e runbooks no portal do Azure. Além disso, você pode agendá-las em caráter semanal, mensal, em dias específicos da semana ou do mês ou em um dia determinado do mês.  Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela.

> [!NOTE]
> Atualmente, os agendamentos não dão suporte às configurações DSC de Automação do Azure.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Cmdlets do Windows PowerShell
Os cmdlets na tabela a seguir são usados para criar e gerenciar agendas com o Windows PowerShell na Automação do Azure. Eles são enviados como parte do [módulo do PowerShell do Azure](/powershell/azure/overview).

| Cmdlets | Descrição |
|:--- |:--- |
| **Cmdlets do Azure Resource Manager** | |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Recupera uma agenda. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Cria uma nova agenda. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Remove uma agenda. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Define as propriedades de uma agenda existente. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Recupera runbooks agendados. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associa um runbook a uma agenda. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissocia um runbook de uma agenda. |
| **Cmdlets do Azure Service Management** | |
| [Get-AzureAutomationSchedule](/powershell/module/azure/get-azureautomationschedule?view=azuresmps-3.7.0) |Recupera uma agenda. |
| [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) |Cria uma nova agenda. |
| [Remove-AzureAutomationSchedule](/powershell/module/azure/remove-azureautomationschedule?view=azuresmps-3.7.0) |Remove uma agenda. |
| [Set-AzureAutomationSchedule](/powershell/module/azure/set-azureautomationschedule?view=azuresmps-3.7.0) |Define as propriedades de uma agenda existente. |
| [Get-AzureAutomationScheduledRunbook](/powershell/module/azure/get-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Recupera runbooks agendados. |
| [Register-AzureAutomationScheduledRunbook](/powershell/module/azure/register-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Associa um runbook a uma agenda. |
| [Unregister-AzureAutomationScheduledRunbook](/powershell/module/azure/unregister-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Dissocia um runbook de uma agenda. |

## <a name="creating-a-schedule"></a>Criando uma agenda
Você pode criar uma nova agenda para runbooks no portal do Azure, no portal clássico ou com o Windows PowerShell. Você também tem a opção de criar uma nova agenda ao vincular um runbook a uma agenda usando o portal clássico do Azure ou o portal do Azure.

> [!NOTE]
> A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado.  Para evitar o impacto em seus runbooks e os processos que eles automatizam, primeiro você deve testar quaisquer runbooks que tenham agendas vinculadas com uma conta de Automação dedicada a teste.  Isso valida que seus runbooks agendados continuam funcionando corretamente e, caso não continuem, você pode solucionar problemas e aplicar as alterações necessárias antes de migrar a versão atualizada do runbook para produção.  
>  Sua conta de Automação não recebe automaticamente nenhuma nova versão dos módulos, a menos que você os tenha atualizado manualmente selecionando a opção [Atualizar Módulos do Azure](automation-update-azure-modules.md) em **Módulos**. 
>  

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Para criar uma nova agenda no portal do Azure
1. No portal do Azure, em sua conta de Automação, selecione **Agendas** na seção **Recursos Compartilhados** à esquerda. 
2. Clique em **Adicionar um agendamento** na parte superior da página.
4. No painel **Novo agendamento**, digite um **Nome** e, opcionalmente, uma **Descrição** para a nova agenda.
5. Selecione se a agenda deve ser executada uma vez ou de forma recorrente selecionando **Uma vez** ou **Recorrência**.  Se você selecionar **Uma vez**, especifique uma **Hora de início** e clique em **Criar**.  Se você selecionar **Recorrência**, especifique uma **Hora de início** e a frequência com que deseja que o runbook seja repetido: a cada **hora**, **dia**, **semana** ou **mês**.  Se você selecionar **semana** ou **mês** na lista suspensa, a **Opção de recorrência** aparecerá no painel e, após a seleção, o painel **Opção de recorrência** será apresentado e você poderá selecionar o dia da semana, se tiver selecionado **semana**.  Se tiver selecionado **mês**, você poderá escolher por **dias da semana** ou dias específicos do mês no calendário e, por fim, se deseja executar no último dia do mês ou não. Em seguida, clique em **OK**.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Para criar uma nova agenda no portal clássico do Azure
1. No portal clássico do Azure, selecione Automação e, em seguida, selecione no nome de uma conta de Automação.
2. Selecione a guia **Ativos** .
3. Clique em **Adicionar Configuração**na parte inferior da tela.
4. Clique em **Adicionar Agenda**.
5. Digite um **Nome** e, opcionalmente, uma **Descrição** para a nova agenda. A agenda pode ser executada **Uma vez**, **A cada hora**, **Diariamente**, **Semanalmente** ou **Mensalmente**.
6. Especifique uma **Hora de início** e outras opções, dependendo do tipo de agenda selecionado.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Para criar uma nova agenda com o Windows PowerShell
Você pode usar o cmdlet [New-AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) para criar um novo agendamento na Automação do Azure para runbooks clássicos ou o cmdlet [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) para runbooks no Portal do Azure. Você deve especificar a hora de início para a agenda e a frequência de execução.

Os comandos de exemplo a seguir mostram como criar uma agenda para os dias 15 e o 30 de cada mês usando um cmdlet do Azure Resource Manager.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

Os comandos de exemplo a seguir mostram como criar uma nova agenda que é executada todos os dias às 15h30 começando em 20 de janeiro de 2015, com um cmdlet do Gerenciamento de Serviços do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Vinculando uma agenda a um runbook
Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela. Se um runbook tiver parâmetros, você pode fornecer valores para eles. Você deve fornecer valores para todos os parâmetros obrigatórios e pode fornecer valores para os opcionais.  Esses valores são usados sempre que o runbook é iniciado por essa agenda.  Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Para vincular uma agenda a um runbook com o portal do Azure
1. No portal do Azure, em sua conta da Automação, selecione **Runbooks** na seção **Automação de Processos** à esquerda.
2. Clique no nome do runbook para agendar.
3. Se o runbook não estiver vinculado atualmente a uma agenda, você terá a opção de criar uma nova agenda ou de vincular a uma agenda existente.  
4. Se o runbook tiver parâmetros, você poderá selecionar a opção **Modificar configurações de execução (Padrão: Azure)** e será apresentado o painel **Parâmetros**, no qual você poderá inserir as informações de forma adequada.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Para vincular uma agenda a um runbook com o portal clássico do Azure
1. No portal clássico do Azure, selecione **Automação** e, em seguida, clique no nome de uma conta de Automação.
2. Selecione a guia **Runbooks** .
3. Clique no nome do runbook para agendar.
4. Clique na guia **Agenda** .
5. Se o runbook não estiver vinculado atualmente a uma agenda, você terá a opção **Link para uma nova Agenda** ou **Link para uma Agenda Existente**.  Se o runbook estiver atualmente vinculado a uma agenda, clique em **Vincular** na parte inferior da janela para acessar essas opções.
6. Se o runbook tiver parâmetros, você será solicitado a inserir seus valores.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Para vincular uma agenda a um runbook com o Windows PowerShell
Você pode usar [Register-AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) para vincular um agendamento a um runbook clássico ou o cmdlet [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) para runbooks no Portal do Azure.  Você pode especificar valores para os parâmetros do runbook com o parâmetro Parameters. Confira [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md) para saber mais sobre como especificar valores de parâmetro.

Os comandos de exemplo a seguir mostram como vincular uma agenda a um runbook usando um cmdlet do Gerenciador de Recursos do Azure com parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
Os comandos de exemplo a seguir mostram como vincular uma agenda usando um cmdlet do Gerenciamento de Serviços do Azure com parâmetros.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>Desabilitando uma agenda
Quando você desabilita uma agenda, todos os runbooks vinculados a ela deixam de ser executados segundo ela. Você pode desabilitar manualmente uma agenda ou definir uma hora de expiração para agendas com uma frequência ao criá-las. Quando o tempo de expiração é atingido, a agenda é desabilitada.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Para desabilitar uma agenda no portal do Azure
1. No portal do Azure, em sua conta de Automação, selecione **Agendas** na seção **Recursos Compartilhados** à esquerda.
2. Clique no nome de uma agenda para abrir o painel de detalhes.
3. Altere **Habilitado** para **Não**.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Para desabilitar uma agenda no portal clássico do Azure
Você pode desabilitar uma agenda no portal clássico do Azure da página Detalhes da Agenda.

1. No portal clássico do Azure, selecione Automação e, em seguida, clique no nome de uma conta de Automação.
2. Selecione a guia Ativos.
3. Clique no nome de uma agenda para abrir sua página de detalhes.
4. Altere **Habilitado** para **Não**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Para desabilitar uma agenda com o Windows PowerShell
Você pode usar o cmdlet [Set-AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) para alterar as propriedades de um agendamento existente para um runbook clássico ou o cmdlet [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) para runbooks no Portal do Azure. Para desabilitar a agenda, especifique **false** para o parâmetro **IsEnabled**.

Os comandos de exemplo a seguir mostram como desabilitar uma agenda para um runbook usando um cmdlet do Gerenciador de Recursos do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

Os comandos de exemplo a seguir mostram como desabilitar uma agenda usando o cmdlet do Gerenciamento de Serviços do Azure.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar runbooks na Automação do Azure, confira [Iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md) 

