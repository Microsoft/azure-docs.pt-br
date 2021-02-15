---
title: Gerenciar pré-scripts e pós-scripts na implantação do Gerenciamento de Atualizações no Azure
description: Este artigo informa como configurar e gerenciar pré-scripts e pós-scripts para implantações de atualizações.
services: automation
ms.subservice: update-management
ms.date: 12/17/2020
ms.topic: conceptual
ms.openlocfilehash: 4c37fe107d9256461e5aa632f859ae02c5dc42f5
ms.sourcegitcommit: e0ec3c06206ebd79195d12009fd21349de4a995d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/18/2020
ms.locfileid: "97683418"
---
# <a name="manage-pre-scripts-and-post-scripts"></a>Gerenciar pré-scripts e pós-scripts

Pré-scripts e pós-scripts são runbooks para execução na sua conta da Automação do Azure antes (pré-tarefa) e depois (pós-tarefa) de uma implantação de atualizações. Os pré-scripts e pós-scripts são executados no contexto do Azure, não localmente. Os pré-scripts são executados no início da implantação de atualizações. Os pós-scripts são executados no final da implantação e após quaisquer reinícios configurados.

## <a name="pre-script-and-post-script-requirements"></a>Requisitos de pré-script e pós-script

Para que um runbook seja usado como pré-script ou pós-script, você deve importá-lo para sua conta da Automação e [publicar o runbook](../manage-runbooks.md#publish-a-runbook).

## <a name="pre-script-and-post-script-parameters"></a>Parâmetros de pré-script e pós-script

Ao configurar pré-scripts e pós-scripts, você pode passar parâmetros, assim como agendar um runbook. Os parâmetros são definidos no momento da criação da implantação de atualização. Pré-scripts e pós-scripts dão suporte aos seguintes tipos:

* [char]
* [byte]
* [int]
* [long]
* [decimal]
* [single]
* [double]
* [DateTime]
* [string]

Os parâmetros de runbook pré-script e pós-script não oferecem suporte a tipos boolianos, de objetos ou de matriz. Esses valores causam a falha dos runbooks. 

Se precisar de outro tipo de objeto, você poderá convertê-lo em outro tipo usando com sua própria lógica no runbook.

Além dos parâmetros padrão do runbook, o parâmetro `SoftwareUpdateConfigurationRunContext` (tipo cadeia de caracteres JSON) é fornecido. Se você definir o parâmetro no seu runbook pré-script ou pós-script, ele será passado automaticamente pela implantação de atualizações. O parâmetro contém informações sobre a implantação de atualizações, que é um subconjunto das informações retornadas pela [API SoftwareUpdateconfigurations](/rest/api/automation/softwareupdateconfigurations/getbyname#updateconfiguration). As seções a seguir definem as propriedades associadas.

### <a name="softwareupdateconfigurationruncontext-properties"></a>Propriedades de SoftwareUpdateConfigurationRunContext

|Propriedade  |Descrição  |
|---------|---------|
|SoftwareUpdateConfigurationName     | O nome da configuração da atualização de software.        |
|SoftwareUpdateConfigurationRunId     | A ID exclusiva para a execução.        |
|SoftwareUpdateConfigurationSettings     | Uma coleção de propriedades relacionadas à configuração da atualização de software.         |
|SoftwareUpdateConfigurationSettings.operatingSystem     | Os sistemas operacionais selecionados para a implantação de atualização.         |
|SoftwareUpdateConfigurationSettings.duration     | A duração máxima da execução da implantação de atualizações, `PT[n]H[n]M[n]S` conforme a norma ISO8601, também chamada de janela de manutenção.          |
|SoftwareUpdateConfigurationSettings.Windows     | Uma coleção de propriedades relacionadas a computadores Windows.         |
|SoftwareUpdateConfigurationSettings.Windows.excludedKbNumbers     | Uma lista de KBs que são excluídas da implantação de atualização.        |
|SoftwareUpdateConfigurationSettings.Windows.includedUpdateClassifications     | Classificações de atualizações selecionadas para a implantação de atualização.        |
|SoftwareUpdateConfigurationSettings.Windows.rebootSetting     | Configurações de reinicialização para a implantação de atualizações.        |
|azureVirtualMachines     | Uma lista de resourceIds para as VMs do Azure na implantação de atualizações.        |
|nonAzureComputerNames|Uma lista dos FQDNs de computadores não Azure na implantação de atualizações.|

A seguir há um exemplo de cadeia de caracteres JSON passada para o parâmetro **SoftwareUpdateConfigurationRunContext**:

```json
"SoftwareUpdateConfigurationRunContext":{
      "SoftwareUpdateConfigurationName":"sampleConfiguration",
      "SoftwareUpdateConfigurationRunId":"00000000-0000-0000-0000-000000000000",
      "SoftwareUpdateConfigurationSettings":{
         "operatingSystem":"Windows",
         "duration":"PT2H0M",
         "windows":{
            "excludedKbNumbers":[
               "168934",
               "168973"
            ],
            "includedUpdateClassifications":"Critical",
            "rebootSetting":"IfRequired"
         },
         "azureVirtualMachines":[
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-01",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-02",
            "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresources/providers/Microsoft.Compute/virtualMachines/vm-03"
         ],
         "nonAzureComputerNames":[
            "box1.contoso.com",
            "box2.contoso.com"
         ]
      }
   }
```

Um exemplo completo com todas as propriedades pode ser encontrado em: [Obter configuração de atualização de software por nome](/rest/api/automation/softwareupdateconfigurations/getbyname#examples).

> [!NOTE]
> O objeto `SoftwareUpdateConfigurationRunContext` pode conter entradas duplicadas para computadores. Isso pode fazer com que os pré-scripts e os pós-scripts sejam executados várias vezes no mesmo computador. Para contornar esse comportamento, use `Sort-Object -Unique` para selecionar apenas nomes de VM exclusivos.

> [!NOTE]
> Atualmente, somente os runbooks do PowerShell têm suporte como scripts de pré/pós. Outros tipos de runbook como Python, gráfico, fluxo de trabalho do PowerShell, fluxo de trabalho gráfico do PowerShell atualmente não têm suporte como scripts de pré/pós.

## <a name="use-a-pre-script-or-post-script-in-a-deployment"></a>Usar um pré-script ou pós-script em uma implantação

Para usar um pré-script ou um pós-script em uma implantação de atualização, comece criando uma implantação de atualização. Selecione **Pré-Scripts + Pós-scripts**. Essa ação abre a página **Selecionar Pré-scripts + Pós-scripts**.

![Selecionar scripts](./media/pre-post-scripts/select-scripts.png)

Selecione o script que você deseja usar. Neste exemplo, usamos o runbook **UpdateManagement-TurnOnVms**. Quando você seleciona o runbook, a página **Configurar Script** é aberta. Selecione **Pré-script** e, em seguida, selecione **OK**.

Repita esse processo para o script **UpdateManagement-TurnOffVms**. Porém, quando escolher o **Tipo de script**, selecione **Pós-script**.

A seção **Itens selecionados** agora mostra os dois scripts selecionados. Um é um pré-script e o outro é um post-script:

![Itens selecionados](./media/pre-post-scripts/selected-items.png)

Conclua a configuração da implantação de atualizações.

Quando a implantação de atualizações estiver concluída, você poderá acessar **Implantações de atualizações** para exibir os resultados. Como pode ver, são informados os status do pré-script e do pós-script:

![Atualizar resultados](./media/pre-post-scripts/update-results.png)

Ao selecionar a execução da implantação de atualizações, você verá detalhes adicionais de pré-scripts e pós-scripts. Um link para a fonte do script no momento da execução é fornecido.

![Resultados da execução da implantação](./media/pre-post-scripts/deployment-run.png)

## <a name="stop-a-deployment"></a>Interromper uma implantação

Se você deseja interromper uma implantação com base em um pré-script, [lance](../automation-runbook-execution.md#throw) uma exceção. Se não, a implantação e o post-script ainda serão executados. O snippet de código a seguir mostra como lançar uma exceção.

```powershell
#In this case, we want to terminate the patch job if any run fails.
#This logic might not hold for all cases - you might want to allow success as long as at least 1 run succeeds
foreach($summary in $finalStatus)
{
    if ($summary.Type -eq "Error")
    {
        #We must throw in order to fail the patch deployment.
        throw $summary.Summary
    }
}
```

## <a name="interact-with-machines"></a>Interação com computadores

Pré-scripts e pós-scripts são executados como runbooks em sua conta de automação e não diretamente nos computadores em sua implantação. As pré-tarefas e as pós-tarefas também são executadas no contexto do Azure e não têm acesso a computadores não Azure. As seções a seguir mostram como você pode interagir com os computadores diretamente, sejam eles VMs do Azure ou computadores não Azure.

### <a name="interact-with-azure-machines"></a>Interação com computadores Azure

Pré-tarefas e pós-tarefas são executadas como runbooks e não são executadas nativamente nas VMs do Azure em sua implantação. Para interagir com as VMs do Azure, você precisa ter os seguintes itens:

* Uma conta Executar como
* Um runbook que você deseja executar

Para interagir com computadores do Azure, você deve usar o cmdlet [Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) para interagir com suas VMs do Azure. Para obter um exemplo de como fazer isso, confira o exemplo do runbook [Gerenciamento de Atualizações – executar script com o comando Executar](https://github.com/azureautomation/update-management-run-script-with-run-command).

### <a name="interact-with-non-azure-machines"></a>Interação com computadores não Azure

As pré-tarefas e as pós-tarefas são executadas no contexto do Azure e não têm acesso a computadores não Azure. Para interagir com os computadores não Azure, você precisa ter os seguintes itens:

* Uma conta Executar como
* Um Hybrid Runbook Worker instalado no computador
* Um runbook que deseja executar localmente
* Um runbook pai

Para interagir com computadores não Azure, um runbook pai é executado no contexto do Azure. Esse runbook chama um runbook filho com o cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). É necessário especificar o parâmetro `RunOn` e fornecer o nome do Hybrid Runbook Worker para o script ser executado. Confira o exemplo de runbook [Gerenciamento de Atualizações – executar script localmente](https://github.com/azureautomation/update-management-run-script-locally).

## <a name="abort-patch-deployment"></a>Anular implantação de patch

Se o seu pré-script retornar um erro, você poderá anular a implantação. Para fazer isso, você deve [lançar](/powershell/module/microsoft.powershell.core/about/about_throw) um erro em seu script para qualquer lógica que possa constituir uma falha.

```powershell
if (<My custom error logic>)
{
    #Throw an error to fail the patch deployment.
    throw "There was an error, abort deployment"
}
```

## <a name="samples"></a>Exemplos

Exemplos de pré-scripts e pós-scripts podem ser encontrados na [organização GitHub da automação do Azure](https://github.com/azureautomation) e no [Galeria do PowerShell](https://www.powershellgallery.com/packages?q=Tags%3A%22UpdateManagement%22+Tags%3A%22Automation%22), ou você pode importá-los por meio do portal do Azure. Para tal, na sua conta da Automação, em **Automação de Processo**, selecione **Galeria de Runbooks**. Use **Gerenciamento de Atualizações** para o filtro.

![Lista de galerias](./media/pre-post-scripts/runbook-gallery.png)

Ou pode pesquisar por elas usando o nome do script, como mostrado na lista a seguir:

* Gerenciamento de Atualizações – Ligar VMs
* Gerenciamento de Atualizações – Desligar VMs
* Gerenciamento de Atualizações – Executar script localmente
* Gerenciamento de Atualizações – Modelo para scripts pré/pós
* Gerenciamento de Atualizações – Executar script com comando de execução

> [!IMPORTANT]
> Depois de importar os runbooks, você precisa publicá-los antes que eles possam ser usados. Para fazer isso, localize o runbook na sua conta da Automação, selecione **Editar** e, em seguida, **Publicar**.

Todos os exemplos se baseiam no modelo básico que é definido no exemplo a seguir. Esse modelo pode ser usado para criar seu runbook para usar com pré-scripts e pós-scripts. A lógica necessária para autenticar com o Azure e lidar com o parâmetro `SoftwareUpdateConfigurationRunContext` está incluída.

```powershell
<#
.SYNOPSIS
 Barebones script for Update Management Pre/Post

.DESCRIPTION
  This script is intended to be run as a part of Update Management pre/post-scripts.
  It requires a RunAs account.

.PARAMETER SoftwareUpdateConfigurationRunContext
  This is a system variable which is automatically passed in by Update Management during a deployment.
#>

param(
    [string]$SoftwareUpdateConfigurationRunContext
)
#region BoilerplateAuthentication
#This requires a RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID
#endregion BoilerplateAuthentication

#If you wish to use the run context, it must be converted from JSON
$context = ConvertFrom-Json  $SoftwareUpdateConfigurationRunContext
#Access the properties of the SoftwareUpdateConfigurationRunContext
$vmIds = $context.SoftwareUpdateConfigurationSettings.AzureVirtualMachines | Sort-Object -Unique
$runId = $context.SoftwareUpdateConfigurationRunId

Write-Output $context

#Example: How to create and write to a variable using the pre-script:
<#
#Create variable named after this run so it can be retrieved
New-AzAutomationVariable -ResourceGroupName $ResourceGroup –AutomationAccountName $AutomationAccount –Name $runId -Value "" –Encrypted $false
#Set value of variable
Set-AutomationVariable –Name $runId -Value $vmIds
#>

#Example: How to retrieve information from a variable set during the pre-script
<#
$variable = Get-AutomationVariable -Name $runId
#>
```

> [!NOTE]
> Para runbooks não gráficos do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Você pode usar esses cmdlets ou pode [atualizar seus módulos](../automation-update-azure-modules.md) em sua conta de Automação para as versões mais recentes. Talvez você precise atualizar os módulos mesmo que você tenha acabado de criar uma conta de Automação.

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes sobre o gerenciamento de atualizações, consulte [gerenciar atualizações e patches para suas VMs](manage-updates-for-vm.md).
