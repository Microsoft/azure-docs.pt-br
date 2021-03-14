---
title: Configurar Iniciar/Parar VMs fora do horário comercial da Automação do Azure
description: Este artigo informa como configurar o recurso Iniciar/Parar VMs fora do horário comercial para dar suporte a cenários ou casos de uso diferentes.
services: automation
ms.subservice: process-automation
ms.date: 06/01/2020
ms.topic: conceptual
ms.openlocfilehash: b52b51133f059f028baf470515e886d17077af6a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593934"
---
# <a name="configure-startstop-vms-during-off-hours"></a>Configurar Iniciar/Parar VMs fora do horário comercial

Este artigo descreve como configurar o recurso [Iniciar/Parar VMs fora do horário comercial](automation-solution-vm-management.md) para dar suporte aos cenários descritos. Você também pode aprender como:

* [Configurar notificações por email](#configure-email-notifications)
* [Adicionar uma VM](#add-a-vm)
* [Excluir uma VM](#exclude-a-vm)
* [Modificar as agendas de inicialização e desligamento](#modify-the-startup-and-shutdown-schedules)

## <a name="scenario-1-startstop-vms-on-a-schedule"></a><a name="schedule"></a>Cenário 1: Iniciar/parar VMs em um agendamento

Este cenário é a configuração padrão ao implantar a solução Iniciar/Parar VMs fora do horário comercial pela primeira vez. Por exemplo, você pode configurar o recurso para parar todas as VMs em uma assinatura ao sair do trabalho à noite e iniciá-las pela manhã ao retornar ao escritório. Quando configurados durante a implantação, os agendamentos **Scheduled-StartVM** e **Scheduled-StopVM** iniciam e param VMs de destino. 

A configuração do recurso para apenas parar as VMs tem suporte. Consulte [Modificar as agendas de inicialização e desligamento](#modify-the-startup-and-shutdown-schedules) para saber como configurar um agendamento personalizado.

> [!NOTE]
> O fuso horário usado pelo recurso é o seu fuso horário atual de quando o parâmetro de hora do agendamento foi configurado. No entanto, a Automação do Azure armazena-o no formato UTC na Automação do Azure. Não é necessário executar nenhuma conversão de fuso horário, pois isso acontece durante a implantação no computador.

Para controlar as VMs que estão no escopo, configure as variáveis: `External_Start_ResourceGroupNames`, `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames`.

Você pode habilitar o direcionamento da ação a uma assinatura e um grupo de recursos ou direcionar a uma lista específica de VMs, mas não ambos.

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcionar a ação de início e parada para uma assinatura e um grupo de recursos

1. Configure as variáveis `External_Stop_ResourceGroupNames` e `External_ExcludeVMNames` para especificar as VMs de destino.

2. Habilite e atualize os agendamentos **Scheduled-StartVM** e **Scheduled-StopVM**.

3. Execute o runbook **ScheduledStartStop_Parent** com o campo de parâmetro **ACTION** definido como **iniciar** e o campo de parâmetro **WHATIF** definido como true para visualizar as alterações.

### <a name="target-the-start-and-stop-action-by-vm-list"></a>Direcionar a ação de início e parada por lista de VMs

1. Execute o runbook **ScheduledStartStop_Parent** com a **ação** definida como **Iniciar**.

2. Adicione uma lista separada por vírgulas de VMs (sem espaços) no campo parâmetro **VMList** . Uma lista de exemplos é `vm1,vm2,vm3` .

3. Defina o campo de parâmetro **WHATIF** como true para visualizar as alterações.

4. Configure a `External_ExcludeVMNames` variável com uma lista separada por vírgulas de VMs (VM1, VM2, VM3), sem espaços entre valores separados por vírgulas.

5. Esse cenário não respeita as variáveis `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupnames`. Para este cenário, é necessário criar seu próprio agendamento da Automação. Para obter detalhes, consulte [Agende um runbook na Automação do Azure](shared-resources/schedules.md).

    > [!NOTE]
    > O valor para **nomes do ResourceGroup de destino** é armazenado como os valores para `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames`. Para obter uma maior granularidade, você pode modificar cada uma das variáveis para direcionar diferentes grupos de recursos. Para iniciar a ação, use `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` para interromper a ação. As VMs são adicionadas automaticamente aos agendamentos de início e parada.

## <a name="scenario-2-startstop-vms-in-sequence-by-using-tags"></a><a name="tags"></a>Cenário 2: Iniciar/parar VMs na sequência usando marcas

Em um ambiente que inclui dois ou mais componentes em várias VMs compatíveis com cargas de trabalho distribuídas, é importante dar suporte ao sequenciamento de quais componentes são iniciados/parados em ordem. 

### <a name="target-the-start-and-stop-actions-against-a-subscription-and-resource-group"></a>Direcionar a ação de início e parada para uma assinatura e um grupo de recursos

1. Adicione um `sequencestart` e uma marca de `sequencestop` com valores inteiros positivos às VMs que são destinadas às variáveis `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames`. As ações iniciar e parar são executadas em ordem crescente. Para saber como marcar uma VM, consulte [Marcar uma máquina virtual do Windows no Azure](../virtual-machines/tag-portal.md) e [Marcar uma máquina virtual do Linux no Azure](../virtual-machines/tag-cli.md).

2. Modifique os agendamentos **Sequenced-StartVM** e **Sequenced-StopVM** de acordo com a data e hora que atendem às suas exigências e habilite o agendamento.

3. Execute o runbook **SequencedStartStop_Parent** com **ACTION** definido como **iniciar** e **WHATIF** definido como True para visualizar as alterações.

4. Visualize a ação e faça as alterações necessárias antes de implementar em VMs de produção. Quando estiver pronto, execute o runbook manualmente com o parâmetro definido como **False** ou permita que os agendamentos **Sequenced-StartVM** e **Sequenced-StopVM** da Automação sejam executados automaticamente de acordo com o agendamento prescrito.

### <a name="target-the-start-and-stop-actions-by-vm-list"></a>Direcionar as ações de início e parada por lista de VMs

1. Adicione um `sequencestart` e uma marca `sequencestop` com valores inteiros positivos às VMs que você planeja adicionar ao parâmetro `VMList`.

2. Execute o runbook **SequencedStartStop_Parent** com a **ação** definida como **Iniciar**.

3. Adicione uma lista separada por vírgulas de VMs (sem espaços) no campo parâmetro **VMList** . Uma lista de exemplos é `vm1,vm2,vm3` .

4. Defina **WHATIF** como true para visualizar as alterações. 

5. Configure a `External_ExcludeVMNames` variável com uma lista separada por vírgulas de VMs, sem espaços entre valores separados por vírgulas.

6. Esse cenário não respeita as variáveis `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupnames`. Para este cenário, é necessário criar seu próprio agendamento da Automação. Para obter detalhes, consulte [Agende um runbook na Automação do Azure](shared-resources/schedules.md).

7. Visualize a ação e faça as alterações necessárias antes de implementar em VMs de produção. Quando estiver pronto, execute manualmente o **Monitoring-and-Diagnostics/Monitoring-Action-groupsrunbook** com o parâmetro definido como **False**. Como alternativa, permita que os agendamentos da Automação **Sequenced-StartVM** e **Sequenced-StopVM** executem automaticamente seguindo o agendamento prescrito.

## <a name="scenario-3-start-or-stop-automatically-based-on-cpu-utilization"></a><a name="cpuutil"></a>Cenário 3: Iniciar ou parar automaticamente com base na utilização da CPU

Iniciar/Parar VMs fora do horário comercial pode ajudar a gerenciar o custo de executar o Azure Resource Manager e as VMs clássicas em sua assinatura avaliando computadores que não são usados fora dos períodos de pico, como depois do horário comercial, e as desliga automaticamente se a utilização do processador for menor que uma porcentagem especificada.

Por padrão, o recurso é pré-configurado para avaliar a métrica percentual da CPU para ver se a utilização média é de 5% ou menos. Esse cenário é controlado pelas seguintes variáveis e pode ser modificado caso os valores padrão não atendam às suas necessidades:

* `External_AutoStop_MetricName`
* `External_AutoStop_Threshold`
* `External_AutoStop_TimeAggregationOperator`
* `External_AutoStop_TimeWindow`
* `External_AutoStop_Frequency`
* `External_AutoStop_Severity`

Você pode habilitar e direcionar a ação a uma assinatura e um grupo de recursos ou direcionar a uma lista específica de VMs.

Quando você executa o runbook **AutoStop_CreateAlert_Parent**, ele verifica se a assinatura de destino, os grupos de recursos e as VMs existem. Se as VMs existirem, o runbook **AutoStop_CreateAlert_Child** será chamado para cada VM verificada pelo runbook pai. Este runbook filho:

* Cria uma regra de alerta de métrica para cada VM verificada.
* Dispara o runbook **AutoStop_VM_Child** para uma VM específica se a CPU cair abaixo do limite configurado para o intervalo de tempo especificado. 
* Tenta parar a VM.

### <a name="target-the-autostop-action-against-all-vms-in-a-subscription"></a>Direcionar a ação de autostop em todas as VMs em uma assinatura

1. Verifique se a variável `External_Stop_ResourceGroupNames` está vazia ou definida como * (curinga).

2. [Opcional] Se você quiser excluir algumas VMs da ação autostop, poderá adicionar uma lista separada por vírgulas de nomes de VM à variável `External_ExcludeVMNames`.

3. Habilite o agendamento do **Schedule_AutoStop_CreateAlert_Parent** para executar e criar as regras de alerta de interrupção de métrica de VM necessárias para todas as VMs em sua assinatura. A execução desse tipo de agendamento permite criar novas regras de alerta de métrica à medida que novas VMs são adicionadas à assinatura.

### <a name="target-the-autostop-action-against-all-vms-in-a-resource-group-or-multiple-resource-groups"></a>Direcionar a ação de autostop para todas as VMs em um grupo de recursos ou vários grupos de recursos

1. Adicione uma lista separada por vírgulas de nomes de grupos de recursos à variável `External_Stop_ResourceGroupNames`.

2. Se você quiser excluir algumas das VMs do autostop, poderá adicionar uma lista separada por vírgulas de nomes de VMs à variável `External_ExcludeVMNames`.

3. Habilite o agendamento do **Schedule_AutoStop_CreateAlert_Parent** para executar e criar as regras de alerta de interrupção de métrica de VMs necessárias para todas as VMs em seus grupos de recursos. A execução dessa operação em um agendamento permite que você crie novas regras de alerta de métrica à medida que novas VMs são adicionadas aos grupos de recursos.

### <a name="target-the-autostop-action-to-a-list-of-vms"></a>Direcionar a ação de autostop para uma lista de VMs

1. Crie um novo [agendamento](shared-resources/schedules.md#create-a-schedule) e vincule-o ao runbook **AutoStop_CreateAlert_Parent**, adicionando uma lista separada por vírgulas de nomes de VM ao parâmetro `VMList`.

2. Opcionalmente, se você quiser excluir algumas VMs da ação autostop, poderá adicionar uma lista separada por vírgulas de nomes de VM (sem espaços) à `External_ExcludeVMNames` variável.

## <a name="configure-email-notifications"></a>Configurar notificações por email

Para alterar as notificações por email depois que a ação Iniciar/Parar VMs fora do horário comercial for implantada, você poderá modificar o grupo de ações criado durante a implantação.  

> [!NOTE]
> As assinaturas na nuvem do Azure Governamental não oferecem suporte para a funcionalidade de email deste recurso.

1. No portal do Azure, navegue até **Monitor** e **Grupos de ação**. Selecione o grupo de ações chamado **StartStop_VM_Notication**.

    :::image type="content" source="media/automation-solution-vm-management/azure-monitor.png" alt-text="Captura de tela da página monitorar-grupos de ações.":::

2. Na página StartStop_VM_Notification, clique em **Editar detalhes** em **Detalhes**. Isso abre a página Email/SMS/Push/Voz. Atualize o endereço de email e clique em **OK** para salvar suas alterações.

    :::image type="content" source="media/automation-solution-vm-management/change-email.png" alt-text="Captura de tela da página email/SMS/Push/voz mostrando um exemplo de endereço de email atualizado.":::

    Como alternativa, você pode adicionar mais ações no grupo de ações. Para saber mais sobre grupos de ações, confira [grupos de ações](../azure-monitor/alerts/action-groups.md)

A seguir está um email de exemplo que é enviado quando o recurso desliga as máquinas virtuais.

:::image type="content" source="media/automation-solution-vm-management/email.png" alt-text="Captura de tela de um email de exemplo enviado quando o recurso desliga as máquinas virtuais.":::

## <a name="add-or-exclude-vms"></a><a name="add-exclude-vms"></a>Adicionar ou excluir VMs

O recurso permite que você adicione VMs a serem destinadas ou excluídas. 

### <a name="add-a-vm"></a>Adicionar uma VM

Há duas maneiras de garantir que uma VM seja incluída quando o recurso é executado:

* Cada um dos runbooks [pai](automation-solution-vm-management.md#runbooks) do recurso tem um parâmetro `VMList`. Você pode passar uma lista separada por vírgulas de nomes de VM (sem espaços) para esse parâmetro ao agendar o runbook pai apropriado para sua situação e essas VMs serão incluídas quando o recurso for executado.

* Para selecionar várias VMs, defina `External_Start_ResourceGroupNames` e `External_Stop_ResourceGroupNames` com os nomes do grupo de recursos que contêm as VMs que você deseja iniciar ou parar. Você também pode definir esse valor como `*` para fazer o recurso ser executado em todos os grupos de recursos na assinatura.

### <a name="exclude-a-vm"></a>Excluir uma VM

Para excluir uma VM do recurso Iniciar/Parar VMs fora do horário comercial, você pode adicionar seu nome à variável `External_ExcludeVMNames`. Essa variável é uma lista separada por vírgulas de VMs específicas (sem espaços) a serem excluídas do recurso. Essa lista é limitada a 140 VMs. Se você adicionar mais de 140 VMs a essa lista, as VMs que estão definidas para serem excluídas poderão ser acidentalmente iniciadas ou interrompidas.

## <a name="modify-the-startup-and-shutdown-schedules"></a>Modificar as agendas de inicialização e desligamento

O gerenciamento das agendas de inicialização e desligamento neste recurso segue as mesmas etapas descritas em [Agendamento de um runbook na Automação do Azure](shared-resources/schedules.md). É necessário fazer agendamentos separados para iniciar e parar VMs.

Configurar o recurso para simplesmente parar as VMs em um determinado momento. Neste cenário, você acabou de criar um agendamento para parar e nenhum agendamento para iniciar correspondente. 

1. Verifique se você adicionou os grupos de recursos para que as VMs sejam desligadas na variável `External_Stop_ResourceGroupNames`.

2. Crie seu próprio agendamento para a hora em que você deseja desligar as VMs.

3. Navegue até o runbook **ScheduledStartStop_Parent** e clique em **Agenda**. Isso permite que você selecione a agenda que criou na etapa anterior.

4. Selecione **Parâmetros e configurações de execução** e defina o campo **ACTION** para **Parar**.

5. Selecione **OK** para salvar suas alterações.

## <a name="next-steps"></a>Próximas etapas

* Para monitorar o recurso durante a operação, consulte [Logs de consulta de Iniciar/Parar VMs fora do horário comercial](automation-solution-vm-management-logs.md).
* Para lidar com problemas durante o gerenciamento de VMs, consulte [Solucionar problemas de Iniciar/Parar VMs fora do horário comercial](troubleshoot/start-stop-vm.md).
