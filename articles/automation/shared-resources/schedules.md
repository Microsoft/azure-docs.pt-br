---
title: Gerenciar agendamentos na Automação do Azure
description: Este artigo mostra como criar e trabalhar com um agendamento na Automação do Azure.
services: automation
ms.subservice: shared-capabilities
ms.date: 09/10/2020
ms.topic: conceptual
ms.openlocfilehash: 844a45c9b596522b949443b6edc311308da7806c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90004605"
---
# <a name="manage-schedules-in-azure-automation"></a>Gerenciar agendamentos na Automação do Azure

Para agendar um runbook na Automação do Azure para iniciar em um horário específico, você deve vinculá-lo a uma ou mais agendas. Um agendamento pode ser configurado para ser executado uma vez ou de forma recorrente a cada hora ou diariamente para runbooks no portal do Azure. Você também pode agendá-los para semanal, mensal, dias específicos da semana ou dias do mês ou um determinado dia do mês. Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela.

> [!NOTE]
> A Automação do Azure dá suporte a horário de verão, que é agendado adequadamente para operações de automação.

> [!NOTE]
> Atualmente, os agendamentos não estão habilitados para as configurações de DSC de Automação do Azure.

## <a name="powershell-cmdlets-used-to-access-schedules"></a>Cmdlets do PowerShell usados para acessar agendamentos

Os cmdlets na tabela a seguir são usados para criar e gerenciar agendamentos de Automação com o PowerShell. Eles são fornecidos como parte dos [módulos AZ](modules.md#az-modules).

| Cmdlets | Descrição |
|:--- |:--- |
| [Get-AzAutomationSchedule](/powershell/module/Az.Automation/Get-AzAutomationSchedule) |Recupera uma agenda. |
| [Get-AzAutomationScheduledRunbook](/powershell/module/az.automation/get-azautomationscheduledrunbook) |Recupera runbooks agendados. |
| [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) |Cria uma nova agenda. |
| [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) |Associa um runbook a uma agenda. |
| [Remove-AzAutomationSchedule](/powershell/module/Az.Automation/Remove-AzAutomationSchedule) |Remove uma agenda. |
| [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) |Define as propriedades de uma agenda existente. |
| [Unregister-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook) |Dissocia um runbook de uma agenda. |

## <a name="create-a-schedule"></a>Criar um agendamento

Você pode criar um novo agendamento para runbooks no portal do Azure ou com o PowerShell. Para evitar impactos em seus runbooks e os processos que eles automatizam, primeiro você deve testar quaisquer runbooks que tenham agendamentos vinculados a uma conta de Automação dedicada a testes. Um teste valida que seus runbooks agendados continuam funcionando corretamente. Se encontrar algum problema, você poderá solucionar e aplicar as alterações necessárias antes de migrar a versão atualizada do runbook para produção.

> [!NOTE]
> Sua conta de Automação não recebe automaticamente nenhuma nova versão dos módulos, a menos que você os tenha atualizado manualmente selecionando a opção [Atualizar módulos do Azure](../automation-update-azure-modules.md) em **Módulos**. A Automação do Azure usa os módulos mais recentes de sua conta de Automação quando um novo trabalho agendado é executado. 

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Para criar um novo agendamento no portal do Azure

1. Na sua conta de automação, no painel esquerdo, selecione **agendas** em **recursos compartilhados**.
2. Na página **agendas** , selecione **Adicionar um agendamento**.
3. Na página **novo agendamento** , insira um nome e, opcionalmente, insira uma descrição para a nova agenda.

    >[!NOTE]
    >Atualmente, os agendamentos de automação não dão suporte ao uso de caracteres especiais no nome da agenda.
    >

4. Selecione se a agenda é executada uma vez ou em um agendamento recorrente selecionando **uma vez** ou **repetindo**. Se você selecionar **Uma vez**, especifique uma hora de início e clique em **Criar**. Se você selecionar **Recorrente**, especifique uma hora de início. Para **Repetir a cada**, selecione com que frequência você deseja que o runbook se repita. Selecione por hora, dia, semana ou mês.

    * Se você selecionar **Semana**, os dias da semana serão apresentados para sua escolha. Selecione quantos dias você desejar. A primeira execução da agenda de ocorrerá no primeiro dia selecionado após a hora de início. Por exemplo, para escolher um agendamento de fim de semana, selecione sábado e domingo.

    ![Definir agendamento recorrente de fim de semana](../media/schedules/week-end-weekly-recurrence.png)

    * Se você selecionar **Mês**, terá diferentes opções. Para a opção **Ocorrências mensais**, selecione **Dias do mês** ou **Dias da semana**. Se você selecionar **Dias do mês**, você verá um calendário onde poderá escolher quantos dias quiser. Se você escolher uma data, como o 31º dia, que não ocorre no mês atual, a programação não será executada. Se você quiser que o agendamento seja executado no último dia, escolha **Sim** em **Executar no último dia do mês**. Se você selecionar **Dias da semana**, a opção **Repetir a cada** será exibida. Escolher **primeira**, **segundo**, **terceira**, **quarto**, ou **último**. Finalmente, escolha um dia para repetir.

    ![Agendamento mensal no primeiro, décimo-quinto e no último dia do mês](../media/schedules/monthly-first-fifteenth-last.png)

5. Ao terminar, selecione **Criar**.

### <a name="create-a-new-schedule-with-powershell"></a>Criar um novo agendamento com o PowerShell

Use o cmdlet [New-AzAutomationSchedule](/powershell/module/Az.Automation/New-AzAutomationSchedule) para criar agendamentos. Especifique a hora de início para a agenda e a frequência de execução. Os exemplos a seguir mostram como criar vários cenários de agendamento diferentes.

>[!NOTE]
>Atualmente, os agendamentos de automação não dão suporte ao uso de caracteres especiais no nome da agenda.
>

#### <a name="create-a-one-time-schedule"></a>Criar um agendamento único

O exemplo a seguir cria um agendamento único.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Criar um agendamento recorrente

O exemplo a seguir mostra como criar um agendamento recorrente que é executado todos os dias às 13:00 por um ano.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Criar um agendamento recorrente semanal

O exemplo a seguir mostra como criar um agendamento semanal que é executado somente em dias da semana.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Criar um agendamento recorrente semanal para fins de semana

O exemplo a seguir mostra como criar um agendamento semanal que é executado somente em fins de semana.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-the-first-fifteenth-and-last-days-of-the-month"></a>Criar um agendamento recorrente para o primeiro, décimo-quinto e último dia do mês

O exemplo a seguir mostra como criar um agendamento recorrente que é executado no primeiro, décimo-quinto e no último dia de um mês.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="link-a-schedule-to-a-runbook"></a>Vincular um agendamento a um runbook

Um runbook pode ser vinculado a várias agendas, e uma agenda pode ter vários runbooks vinculados a ela. Se um runbook tiver parâmetros, você poderá fornecer valores para eles. Você precisa fornecer valores para todos os parâmetros obrigatórios e também pode fornecer valores para os opcionais. Esses valores são usados sempre que o runbook é iniciado por essa agenda. Você pode anexar o mesmo runbook a outra agenda e especificar valores de parâmetro diferentes.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Vincular um agendamento a um runbook com o portal do Azure

1. No portal do Azure, na sua conta de automação, selecione **Runbooks** em **Automação de processos**.
1. Selecione o nome do runbook para agendar.
1. Se o runbook não estiver vinculado atualmente a um agendamento, você terá a opção de criar um novo agendamento ou de vincular a um agendamento existente.
1. Se o runbook tiver parâmetros, você poderá selecionar a opção **Modificar configurações de execução (padrão: Azure)** e o painel **Parâmetros** será exibido. Você pode inserir informações de parâmetro aqui.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Vincular um agendamento a um runbook com o PowerShell

Use o cmdlet [Register-AzAutomationScheduledRunbook](/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook) para vincular um agendamento. Você pode especificar valores para os parâmetros do runbook com o parâmetro Parameters. Para mais informações para especificar os valores do parâmetro, consulte [Como iniciar um runbook na Automação do Azure](../start-runbooks.md).
O exemplo a seguir mostra como vincular um agendamento a um runbook usando um cmdlet do Azure Resource Manager com parâmetros.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="schedule-runbooks-to-run-more-frequently"></a>Agendar runbooks para serem executados com mais frequência

O intervalo mais frequente para o qual a Automação do Azure pode ser configurada é uma hora. Se você precisar de agendamentos para executar com mais frequência do que isso, há duas opções:

* Criar um [webhook](../automation-webhooks.md) para o runbook e usar o [Aplicativos Lógicos do Azure](../../logic-apps/logic-apps-overview.md) para chamar o webhook. Os Aplicativos Lógicos do Azure fornecem granularidade mais refinada para definir um agendamento.

* Crie quatro agendamentos que iniciam em intervalos de 15 minutos e são executados a cada hora. Este cenário permite que o runbook seja executado a cada 15 minutos com as diferentes agendas.

## <a name="disable-a-schedule"></a>Desabilitar um agendamento

Quando você desabilita uma agenda, qualquer runbook vinculados a ela deixam de ser executados segundo ela. Você pode desabilitar manualmente uma agenda ou definir uma hora de expiração para agendas com uma frequência ao criá-las. Quando o tempo de expiração é atingido, a agenda é desabilitada.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Desabilitar um agendamento no portal do Azure

1. Na sua conta de automação, no painel esquerdo, selecione **agendas** em **recursos compartilhados**.
1. Clique no nome de um agendamento para abrir o painel de detalhes.
1. Altere **Habilitado** para **Não**.

> [!NOTE]
> Se desejar desabilitar um agendamento que tenha uma hora de início no passado, você deverá alterar a data de início para uma hora no futuro antes de salvá-la.

### <a name="disable-a-schedule-with-powershell"></a>Desabilitar um agendamento com o PowerShell

Use o cmdlet [Set-AzAutomationSchedule](/powershell/module/Az.Automation/Set-AzAutomationSchedule) para alterar as propriedades de um agendamento existente. Para desabilitar o agendamento, especifique false para o parâmetro `IsEnabled`.

O exemplo a seguir mostra como desabilitar um agendamento para um runbook usando um cmdlet do Azure Resource Manager.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="remove-a-schedule"></a>Remover um agendamento

Quando estiver tudo pronto para remover seus agendamentos, você poderá usar o portal do Azure ou o PowerShell. Lembre-se de que você só pode remover um agendamento desabilitado conforme descrito na seção anterior.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Remover um agendamento usando o portal do Azure

1. Na sua conta de automação, no painel esquerdo, selecione **agendas** em **recursos compartilhados**.
2. Clique no nome de um agendamento para abrir o painel de detalhes.
3. Clique em **Excluir**.

### <a name="remove-a-schedule-with-powershell"></a>Remover um agendamento com o PowerShell

Você pode usar o cmdlet `Remove-AzAutomationSchedule` como mostrado abaixo, para excluir um agendamento.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzAutomationSchedule -AutomationAccountName $automationAccountName `
-Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os cmdlets usados para acessar agendamentos, consulte [Gerenciar módulos na Automação do Azure](modules.md).
* Para obter informações gerais sobre runbooks, consulte [Execução de runbook na Automação do Azure](../automation-runbook-execution.md).