---
title: Solucionar problemas de Gerenciamento de Atualizações com a Automação do Azure
description: Este artigo informa como solucionar problemas com o Gerenciamento de Atualizações de automação do Azure.
services: automation
ms.subservice: update-management
ms.date: 01/13/2021
ms.topic: troubleshooting
ms.openlocfilehash: c16b032502401b633532ab0fcf9518aa85a1b8d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579732"
---
# <a name="troubleshoot-update-management-issues"></a>Solucionar problemas do Gerenciamento de Atualizações

Este artigo aborda os problemas que você pode encontrar ao implantar o recurso de Gerenciamento de Atualizações em seus computadores. Existe uma solução de problemas de um agente para o agente do Hybrid Runbook Worker determinar o problema subjacente. Para saber mais sobre a solução de problemas, consulte [Solucionar problemas do Windows Update Agent](update-agent-issues.md) e [solucionar problemas do agente de atualização do Linux](update-agent-issues-linux.md). Para outros problemas de implantação de recursos, consulte [Solucionar problemas de implantação de recursos](onboarding.md).

>[!NOTE]
>Se você tiver problemas ao implantar Gerenciamento de Atualizações em um computador Windows, abra o Visualizador de Eventos do Windows e verifique o log de eventos do **Operations Manager** em **Logs de Aplicativos e Serviços** no computador local. Procure eventos com a ID de evento 4502 e detalhes do evento que contenham `Microsoft.EnterpriseManagement.HealthService.AzureAutomation.HybridAgent`.

## <a name="scenario-linux-updates-shown-as-pending-and-those-installed-vary"></a><a name="updates-linux-installed-different"></a>Cenário: as atualizações do Linux mostradas como pendentes e as instaladas variam

### <a name="issue"></a>Problema

Para seu computador Linux, o Gerenciamento de Atualizações mostra atualizações específicas disponíveis sob a classificação **Segurança** e **Outros**. Porém, quando uma agenda de atualização é executada no computador, por exemplo, para instalar somente as atualizações que correspondem à classificação **Segurança**, as atualizações instaladas são diferentes ou um subconjunto das atualizações mostradas anteriormente correspondentes a essa classificação.

### <a name="cause"></a>Causa

Quando uma avaliação das atualizações do SO pendentes para seu computador Linux é feita, os arquivos OVAL [(Linguagem Aberta de Avaliação e Vulnerabilidades)](https://oval.mitre.org/) fornecidos pelo fornecedor de distribuição Linux são usados pelo Gerenciamento de Atualizações para classificação. A categorização é feita para atualizações do Linux como **Segurança** ou **Outros**, com base nos arquivos OVAL que afirmam atualizações de estados que abordam problemas de segurança ou vulnerabilidades. Mas, quando a agenda de atualização é executada, ela é executada no computador Linux usando o gerenciador de pacotes apropriado, como YUM, APT ou ZYPPER, para instalá-lo. O gerenciador de pacotes para a distribuição do Linux pode ter um mecanismo diferente para classificar atualizações, no qual os resultados podem diferir daqueles obtidos dos arquivos OVAL pelo Gerenciamento de Atualizações.

### <a name="resolution"></a>Resolução

Você pode verificar manualmente o computador Linux, as atualizações aplicáveis e a classificação de acordo com o gerenciador de pacotes de distribuição. Para entender quais atualizações são classificadas como **Segurança** pelo gerenciador de pacotes, execute os comandos a seguir.

Para YUM, o comando a seguir retorna uma lista diferente de zero de atualizações categorizadas como **Segurança** pela Red Hat. Observe que, no caso do CentOS, ele sempre retorna uma lista vazia e nenhuma classificação de segurança ocorre.

```bash
sudo yum -q --security check-update
```

Para ZYPPER, o comando a seguir retorna uma lista diferente de zero de atualizações categorizadas como **Segurança** pelo SUSE.

```bash
sudo LANG=en_US.UTF8 zypper --non-interactive patch --category security --dry-run
```

Para APT, o comando a seguir retorna uma lista diferente de zero de atualizações categorizadas como **Segurança** pelas distribuições Canonical e Ubuntu Linux.

```bash
sudo grep security /etc/apt/sources.list > /tmp/oms-update-security.list LANG=en_US.UTF8 sudo apt-get -s dist-upgrade -oDir::Etc::Sourcelist=/tmp/oms-update-security.list
```

Nessa lista, você executa o comando `grep ^Inst` para obter todas as atualizações de segurança pendentes.

## <a name="scenario-you-receive-the-error-failed-to-enable-the-update-solution"></a><a name="failed-to-enable-error"></a>Cenário: Você recebe o erro "Falha ao habilitar a solução de atualização"

### <a name="issue"></a>Problema

Ao tentar habilitar Gerenciamento de Atualizações em sua conta de automação, você obtém o seguinte erro:

```error
Error details: Failed to enable the Update solution
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer por um dos seguintes motivos:

* Os requisitos de firewall de rede para o agente de Log Analytics podem não estar configurados corretamente. Essa situação pode fazer com que o agente falhe ao resolver as URLs de DNS.

* O direcionamento de Gerenciamento de Atualizações está configurado incorretamente e o computador não está recebendo atualizações conforme o esperado.

* Você também pode observar que o computador mostra um status de `Non-compliant` em **Conformidade**. Ao mesmo tempo, **Análise de desktops do agente** relata o agente como `Disconnected`.

### <a name="resolution"></a>Resolução

* Execute a solução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline), dependendo do sistema operacional.

* Visite [Planejamento de rede](../automation-hybrid-runbook-worker.md#network-planning) para saber mais sobre quais endereços e portas devem ter permissão para que Gerenciamento de Atualizações funcione.  

* Verifique se há problemas de configuração de escopo. A [configuração de escopo](../update-management/scope-configuration.md) determina quais computadores estão configurados para Gerenciamento de Atualizações. Se o seu computador estiver aparecendo no seu workspace, mas não no Gerenciamento de Atualizações, você deverá definir a configuração de escopo para direcionar os computadores. Para saber mais sobre a configuração de escopo, consulte [Habilitar máquinas no espaço de trabalho](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

* Remova a configuração de trabalho seguindo as etapas em [Remover o Hybrid Runbook Worker de um computador local com Windows](../automation-windows-hrw-install.md#remove-windows-hybrid-runbook-worker) ou [Remover o Hybrid Runbook Worker de um computador Linux local](../automation-linux-hrw-install.md#remove-linux-hybrid-runbook-worker).

## <a name="scenario-superseded-update-indicated-as-missing-in-update-management"></a>Cenário: Atualização substituída indicada como ausente no Gerenciamento de Atualizações

### <a name="issue"></a>Problema

Atualizações antigas são exibidas como ausentes em uma conta de automação, embora tenham sido substituídas. Uma atualização substituída é aquela que você não precisa instalar porque uma atualização posterior que corrige a mesma vulnerabilidade está disponível. O Gerenciamento de Atualizações ignora a atualização substituída e a torna não aplicável em favor da atualização substituta. Para obter informações sobre um problema relacionado, consulte [A atualização é substituída](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer).

### <a name="cause"></a>Causa

As atualizações substituídas não são recusadas no WSUS (Windows Server Update Services) para que elas possam ser consideradas não aplicáveis.

### <a name="resolution"></a>Resolução

Quando uma atualização substituída se tornar totalmente não aplicável, você deverá alterar o estado de aprovação dessa atualização para `Declined` no WSUS. Para alterar o estado de aprovação de todas as suas atualizações:

1. Na sua conta de Automação, selecione **Gerenciamento de Atualizações** para exibir o status dos computadores. Consulte [Exibir as avaliações de atualização](../update-management/view-update-assessments.md).

2. Verifique a atualização substituída para certificar-se de que é 100% não aplicável.

3. [Recuse a atualização](/windows-server/administration/windows-server-update-services/manage/updates-operations#declining-updates) no servidor WSUS ao qual os computadores reportam.

4. Selecione **Computadores** e, na coluna **Conformidade**, force uma nova verificação de conformidade. Confira [Gerenciar atualizações para VMs](../update-management/manage-updates-for-vm.md).

5. Repita as etapas acima para outras atualizações substituídas.

6. Para os WSUS (Windows Server Update Services), limpe manualmente todas as atualizações substituídas para atualizar a infraestrutura usando o [Assistente de limpeza do servidor](/windows-server/administration/windows-server-update-services/manage/the-server-cleanup-wizard) do WSUS.

7. Repita esse procedimento regularmente para corrigir o problema de exibição e minimizar a quantidade de espaço em disco usada para o gerenciamento de atualizações.

## <a name="scenario-machines-dont-show-up-in-the-portal-under-update-management"></a><a name="nologs"></a>Cenário: Computadores não são exibidos sob o Gerenciamento de Atualizações

### <a name="issue"></a>Problema

Seus computadores têm os seguintes sintomas:

* Seu computador mostra `Not configured` da exibição Gerenciamento de Atualizações de uma VM.

* Seus computadores estão ausentes na exibição Gerenciamento de Atualizações da sua conta de Automação do Azure.

* Você tem computadores que mostram como `Not assessed` em **Conformidade**. No entanto, você vê dados de pulsação em logs de Azure Monitor para o Hybrid Runbook Worker, mas não para Gerenciamento de Atualizações.

### <a name="cause"></a>Causa

Esse problema pode ser causado por problemas de configuração local ou pela configuração de escopo configurada incorretamente. Possíveis causas específicas:

* Talvez seja necessário registrar novamente e reinstalar o Hybrid Runbook Worker.

* Você pode ter definido uma cota em seu espaço de trabalho que foi atingido e que está impedindo o armazenamento de dados adicional.

### <a name="resolution"></a>Resolução

1. Execute a solução de problemas para [Windows](update-agent-issues.md#troubleshoot-offline) ou [Linux](update-agent-issues-linux.md#troubleshoot-offline), dependendo do sistema operacional.

2. Verificar se seu computador envia relatórios para o espaço de trabalho correto. Para obter diretrizes sobre como verificar esse aspecto, confira [Verificar a conectividade do agente para o Azure Monitor](../../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor). Verifique também se esse espaço de trabalho está vinculado à sua conta de automação do Azure. Para confirmar, acesse sua conta de automação e selecione **Espaço de trabalho vinculado** em **Recursos relacionados**.

3. Verifique se as máquinas aparecem no espaço de trabalho Log Analytics vinculado à sua conta de automação. Execute a consulta a seguir do espaço de trabalho do Log Analytics.

   ```kusto
   Heartbeat
   | summarize by Computer, Solutions
   ```

    Se você não vir seu computador nos resultados da consulta, ele não fez check-in recentemente. Provavelmente, há um problema de configuração local e você deve [reinstalar o agente](../../azure-monitor/vm/quick-collect-windows-computer.md#install-the-agent-for-windows).

    Se o computador estiver listado nos resultados da consulta, verifique na propriedade **Soluções** se as **atualizações** estão listadas. Isso verifica se ele está registrado com Gerenciamento de Atualizações. Se não estiver, verifique se há problemas de configuração de escopo. A [configuração de escopo](../update-management/scope-configuration.md) determina quais computadores estão configurados para Gerenciamento de Atualizações. Para definir a configuração de escopo para o computador de destino, confira [Habilitar computadores no workspace](../update-management/enable-from-automation-account.md#enable-machines-in-the-workspace).

4. Em seu espaço de trabalho, execute esta consulta.

   ```kusto
   Operation
   | where OperationCategory == 'Data Collection Status'
   | sort by TimeGenerated desc
   ```

   Se você obtiver um resultado `Data collection stopped due to daily limit of free data reached. Ingestion status = OverQuota`, a cota definida em seu espaço de trabalho foi atingida, o que impediu que os dados fossem salvos. Em seu espaço de trabalho, vá para **gerenciamento de volume de dados** em **Uso e custos estimados** e altere ou remova a cota.

5. Se o problema ainda não tiver sido resolvido, siga as etapas descritas em [Implantar um Hybrid Runbook Worker do Windows](../automation-windows-hrw-install.md) para reinstalar o Hybrid Worker para o Windows. Para o Linux, siga as etapas em [Implantar um Hybrid Runbook Worker do Linux.](../automation-linux-hrw-install.md)

## <a name="scenario-unable-to-register-automation-resource-provider-for-subscriptions"></a><a name="rp-register"></a>Cenário: Não é possível registrar o provedor de recursos de automação para assinaturas

### <a name="issue"></a>Problema

Quando você trabalha com implantações de recursos em sua conta de automação, ocorre o seguinte erro:

```error
Error details: Unable to register Automation Resource Provider for subscriptions
```

### <a name="cause"></a>Causa

O provedor de recursos de automação não está registrado na assinatura.

### <a name="resolution"></a>Resolução

Para registrar o provedor de recursos de Automação, execute as seguintes etapas no portal do Azure.

1. Selecione **Todos os serviços** na lista de serviços inferior do Azure e selecione **Assinaturas** no grupo de serviços Geral.

2. Selecione sua assinatura.

3. Em **Configurações**, selecione **Provedores de recursos**.

4. Na lista de provedores de recursos, verifique se o provedor de recursos Microsoft. Automation está registrado.

5. Se não estiver listado, registre o provedor Microsoft. Automation seguindo as etapas em [Resolver erros de registro do provedor de recursos](../../azure-resource-manager/templates/error-register-resource-provider.md).

## <a name="scenario-scheduled-update-with-a-dynamic-schedule-missed-some-machines"></a><a name="scheduled-update-missed-machines"></a>Cenário: A atualização agendada com um agendamento dinâmico perdeu alguns computadores

### <a name="issue"></a>Problema

As máquinas incluídas em uma visualização de atualização nem todas aparecem na lista de máquinas corrigidas durante uma execução agendada.

### <a name="cause"></a>Causa

O problema pode ter uma das seguintes causas:

* As assinaturas definidas no escopo em uma consulta dinâmica não estão configuradas para o provedor de recursos de automação registrado.

* Os computadores não estavam disponíveis ou não tinham marcas apropriadas quando a agenda foi executada.

### <a name="resolution"></a>Resolução

#### <a name="subscriptions-not-configured-for-registered-automation-resource-provider"></a>Assinaturas não configuradas para o provedor de recursos de automação registrado

Se sua assinatura não estiver configurada para o provedor de recursos de automação, você não poderá consultar nem buscar informações sobre computadores nessa assinatura. Use as etapas a seguir para verificar o registro da assinatura.

1. No [portal do Azure](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal), acesse a lista de serviços do Azure.

2. No grupo de serviços gerais, selecione **Todos os serviços** e, em seguida, selecione **Assinaturas**.

3. Localize a assinatura definida no escopo da sua implantação.

4. Em **Configurações**, selecione **Provedores de recursos**.

5. Verifique se o provedor de recursos Microsoft. Automation está registrado.

6. Se não estiver listado, registre o provedor Microsoft. Automation seguindo as etapas em [Resolver erros de registro do provedor de recursos](../../azure-resource-manager/templates/error-register-resource-provider.md).

#### <a name="machines-not-available-or-not-tagged-correctly-when-schedule-executed"></a>Computadores não disponíveis ou não marcados corretamente quando a agenda foi executada

Use o procedimento a seguir se sua assinatura estiver configurada para o provedor de recursos de automação, mas a execução da agenda de atualização com os [grupos dinâmicos](../update-management/configure-groups.md) especificados ignorou alguns computadores.

1. No portal do Azure, abra sua conta de automação e selecione **Gerenciamento de atualizações**.

2. Verifique [histórico de Gerenciamento de Atualizações](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) para determinar a hora exata em que a implantação de atualização foi executada.

3. Para computadores que você suspeita que tenham sido ignorados no Gerenciamento de Atualizações, use o Azure Resource Graph (ARG) para [localizar alterações no computador](../../governance/resource-graph/how-to/get-resource-changes.md#find-detected-change-events-and-view-change-details).

4. Pesquise alterações em um período considerável, como um dia, antes da execução da implantação de atualização.

5. Verifique os resultados da pesquisa relacionados a quaisquer alterações do sistema, como exclusão ou atualizações, nos computadores neste período. Essas alterações podem alterar o status ou as marcas da máquina de forma que os computadores não sejam selecionados na lista de computadores quando as atualizações forem implantadas.

6. Ajuste as configurações de máquinas e recursos conforme necessário para corrigir problemas de status de máquina ou marca.

7. Execute novamente o agendamento de atualização para garantir que a implantação com os grupos dinâmicos especificados inclua todos os computadores.

## <a name="scenario-expected-machines-dont-appear-in-preview-for-dynamic-group"></a><a name="machines-not-in-preview"></a>Cenário: Os computadores esperados não aparecem na visualização para o grupo dinâmico

### <a name="issue"></a>Problema

As VMs para escopos selecionados de um grupo dinâmico não estão aparecendo na lista de visualização do portal do Azure. Essa lista consiste em todos os computadores recuperados por uma consulta de ARG para os escopos selecionados. Os escopos são filtrados para computadores que têm Hybrid Runbook Workers instalados e para os quais você tem permissões de acesso.

### <a name="cause"></a>Causa

As possíveis causas para esse problema incluem:

* Você não tem o acesso correto nos escopos selecionados.
* A consulta de ARG não recupera os computadores esperados.
* O Hybrid Runbook Worker não está instalado nos computadores.

### <a name="resolution"></a>Resolução 

#### <a name="incorrect-access-on-selected-scopes"></a>Acesso incorreto nos escopos selecionados

O portal do Azure só exibe computadores para os quais você tem acesso de gravação em um determinado escopo. Se você não tiver o acesso correto para um escopo, confira [Tutorial: Conceder acesso aos recursos do Azure para um usuário usando o portal do Azure](../../role-based-access-control/quickstart-assign-role-user-portal.md).

#### <a name="arg-query-doesnt-return-expected-machines"></a>A consulta de ARG não recupera os computadores esperados

Siga as etapas abaixo para descobrir se suas consultas estão funcionando corretamente.

1. Execute uma consulta ARG formatada conforme mostrado abaixo na folha Gerenciador de grafo de recursos em portal do Azure. Essa consulta imita os filtros que você selecionou quando criou o grupo dinâmico no Gerenciamento de Atualizações. Consulte [Usar grupos dinâmicos com o Gerenciamento de Atualizações](../update-management/configure-groups.md).

    ```kusto
    where (subscriptionId in~ ("<subscriptionId1>", "<subscriptionId2>") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "<Windows/Linux>" and resourceGroup in~ ("<resourceGroupName1>","<resourceGroupName2>") and location in~ ("<location1>","<location2>") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" and tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "All" option selected for tags
    | where  (tags[tolower("<tagKey1>")] =~ "<tagValue1>" or tags[tolower("<tagKey2>")] =~ "<tagValue2>") // use this if "Any" option selected for tags
    | project id, location, name, tags
    ```

   Veja um exemplo:

    ```kusto
    where (subscriptionId in~ ("20780d0a-b422-4213-979b-6c919c91ace1", "af52d412-a347-4bc6-8cb7-4780fbb00490") and type =~ "microsoft.compute/virtualmachines" and properties.storageProfile.osDisk.osType == "Windows" and resourceGroup in~ ("testRG","withinvnet-2020-01-06-10-global-resources-southindia") and location in~ ("australiacentral","australiacentral2","brazilsouth") )
    | project id, location, name, tags = todynamic(tolower(tostring(tags)))
    | where  (tags[tolower("ms-resource-usage")] =~ "azure-cloud-shell" and tags[tolower("temp")] =~ "temp")
    | project id, location, name, tags
    ```

2. Verifique se os computadores que você está procurando estão listados nos resultados da consulta.

3. Se os computadores não estiverem listados, provavelmente há um problema com o filtro selecionado no grupo dinâmico. Ajuste a configuração do grupo conforme necessário.

#### <a name="hybrid-runbook-worker-not-installed-on-machines"></a>O Hybrid Runbook Worker não foi instalado nos computadores

As máquinas aparecem nos resultados da consulta de ARG, mas ainda não aparecem na visualização do grupo dinâmico. Nesse caso, as máquinas podem não ser designadas como Hybrid Workers e, portanto, não podem executar a automação do Azure e trabalhos de Gerenciamento de Atualizações. Para garantir que as máquinas que você está esperando ver estejam configuradas como Hybrid Runbook Workers:

1. Na portal do Azure, acesse a conta de automação de um computador que não está aparecendo corretamente.

2. Em **Automação de Processo**, selecione **Grupos de Trabalho Híbrido**.

3. Selecione a guia **Grupos de trabalho híbridos do sistema**.

4. Valide se o Hybrid Worker está presente para esse computador.

5. Se o computador não estiver configurado como um Hybrid Worker, faça ajustes usando instruções em [Automatizar recursos em seu datacenter ou nuvem usando Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md).

6. Inclua o computador ao Grupo de Hybrid Runbook Worker.

7. Repita as etapas acima para todos os computadores que não foram exibidos na visualização.

## <a name="scenario-update-management-components-enabled-while-vm-continues-to-show-as-being-configured"></a><a name="components-enabled-not-working"></a>Cenário: Componentes de Gerenciamento de Atualizações habilitados, enquanto a VM continua a ser mostrada como sendo configurada

### <a name="issue"></a>Problema

Você continua a ver a seguinte mensagem em uma máquina virtual 15 minutos após a implantação:

```error
The components for the 'Update Management' solution have been enabled, and now this virtual machine is being configured. Please be patient, as this can sometimes take up to 15 minutes.
```

### <a name="cause"></a>Causa

Esse erro pode ocorrer por um dos seguintes motivos:

* A comunicação com a Conta de Automação está sendo bloqueada.

* Há um nome de computador duplicado com IDs de computador de origens diferentes. Esse cenário ocorre quando uma VM com um nome de computador específico é criada em grupos de recursos diferentes e está se comunicando com o mesmo espaço de trabalho do agente de logística na assinatura.

* A imagem de VM que está sendo implantada pode vir de uma máquina clonada que não foi preparada com o Sysprep (preparação do sistema) com o agente do Log Analytics para Windows instalado.

### <a name="resolution"></a>Resolução

Para ajudar a determinar o problema exato com a VM, execute a consulta a seguir no espaço de trabalho Log Analytics que está vinculado à sua conta de automação.

```
Update
| where Computer contains "fillInMachineName"
| project TimeGenerated, Computer, SourceComputerId, Title, UpdateState 
```

#### <a name="communication-with-automation-account-blocked"></a>A comunicação com a Conta de Automação foi bloqueada

Visite [Planejamento de rede](../update-management/overview.md#ports) para saber mais sobre quais endereços e portas devem ter permissão para que Gerenciamento de Atualizações funcione.

#### <a name="duplicate-computer-name"></a>O nome do computador duplicado

Renomeie suas VMs para garantir nomes exclusivos no ambiente.

#### <a name="deployed-image-from-cloned-machine"></a>Imagem implantada do computador clonado

Se você estiver usando uma imagem clonada, diferentes nomes de computador terão a mesma ID de computador de origem. Nesse caso:

1. No workspace do Log Analytics, remova a VM da pesquisa salva para a configuração de escopo `MicrosoftDefaultScopeConfig-Updates`, caso ela seja exibida. As pesquisas salvas podem ser encontradas em **Geral** no workspace.

2. Execute o cmdlet a seguir.

    ```azurepowershell-interactive
    Remove-Item -Path "HKLM:\software\microsoft\hybridrunbookworker" -Recurse -Force
    ```

3. Executar `Restart-Service HealthService` para reiniciar o serviço de integridade. Esta operação recria a chave e gera um novo UUID.

4. Se essa abordagem não funcionar, execute o sysprep na imagem primeiro e instale o agente do Log Analytics para Windows.

## <a name="scenario-you-receive-a-linked-subscription-error-when-you-create-an-update-deployment-for-machines-in-another-azure-tenant"></a><a name="multi-tenant"></a>Cenário: Você recebe um erro de assinatura vinculado ao criar uma implantação de atualização para computadores em outro locatário do Azure

### <a name="issue"></a>Problema

Você encontra o seguinte erro ao tentar criar uma implantação de atualização para computadores em outro locatário do Azure:

```error
The client has permission to perform action 'Microsoft.Compute/virtualMachines/write' on scope '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Automation/automationAccounts/automationAccountName/softwareUpdateConfigurations/updateDeploymentName', however the current tenant '00000000-0000-0000-0000-000000000000' is not authorized to access linked subscription '00000000-0000-0000-0000-000000000000'.
```

### <a name="cause"></a>Causa

Esse erro ocorre quando você cria uma implantação de atualização que tem máquinas virtuais do Azure em outro locatário incluído em uma implantação de atualização.

### <a name="resolution"></a>Resolução

Use a solução alternativa a seguir para obter esses itens agendados. Você pode usar o cmdlet [New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule) com o parâmetro `ForUpdateConfiguration` para criar uma agenda. Em seguida, use o cmdlet [New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/Az.Automation/New-AzAutomationSoftwareUpdateConfiguration) e passe os computadores no outro locatário para o parâmetro `NonAzureComputer`. O exemplo a seguir mostra como fazer isso:

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="scenario-unexplained-reboots"></a><a name="node-reboots"></a>Cenário: Reinicializações não explicadas

### <a name="issue"></a>Problema

Embora você tenha definido a opção **Controle de reinicialização** para **Nunca reinicializar**, as máquinas ainda estão sendo reinicializadas após a instalação das atualizações.

### <a name="cause"></a>Causa

O Windows Update pode ser modificado por várias chaves do registro, qualquer uma das quais pode modificar o comportamento de reinicialização.

### <a name="resolution"></a>Resolução

Examine as chaves do registro listadas em [Configuração de Atualizações Automáticas ao editar o registro](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry) e [Chaves do registro usadas para gerenciar a reinicialização](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart) para garantir que seus computadores estejam configurados corretamente.

## <a name="scenario-machine-shows-failed-to-start-in-an-update-deployment"></a><a name="failed-to-start"></a>Cenário: O computador mostra "Falha ao iniciar" em uma implantação de atualização

### <a name="issue"></a>Problema

Um computador mostra um status de `Failed to start`. Ao exibir os detalhes específicos do computador, você verá o seguinte erro:

```error
Failed to start the runbook. Check the parameters passed. RunbookName Patch-MicrosoftOMSComputer. Exception You have requested to create a runbook job on a hybrid worker group that does not exist.
```

### <a name="cause"></a>Causa

Esse problema pode ocorrer por um dos seguintes motivos:

* O computador não existe mais.
* O computador está desligado e inacessível.
* O computador tem um problema de conectividade de rede e, portanto, o Hybrid Worker no computador está inacessível.
* Houve uma atualização no agente do Log Analytics que alterou a ID do computador de origem.
* Se você tiver atingido o limite de 200 trabalhos simultâneos em uma conta de Automação, a execução de atualização foi limitada. Cada implantação é considerada um trabalho e cada computador em uma implantação de atualização conta como um trabalho. Qualquer outro trabalho de automação ou implantação de atualização em execução no momento em sua conta de automação conta para o limite de trabalhos simultâneos.

### <a name="resolution"></a>Resolução

Quando aplicável, use [grupos dinâmicos](../update-management/configure-groups.md) para suas implantações de atualização. Além disso, você pode seguir as seguintes etapas.

1. Verifique se seu computador ou servidor atende aos [requisitos](../update-management/overview.md#system-requirements).
2. Verifique a conectividade com o Hybrid Runbook Worker usando a solução de problemas do agente de Hybrid Runbook Worker. Para saber mais sobre a solução de problemas, consulte [solucionar problemas do agente de atualização](update-agent-issues.md).

## <a name="scenario-updates-are-installed-without-a-deployment"></a><a name="updates-nodeployment"></a>Cenário: As atualizações são instaladas sem uma implantação

### <a name="issue"></a>Problema

Quando registra um computador Windows no Gerenciamento de Atualizações, você vê as atualizações instaladas sem uma implantação.

### <a name="cause"></a>Causa

No Windows, as atualizações serão instaladas automaticamente assim que estiverem disponíveis. Esse comportamento pode causar confusão se você não tiver agendado uma atualização para ser implantada no computador.

### <a name="resolution"></a>Resolução

A chave do registro de `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU` usa como padrão uma configuração de 4: `auto download and install`.

Para clientes de Gerenciamento de Atualizações, é recomendável definir essa chave como 3: `auto download but do not auto install`.

Para obter mais informações, consulte [Configurando atualizações automáticas](/windows/deployment/update/waas-wu-settings#configure-automatic-updates).

## <a name="scenario-machine-is-already-registered-to-a-different-account"></a><a name="machine-already-registered"></a>Cenário: O computador já está registrado em outra conta

### <a name="issue"></a>Problema

Você vê a seguinte mensagem de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.InvalidOperationException: {"Message":"Machine is already registered to a different account."}
```

### <a name="cause"></a>Causa

O computador já foi implantado a outro espaço de trabalho para o Gerenciamento de Atualizações.

### <a name="resolution"></a>Resolução

1. Siga as instruções em [Computadores não aparecem no portal em Gerenciamento de Atualizações](#nologs) para garantir que o computador esteja configurado corretamente para o espaço de trabalho correto.
2. Execute a limpeza de artefatos antigos no computador, [excluindo o grupo de runbooks híbridos](../automation-windows-hrw-install.md#remove-a-hybrid-worker-group) e tente novamente.

## <a name="scenario-machine-cant-communicate-with-the-service"></a><a name="machine-unable-to-communicate"></a>Cenário: O computador não consegue se comunicar com o serviço

### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception System.Net.Http.HttpRequestException: An error occurred while sending the request. ---> System.Net.WebException: The underlying connection was closed: An unexpected error occurred on a receive. ---> System.ComponentModel.Win32Exception: The client and server can't communicate, because they do not possess a common algorithm
```

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception Newtonsoft.Json.JsonReaderException: Error parsing positive infinity value.
```

```error
The certificate presented by the service <wsid>.oms.opinsights.azure.com was not issued by a certificate authority used for Microsoft services. Contact your network administrator to see if they are running a proxy that intercepts TLS/SSL communication.
```

```error
Access is denied. (Exception form HRESULT: 0x80070005(E_ACCESSDENIED))
```

### <a name="cause"></a>Causa

Pode haver um proxy, gateway ou firewall bloqueando a comunicação de rede.

### <a name="resolution"></a>Resolução

Revise sua rede e garanta que portas e endereços apropriados sejam permitidos. Veja os [requisitos de rede](../automation-hybrid-runbook-worker.md#network-planning) para obter uma lista de portas e endereços que são exigidos pelo Gerenciamento de Atualizações e pelos Hybrid Runbook Workers.

## <a name="scenario-unable-to-create-self-signed-certificate"></a><a name="unable-to-create-selfsigned-cert"></a>Cenário: Não é possível criar um certificado autoassinado

### <a name="issue"></a>Problema

Você recebe uma das seguintes mensagens de erro:

```error
Unable to Register Machine for Patch Management, Registration Failed with Exception AgentService.HybridRegistration. PowerShell.Certificates.CertificateCreationException: Failed to create a self-signed certificate. ---> System.UnauthorizedAccessException: Access is denied.
```

### <a name="cause"></a>Causa

O Hybrid Runbook Worker não conseguiu gerar um certificado autoassinado.

### <a name="resolution"></a>Resolução

Verifique se a conta do sistema tem acesso de leitura à pasta **C:\ProgramData\Microsoft\Crypto\RSA** e tente novamente.

## <a name="scenario-the-scheduled-update-failed-with-a-maintenancewindowexceeded-error"></a><a name="mw-exceeded"></a>Cenário: A atualização agendada falhou com um erro MaintenanceWindowExceeded

### <a name="issue"></a>Problema

A janela de manutenção padrão para atualizações é de 120 minutos. Você pode aumentar a janela de manutenção para um máximo de 6 horas ou 360 minutos.

### <a name="resolution"></a>Resolução

Para entender por que isso ocorreu durante uma atualização após a inicialização bem-sucedida, [verifique a saída do trabalho](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) do computador afetado na execução. Você pode encontrar mensagens de erro específicas de sua máquina sobre as quais você pode pesquisar e realizar ações.  

Edite todas as implantações de atualização agendadas com falha e aumente a janela de manutenção.

Para obter mais informações sobre janelas de manutenção, veja [Instalar atualizações](../update-management/deploy-updates.md#schedule-an-update-deployment).

## <a name="scenario-machine-shows-as-not-assessed-and-shows-an-hresult-exception"></a><a name="hresult"></a>Cenário: O computador é exibido como “Não avaliado” e mostra uma exceção HRESULT

### <a name="issue"></a>Problema

* Você tem computadores que são exibidos como `Not assessed`em **Conformidade** e vê uma mensagem de exceção abaixo dela.
* Você vê um código de erro HRESULT no portal.

### <a name="cause"></a>Causa

O agente de atualização (Windows Update Agent no Windows; o gerenciador de pacotes para uma distribuição do Linux) não está configurado corretamente. O Gerenciamento de Atualizações depende do Agente de atualização do computador para fornecer as atualizações necessárias, o status do patch e os resultados de patches implantados. Sem essas informações, o Gerenciamento de Atualizações não pode relatar corretamente sobre os patches necessários ou instalados.

### <a name="resolution"></a>Resolução

Tente realizar atualizações localmente no computador. Se essa operação falhar, isso normalmente significa que há um erro de configuração do agente de atualização.

Esse problema costuma ser causado por problemas de firewall e configuração de rede. Use as seguintes verificações para corrigir o problema.

* Para o Linux, verifique a documentação apropriada para ter certeza de que você pode acessar o ponto de extremidade de rede do seu repositório de pacotes.

* Para o Windows, verifique a configuração do agente, conforme listado em [As atualizações não estão sendo baixadas do ponto de extremidade da intranet (WSUS/SCCM)](/windows/deployment/update/windows-update-troubleshooting#updates-arent-downloading-from-the-intranet-endpoint-wsussccm).

  * Se os computadores estiverem configurados para Windows Update, verifique se você pode acessar os pontos de extremidade descritos em [Problemas relacionados a HTTP/proxy](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy).
  * Se os computadores estiverem configurados para o Windows Server Update Services (WSUS), verifique se você pode acessar o servidor do WSUS configurado pela chave do registro [WUServer](/windows/deployment/update/waas-wu-settings).

Se você vir um HRESULT, clique duas vezes na exceção exibida em vermelho para ver a mensagem de exceção completa. Examine a tabela a seguir para saber as possíveis soluções ou ações recomendadas.

|Exceção  |Resolução ou ação  |
|---------|---------|
|`Exception from HRESULT: 0x……C`     | Pesquisar o código de erro relevante na [Lista de códigos de erro da atualização do Windows](https://support.microsoft.com/help/938205/windows-update-error-code-list) para localizar detalhes adicionais sobre a causa da exceção.        |
|`0x8024402C`</br>`0x8024401C`</br>`0x8024402F`      | Esses erros indicam problemas de conectividade de rede. Verifique se seu computador tem a conectividade de rede apropriada para o Gerenciamento de Atualizações. Consulte a seção sobre [planejamento de rede](../update-management/overview.md#ports) para obter uma lista de portas e endereços necessários.        |
|`0x8024001E`| A operação de atualização não foi concluída porque o serviço ou o sistema estava sendo desligado.|
|`0x8024002E`| O serviço Windows Update está desabilitado.|
|`0x8024402C`     | Se você estiver usando um servidor WSUS, verifique se os valores do registro para `WUServer` e `WUStatusServer` sob a chave do registro `HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate` têm o servidor WSUS correto.        |
|`0x80072EE2`|Problema de conectividade de rede ou problema de comunicação com um servidor WSUS configurado. Verifique as configurações do WSUS e confira se o serviço pode ser acessado do cliente.|
|`The service cannot be started, either because it is disabled or because it has no enabled devices associated with it. (Exception from HRESULT: 0x80070422)`     | Verifique se o serviço Windows Update (wuauserv) está em execução e não está desabilitado.        |
|`0x80070005`| Um erro de acesso negado pode ser causado por um dos seguintes motivos:<br> Computador infectado<br> Windows Update não configurado corretamente<br> Erro de permissão de arquivo com a pasta %WinDir%\SoftwareDistribution<br> Espaço em disco insuficiente na unidade do sistema (C:).
|Qualquer outra exceção genérica     | Faça uma pesquisa na Internet para as soluções possíveis e trabalhe com o suporte de TI local.         |

Revisar o arquivo **%Windir%\Windowsupdate.log** também pode ajudá-lo a determinar possíveis causas. Para obter mais informações sobre como ler o log, consulte [Como ler o arquivo Windowsupdate.log](https://support.microsoft.com/help/902093/how-to-read-the-windowsupdate-log-file).

Você também pode baixar e executar a [Solução de problemas do Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) para verificar se há problemas com o Windows Update no computador.

> [!NOTE]
> A documentação da [Solução de problemas do Windows Update](https://support.microsoft.com/help/4027322/windows-update-troubleshooter) indica que ele é para uso em clientes Windows, mas também funciona no Windows Server.

## <a name="scenario-update-run-returns-failed-status-linux"></a>Cenário: A execução da atualização retorna o status Falha (Linux)

### <a name="issue"></a>Problema

Uma execução de atualização é iniciada, mas encontra erros durante a execução.

### <a name="cause"></a>Causa

Possíveis causas:

* O gerenciador de pacotes não é saudável.
* O agente de atualização (WUA para Windows, Gerenciador de pacotes específico do distribuição para Linux) está configurado incorretamente.
* Pacotes específicos podem interferir na correção baseada em nuvem.
* O computador está inacessível.
* As atualizações tinham dependências que não foram resolvidas.

### <a name="resolution"></a>Resolução

Se ocorrerem falhas durante uma atualização após a inicialização bem-sucedida no Linux, [verifique a saída da tarefa](../update-management/deploy-updates.md#view-results-of-a-completed-update-deployment) da máquina afetada na execução. Você pode encontrar mensagens de erro específicas de sua máquina sobre as quais você pode pesquisar e realizar ações. O Gerenciamento de Atualizações requer que o gerenciador de pacotes seja saudável para implantações de atualização bem-sucedidas.

Se patches, pacotes ou atualizações específicas forem vistos imediatamente antes da falha do trabalho, você poderá tentar [excluir](../update-management/deploy-updates.md#schedule-an-update-deployment) esses itens da próxima implantação de atualização. Para coletar informações de log de Windows Update, consulte [Arquivos de log do Windows Update](/windows/deployment/update/windows-update-logs).

Se você não conseguir resolver um problema de patch, faça uma cópia do arquivo **/var/opt/microsoft/omsagent/run/automationworker/omsupdatemgmt.log** e preserve-o antes da próxima implantação de atualização começar para fins de solução de problemas.

## <a name="patches-arent-installed"></a>Os patches não estão instalados

### <a name="machines-dont-install-updates"></a>Os computadores não estão instalando atualizações

Tente executar as atualizações diretamente no computador. Se não for possível atualizar o computador, confira a [lista de possíveis erros no guia de solução de problemas](#hresult).

Se as atualizações são executadas localmente, tente remover e reinstalar o agente no computador seguindo as instruções em [Remover uma VM do Gerenciamento de Atualizações](../update-management/remove-vms.md).

### <a name="i-know-updates-are-available-but-they-dont-show-as-available-on-my-machines"></a>Sei que há atualizações disponíveis, mas elas não são mostradas conforme necessário nos meus computadores

Isso geralmente acontece se os computadores estiverem configurados para obter atualizações do WSUS ou do Microsoft Endpoint Configuration Manager, mas o WSUS e Configuration Manager não tiverem aprovado as atualizações.

Você pode verificar se os computadores estão configurados para WSUS/SCCM fazendo referência cruzada da `UseWUServer`chave de registro com as chaves de registro da seção [Configurar atualizações automáticas editando o registro deste artigo](https://support.microsoft.com/help/328010/how-to-configure-automatic-updates-by-using-group-policy-or-registry-s).

Se as atualizações não forem aprovadas no WSUS, elas não serão instaladas. Você pode verificar se há atualizações não aprovadas em Log Analytics, ao executar a consulta a seguir.

  ```kusto
  Update | where UpdateState == "Needed" and ApprovalSource == "WSUS" and Approved == "False" | summarize max(TimeGenerated) by Computer, KBID, Title
  ```

### <a name="updates-show-as-installed-but-i-cant-find-them-on-my-machine"></a>As atualizações aparecem como instaladas, mas não as encontro em meu computador

Normalmente, as atualizações são substituídas por outras atualizações. Para obter mais informações, confira [A atualização foi substituída](/windows/deployment/update/windows-update-troubleshooting#the-update-is-not-applicable-to-your-computer) no guia Solução de Problemas do Windows Update.

### <a name="installing-updates-by-classification-on-linux"></a>Instalando atualizações por classificação no Linux

Implantar atualizações no Linux por classificação (“Atualizações críticas e de segurança”) tem limitações importantes, especialmente no CentOS. Essas limitações estão documentadas na [página de visão geral do Gerenciamento de Atualizações](../update-management/overview.md#linux).

### <a name="kb2267602-is-consistently-missing"></a>KB2267602 está consistentemente ausente

KB2267602 é a [atualização de definição do Windows Defender](https://www.microsoft.com/wdsi/definitions). É atualizado diariamente.

## <a name="next-steps"></a>Próximas etapas

Se você não encontrou seu problema ou não conseguiu resolver seu problema, visite um dos seguintes canais para obter mais suporte.

* Obtenha respostas de especialistas do Azure por meio de [Fóruns do Azure](https://azure.microsoft.com/support/forums/).
* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport), a conta oficial de Microsoft Azure para melhorar a experiência do cliente.
* Registrar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **Obter Suporte**.
