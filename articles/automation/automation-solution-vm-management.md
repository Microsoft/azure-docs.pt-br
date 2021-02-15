---
title: Visão geral do recurso Iniciar/Parar VMs fora do horário comercial da Automação do Azure
description: Este artigo descreve o recurso Iniciar/Parar VMs fora do horário comercial, que inicia ou interrompe as VMs em um agendamento e os monitora proativamente de logs de Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 991ef6e7ffc26294f75ba5bd2f24c62ea6e0b421
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/09/2021
ms.locfileid: "100006999"
---
# <a name="startstop-vms-during-off-hours-overview"></a>Visão geral do recurso Iniciar/Parar VMs fora do horário comercial

O recurso Iniciar/Parar VMs fora do horário comercial inicia ou para as VMs do Azure habilitadas. Ele inicia ou interrompe computadores conforme agendamentos definidos pelo usuário, fornece informações por meio de logs de Azure Monitor e envia emails opcionais usando [grupos de ações](../azure-monitor/platform/action-groups.md). O recurso pode ser habilitado em VMs clássicas e do Azure Resource Manager na maioria dos cenários.

Esse recurso usa o cmdlet [Start-AzVm](/powershell/module/az.compute/start-azvm) para iniciar as VMs. Ele usa [Stop-AzVM](/powershell/module/az.compute/stop-azvm) para parar as VMs.

> [!NOTE]
> Embora os runbooks tenham sido atualizados para usar os novos cmdlets do módulo AZ do Azure, eles usam o alias de prefixo AzureRM.

> [!NOTE]
> O recurso Iniciar/Parar VMs fora do horário comercial foi atualizado para dar suporte às versões mais recentes dos módulos do Azure que estão disponíveis. A versão atualizada desse recurso, disponível no Marketplace, não dá suporte a módulos do AzureRM porque migramos do AzureRM para módulos AZ.

O recurso fornece uma opção de automação descentralizada de baixo custo para os usuários que desejam otimizar seus custos de VM. Você pode usar o recurso para:

- [Agendar o início e a parada de VMs](automation-solution-vm-management-config.md#schedule).
- Agendar o início e a parada de VMs em ordem crescente usando [Tags do Azure](automation-solution-vm-management-config.md#tags). Não há suporte para essa atividade em VMs clássicas.
- Parar VMs automaticamente com base no [baixo uso da CPU](automation-solution-vm-management-config.md#cpuutil).

A seguir estão as limitações do recurso atual:

- Ele gerencia VMs em qualquer região, mas só pode ser usado na mesma assinatura que sua conta da Automação do Azure.
- Ela está disponível no Azure e no Azure Governamental para qualquer região que ofereça suporte a um workspace do Log Analytics, uma conta da Automação do Azure e alertas. As regiões do Azure Governamental não dão suporte à funcionalidade de email no momento.

## <a name="prerequisites"></a>Pré-requisitos

- Os runbooks para o recurso Iniciar/Parar VMs fora do horário comercial funcionam com uma [conta Executar como do Azure](./automation-security-overview.md#run-as-accounts). A conta Executar como é o método de autenticação preferencial, pois ela usa a autenticação de certificado em vez de uma senha que poderá expirar ou ser alterada com frequência.

- Um [Azure monitor espaço de trabalho log Analytics](../azure-monitor/platform/design-logs-deployment.md) que armazena os logs de trabalho do runbook e os resultados do fluxo de trabalho em um espaço de trabalho para consultar e analisar. A conta de automação pode ser vinculada a um espaço de trabalho Log Analytics novo ou existente e os dois recursos precisam estar no mesmo grupo de recursos.

Recomendamos que você use uma conta de Automação separada para trabalhar com VMs habilitadas para o recurso Iniciar/Parar VMs fora do horário comercial. As versões de módulo do Azure são atualizadas com frequência, e seus parâmetros podem ser alterados. O recurso não é atualizado na mesma cadência e pode não funcionar com as versões mais recentes dos cmdlets que ele usa. Antes de importar os módulos atualizados para suas contas de automação de produção, recomendamos que você os importe em uma conta de automação de teste para verificar se não há nenhum problema de compatibilidade.

## <a name="permissions"></a>Permissões

Você deve ter determinadas permissões a fim de habilitar as VMs para o recurso Iniciar/Parar VMs fora do horário comercial. As permissões são diferentes dependendo se o recurso usar uma conta de Automação criada previamente e um workspace do Log Analytics ou criar uma conta e um workspace.

Você não precisará configurar permissões se for um Colaborador na assinatura e um Administrador global em seu locatário do Azure AD (Active Directory). Se você não tiver esses direitos ou precisar configurar uma função personalizada, verifique se tem as permissões descritas abaixo.

### <a name="permissions-for-pre-existing-automation-account-and-log-analytics-workspace"></a>Permissões para a conta de Automação pré-existente e um workspace do Log Analytics

Para habilitar as VMs para o recurso Iniciar/Parar VMs fora do horário comercial usando uma conta de Automação existente e um workspace do Log Analytics, você precisa das seguintes permissões no escopo do Grupo de recursos. Para saber mais sobre as funções, consulte [funções personalizadas do Azure](../role-based-access-control/custom-roles.md).

| Permissão | Escopo|
| --- | --- |
| Microsoft.Automation/automationAccounts/read | Grupo de recursos |
| Microsoft.Automation/automationAccounts/variables/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/schedules/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/runbooks/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/connections/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/certificates/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/modules/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/modules/read | Grupo de recursos |
| Microsoft.automation/automationAccounts/jobSchedules/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/jobs/write | Grupo de recursos |
| Microsoft.Automation/automationAccounts/jobs/read | Grupo de recursos |
| Microsoft.OperationsManagement/solutions/write | Grupo de recursos |
| Microsoft.OperationalInsights/workspaces/* | Grupo de recursos |
| Microsoft.Insights/diagnosticSettings/write | Grupo de recursos |
| Microsoft.Insights/ActionGroups/Write | Grupo de recursos |
| Microsoft.Insights/ActionGroups/read | Grupo de recursos |
| Microsoft.Resources/subscriptions/resourceGroups/read | Grupo de recursos |
| Microsoft.Resources/deployments/* | Grupo de recursos |

### <a name="permissions-for-new-automation-account-and-new-log-analytics-workspace"></a>Permissões para uma nova conta de Automação e um novo workspace do Log Analytics

Você pode habilitar VMs para o recurso Iniciar/Parar VMs fora do horário comercial usando uma nova conta de Automação e um novo workspace do Log Analytics. Nesse caso, você precisa das permissões definidas na seção anterior, bem como das permissões definidas nesta seção. Você também precisa das seguintes funções:

- Co-Administrator na assinatura. Essa função é necessária para criar a conta Executar como clássica se você pretende gerenciar VMs clássicas. [Contas Executar como clássicas](automation-create-standalone-account.md#create-a-classic-run-as-account) não são mais criadas por padrão.
- Associação na função de Desenvolvedor de Aplicativo do [Azure AD](../active-directory/roles/permissions-reference.md). Para obter mais informações sobre contas Executar como, confira [Permissões para configurar contas Executar como](automation-security-overview.md#permissions).
- Colaborador na assinatura ou as permissões a seguir.

| Permissão |Escopo|
| --- | --- |
| Microsoft.Authorization/Operations/read | Subscription|
| Microsoft.Authorization/permissions/read |Subscription|
| Microsoft.Authorization/roleAssignments/read | Subscription |
| Microsoft.Authorization/roleAssignments/write | Subscription |
| Microsoft.Authorization/roleAssignments/delete | Subscription || Microsoft.Automation/automationAccounts/connections/read | Grupo de recursos |
| Microsoft.Automation/automationAccounts/certificates/read | Grupo de recursos |
| Microsoft.Automation/automationAccounts/write | Grupo de recursos |
| Microsoft.OperationalInsights/workspaces/write | Grupo de recursos |

## <a name="components"></a>Componentes

O recurso Iniciar/Parar VMs fora do horário comercial inclui runbooks pré-configurados, agendas e integração com logs de Azure Monitor. Você pode usar esses elementos para personalizar a inicialização e o desligamento de suas VMs de acordo com suas necessidades de negócios.

### <a name="runbooks"></a>Runbooks

A tabela a seguir lista os runbooks que o recurso implanta em sua conta de Automação. NÃO faça alterações no código do runbook. Em vez disso, escreva seu próprio runbook para obter novas funcionalidades.

> [!IMPORTANT]
> Não execute diretamente nenhum runbook com **child** acrescentado ao nome.

Todos os runbooks pai incluem o parâmetro `WhatIf`. Quando definido como True, o parâmetro dá suporte ao detalhamento do comportamento exato que o runbook assume quando executado sem o parâmetro e ele valida se as VMs corretas estão sendo direcionadas. Um runbook só executa suas ações definidas quando o parâmetro `WhatIf` é definido como False.

|Runbook | Parâmetros | Descrição|
| --- | --- | ---|
|AutoStop_CreateAlert_Child | VMObject <br> AlertAction <br> WebHookURI | Chamada a partir do runbook pai. Esse runbook cria alertas por recurso para o cenário Auto-Stop.|
|AutoStop_CreateAlert_Parent | VMList<br> WhatIf: Verdadeiro ou Falso  | Cria ou atualiza regras de alerta do Azure em VMs nos grupos de assinatura ou de recursos de destino. <br> `VMList` é uma lista separada por vírgulas de VMs (sem espaços em branco), por exemplo, `vm1,vm2,vm3` .<br> `WhatIf` habilita a validação da lógica do runbook sem execução.|
|AutoStop_Disable | Nenhum | Desabilita os alertas de Auto-Stop e o agendamento padrão.|
|AutoStop_VM_Child | WebHookData | Chamada a partir do runbook pai. As regras de alerta chamam esse runbook para parar uma VM clássica.|
|AutoStop_VM_Child_ARM | WebHookData |Chamada a partir do runbook pai. As regras de alerta chamam esse runbook para interromper uma VM.  |
|ScheduledStartStop_Base_Classic | CloudServiceName<br> Ação: Iniciar ou parar<br> VMList  | Executa a ação iniciar ou parar no grupo de VMs clássicas por Serviços de Nuvem. |
|ScheduledStartStop_Child | VMName <br> Ação: Iniciar ou parar <br> ResourceGroupName | Chamada a partir do runbook pai. Executa uma ação de iniciar ou parar para a parada agendada.|
|ScheduledStartStop_Child_Classic | VMName<br> Ação: Iniciar ou parar<br> ResourceGroupName | Chamada a partir do runbook pai. Executa uma ação de iniciar ou parar na parada agendada para VMs clássicas. |
|ScheduledStartStop_Parent | Ação: Iniciar ou parar <br>VMList <br> WhatIf: Verdadeiro ou Falso | Inicia ou para todas as VMs na assinatura. Edite as variáveis `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` para serem executadas somente nesses grupos de recursos de destino. Você também pode excluir VMs específicas atualizando a variável `External_ExcludeVMNames`.|
|SequencedStartStop_Parent | Ação: Iniciar ou parar <br> WhatIf: Verdadeiro ou Falso<br>VMList| Cria tags com o nome **sequencestart** e **sequencestop** em cada VM para as quais você deseja sequenciar a atividade de iniciar/parar. Os nomes das tags diferenciam maiúsculas de minúsculas. O valor da marca deve ser uma lista de inteiros positivos, por exemplo,, `1,2,3` que corresponde à ordem na qual você deseja iniciar ou parar. <br>**Observação**: As VMs devem estar dentro dos grupos de recursos definidos nas variáveis `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames`. Elas devem ter as marcas apropriadas para que as ações entrem em vigor.|

### <a name="variables"></a>Variáveis

A tabela a seguir lista as variáveis criadas na sua conta da Automação. Modifique apenas as variáveis com prefixo `External`. Modificar variáveis prefixadas com `Internal` causará efeitos indesejáveis.

> [!NOTE]
> As limitações no nome da VM e no grupo de recursos são principalmente um resultado do tamanho da variável. Confira [Ativos variáveis na Automação do Azure](./shared-resources/variables.md).

|Variável | Descrição|
|---------|------------|
|External_AutoStop_Condition | O operador condicional exigido para configurar a condição antes de disparar um alerta. Os valores aceitáveis são `GreaterThan`, `GreaterThanOrEqual`, `LessThan` e `LessThanOrEqual`.|
|External_AutoStop_Description | O alerta para parar a VM se o percentual da CPU exceder o limite.|
|External_AutoStop_Frequency | A frequência de avaliação para a regra. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de 5 minutos até 6 horas. |
|External_AutoStop_MetricName | O nome da métrica de desempenho para a qual a regra de alerta do Azure deverá ser configurada.|
|External_AutoStop_Severity | Severidade do alerta de métrica, que pode variar de 0 a 4. |
|External_AutoStop_Threshold | O limite para a regra de alerta do Azure especificada na variável `External_AutoStop_MetricName`. Os valores percentuais podem variar de 1 a 100.|
|External_AutoStop_TimeAggregationOperator | O operador de agregação de tempo aplicado ao tamanho de janela selecionado para avaliar a condição. Os valores aceitáveis são `Average`, `Minimum`, `Maximum`, `Total` e `Last`.|
|External_AutoStop_TimeWindow | O tamanho da janela durante o qual o Azure analisa a métrica selecionada para disparar um alerta. Esse parâmetro aceita a entrada no formato Intervalo de tempo. Os valores possíveis são de 5 minutos até 6 horas.|
|External_EnableClassicVMs| Valor que especifica se as VMs clássicas são destinadas ao recurso. O valor padrão é True. Defina essa variável como False para assinaturas do CSP (Provedor de Soluções de Nuvem) do Azure. As VMs clássicas exigem uma [conta Executar como clássica](automation-create-standalone-account.md#create-a-classic-run-as-account).|
|External_ExcludeVMNames | Lista separada por vírgulas de nomes de VMs a serem excluídas, limitada a 140 VMs. Se você adicionar mais de 140 VMs à lista, as VMs especificadas para exclusão poderão ser iniciadas ou interrompidas inadvertidamente.|
|External_Start_ResourceGroupNames | Lista separada por vírgulas de um ou mais grupos de recursos que são direcionados para ações de inicialização.|
|External_Stop_ResourceGroupNames | Lista separada por vírgulas de um ou mais grupos de recursos que são direcionados para ações de parada.|
|External_WaitTimeForVMRetrySeconds |O tempo de espera em segundos para que as ações sejam executadas nas VMs para o runbook **SequencedStartStop_Parent**. Essa variável permite que o runbook aguarde as operações filhas por um número especificado de segundos antes de prosseguir com a próxima ação. O tempo de espera máximo é de 10800 ou três horas. O valor padrão é 2100 segundos.|
|Internal_AutomationAccountName | Especifica o nome da conta de Automação.|
|Internal_AutoSnooze_ARM_WebhookURI | Especifica o URI do webhook chamado para o cenário AutoStop das VMs.|
|Internal_AutoSnooze_WebhookUri | O URI do webhook chamado para o cenário de AutoStop das VMs clássicas.|
|Internal_AzureSubscriptionId | A ID de assinatura do Azure.|
|Internal_ResourceGroupName | O nome do grupo de recursos da conta de Automação.|

>[!NOTE]
>Para a variável `External_WaitTimeForVMRetryInSeconds`, o valor padrão foi atualizado de 600 para 2100.

Em todos os cenários, as variáveis `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames` são necessárias para o direcionamento às VMs, exceto as listas de VMs separadas por vírgulas dos runbooks **AutoStop_CreateAlert_Parent**, **SequencedStartStop_Parent** e **ScheduledStartStop_Parent**. Em outras palavras, suas VMs devem pertencer a grupos de recursos de destino para que as ações iniciar e parar ocorram. A lógica funciona semelhante ao Azure Policy, ou seja, você pode direcionar para a assinatura ou o grupo de recursos e fazer com que as ações sejam herdadas por VMs recém-criadas. Com essa abordagem, não é necessário ter um agendamento distinto para cada VM nem gerenciar as ações iniciar e parar em escala.

### <a name="schedules"></a>Agendas

A tabela a seguir lista cada uma das agendas padrão criadas em sua conta de Automação.  É possível modificá-las ou criar suas próprias agendas personalizadas.  Por padrão, todos os agendamentos são desabilitados, exceto **Scheduled_StartVM** e **Scheduled_StopVM**.

Você não deve habilitar todos os agendamentos, pois isso poderá criar ações de agendamento sobrepostas. É melhor determinar quais otimizações você deseja executar e modificá-las da forma apropriada. Consulte os exemplos de cenários na seção Visão geral para obter explicações adicionais.

|Nome da agenda | Frequência | Descrição|
|--- | --- | ---|
|Schedule_AutoStop_CreateAlert_Parent | A cada 8 horas | Executa o runbook **AutoStop_CreateAlert_Parent** a cada oito horas, o que, por sua vez, interrompe os valores baseados em VM nas variáveis `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames`. Como alternativa, você pode especificar uma lista de VMs separadas por vírgula utilizando o parâmetro `VMList`.|
|Scheduled_StopVM | Definido pelo usuário, diariamente | Executa o runbook **ScheduledStopStart_Parent** com um parâmetro de `Stop` todos os dias no horário especificado.  Interrompe automaticamente todas as VMs que atendem às regras definidas por ativos variáveis.  Habilite o agendamento relacionado, **Scheduled-StartVM**.|
|Scheduled_StartVM | Definido pelo usuário, diariamente | Executa o runbook **ScheduledStopStart_Parent** com um valor de parâmetro `Start` todos os dias no horário especificado. Inicia automaticamente todas as VMs que atendem às regras definidas por ativos variáveis.  Habilite o agendamento relacionado, **Sequenced-StopVM**.|
|Sequenced-StopVM | 1h (UTC), toda sexta-feira | Executa o runbook **Sequenced_StopStop_Parent** com um valor de parâmetro `Stop` toda sexta-feira no horário especificado.  Para sequencialmente (em ordem crescente) todas as VMs com uma marca de **SequenceStop** definida pelas variáveis adequadas. Para obter mais informações sobre valores de tags e variáveis de ativo, confira [Runbooks](#runbooks).  Habilite o agendamento relacionado, **Sequenced-StartVM**.|
|Sequenced-StartVM | 13h (UTC), toda segunda-feira | Executa o runbook **SequencedStopStart_Parent** com um valor de parâmetro `Start` toda segunda-feira no horário especificado. Inicia sequencialmente (em ordem decrescente) todas as VMs com uma marca de **SequenceStart** definida pelas variáveis adequadas. Para obter mais informações sobre valores de tags e ativos variáveis, confira [Runbooks](#runbooks). Habilite o agendamento relacionado, **Sequenced-StopVM**.

## <a name="use-the-feature-with-classic-vms"></a>Usar o recurso com VMs clássicas

Se você estiver usando o recurso de Iniciar/Parar VMs fora do horário comercial para VMs clássicas, a Automação processará todas as VMs sequencialmente por serviço de nuvem. VMs ainda são processadas em paralelo entre diferentes serviços de nuvem. 

Para o uso do recurso com VMs clássicas, você precisa de uma conta Executar como clássica, que não é criada por padrão. Para obter instruções sobre como criar uma conta Executar como clássica, confira [Criar uma conta Executar como clássica](automation-create-standalone-account.md#create-a-classic-run-as-account).

Se você tiver mais de 20 VMs por serviço de nuvem, confira algumas recomendações:

* Crie vários agendamentos com o runbook pai **ScheduledStartStop_Parent** e especifique 20 VMs por agendamento.
* Nas propriedades da agenda, use o `VMList` parâmetro para especificar nomes de VM como uma lista separada por vírgulas (sem espaços em branco).

Caso contrário, se o trabalho de Automação para esse recurso for executado por mais de três horas, ele será temporariamente descarregado ou interrompido de acordo com o limite de [compartilhamento justo](automation-runbook-execution.md#fair-share).

As assinaturas do CSP do Azure dão suporte apenas ao modelo do Azure Resource Manager. Serviços que não pertencem ao Azure Resource Manager não estão disponíveis no programa. Quando o recurso de Iniciar/Parar VMs fora do horário comercial é executado, você pode receber erros, já que ele tem cmdlets para gerenciar recursos clássicos. Para saber mais sobre CSP, confira [Serviços disponíveis em assinaturas do CSP](/azure/cloud-solution-provider/overview/azure-csp-available-services). Se você usar uma assinatura do CSP, defina a variável [External_EnableClassicVMs](#variables) como False após a implantação.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="view-the-feature"></a>Exibir o recurso

Use um dos seguintes mecanismos para acessar o recurso habilitado:

* Na sua conta de Automação, selecione **Iniciar/Parar VM** em **Recursos Relacionados**. Na página Iniciar/Parar VM, selecione **Gerenciar a solução** em **Gerenciar Soluções para Iniciar/Parar VMs**.

* Navegue até o workspace do Log Analytics vinculado à sua conta de Automação. Depois de selecionar o workspace, escolha **Soluções** no painel esquerdo. Na página Soluções, selecione **Start-Stop-VM[workspace]** na lista.  

A seleção do recurso exibe a página **Start-Stop-VM [Workspace]** . Nela, você pode examinar detalhes importantes, como as informações no bloco **StartStopVM**. Assim como no seu workspace do Log Analytics, esse bloco exibe uma contagem e uma representação gráfica dos trabalhos de runbook do recurso que foram iniciados e encerrados com êxito.

![Página Gerenciamento de Atualizações de Automação](media/automation-solution-vm-management/azure-portal-vmupdate-solution-01.png)

Você pode executar outras análises dos registros de trabalho clicando no bloco de rosca. O painel mostra o histórico de trabalhos e as consultas de pesquisa de logs pré-definidas. Acesse o portal avançado de análise de logs para pesquisar com base em suas consultas de pesquisa.

## <a name="update-the-feature"></a>Atualizar o recurso

Se você implantou uma versão anterior do recurso Iniciar/Parar VMs fora do horário comercial, exclua-a da sua conta antes de implantar uma versão atualizada. Siga as etapas para [remover o recurso](automation-solution-vm-management-remove.md#delete-the-feature), depois siga as etapas para [habilitá-lo](automation-solution-vm-management-enable.md).

## <a name="next-steps"></a>Próximas etapas

Para habilitar o recurso em VMs em seu ambiente, confira [Habilitar recurso Iniciar/Parar VMs fora do horário comercial](automation-solution-vm-management-enable.md).
