---
title: Usar um alerta para disparar um runbook de Automação do Azure
description: Este artigo descreve como disparar um runbook para executar quando um alerta do Azure é acionado.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: 03814766d7bc873855df261a50a40b8d342fa69b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054239"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Usar um alerta para disparar um runbook de Automação do Azure

Você pode usar o [Azure Monitor](../azure-monitor/overview.md) para monitorar logs e métricas de nível básico para a maioria dos serviços no Azure. Você pode chamar os runbooks da Automação do Azure usando [grupos de ações](../azure-monitor/platform/action-groups.md) ou usando alertas clássicos para automatizar tarefas com base em alertas. Este artigo mostra como configurar e executar um runbook usando alertas.

## <a name="alert-types"></a>Tipos de alerta

Você pode usar runbooks de automação com três tipos de alerta:

* Alertas comuns
* Alertas do log de atividades
* Alertas de métrica quase em tempo real

> [!NOTE]
> O esquema de alerta comum padroniza a experiência de consumo para notificações de alerta no Azure hoje. Historicamente, os três tipos de alertas no Azure hoje (métrica, log e log de atividades) têm seus próprios modelos de email, esquemas de webhook etc. Para saber mais, confira [Esquema de alerta comum](../azure-monitor/platform/alerts-common-schema.md)

Quando um alerta chama um runbook, a chamada real é uma solicitação HTTP POST para o webhook. O corpo da solicitação POST contém um objeto com o formato JSON que tem propriedades úteis relacionadas ao alerta. A tabela a seguir lista links para o esquema de conteúdo para cada tipo de alerta:

|Alerta  |Descrição|Esquema de conteúdo  |
|---------|---------|---------|
|[Alertas comuns](../azure-monitor/platform/alerts-common-schema.md)|O esquema de alerta comum que padroniza a experiência de consumo para notificações de alerta no Azure hoje.|Esquema de conteúdo de alerta comum|
|[Alerta do log de atividades](../azure-monitor/platform/activity-log-alerts.md)    |Envia uma notificação quando qualquer novo evento no log de atividades do Azure corresponde a condições específicas. Por exemplo, quando uma operação `Delete VM` ocorre em **myProductionResourceGroup** ou quando um novo evento da Integridade do Serviço do Azure com um status Ativo é exibido.| [Esquema de conteúdo de alerta de log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Alertas de métrica quase em tempo real](../azure-monitor/platform/alerts-metric-near-real-time.md)    |Envia uma notificação mais rápido do que alertas de métrica quando uma ou mais métricas de nível de plataforma atendem às condições especificadas. Por exemplo, quando o valor de **% de CPU** em uma VM é maior que 90 e o valor de **Entrada de Rede** é maior do que 500 MB para os últimos 5 minutos.| [Esquema de conteúdo de alerta de métrica quase em tempo real](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Já que os dados fornecidos por cada alerta são diferentes, cada tipo de alerta é tratado de forma diferente. Na próxima seção, você aprenderá a criar um runbook para lidar com diferentes tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Criar um runbook para tratar alertas

Para usar a Automação com alertas, será necessário um runbook que tenha lógica que gerencie o conteúdo JSON de alerta que é passado ao runbook. O seguinte exemplo de runbook deverá ser chamado a partir de um alerta do Azure.

Conforme descrito na seção anterior, cada tipo de alerta tem um esquema diferente. O script aceita os dados de webhook de um alerta no parâmetro de entrada do runbook `WebhookData`. Em seguida, o script avalia o conteúdo de JSON para determinar qual tipo de alerta está sendo usado.

Este exemplo usa um alerta de uma VM. Ele recupera os dados da VM do conteúdo e utiliza essas informações para interromper a VM. A conexão deve ser configurada na conta de Automação em que o runbook é executado. Ao usar alertas para disparar runbooks, é importante verificar o status do alerta no runbook que é disparado. O runbook dispara sempre que o alerta muda de estado. Os alertas têm vários estados, e os dois mais comuns são Ativo e Resolvido. Verifique o estado em sua lógica de runbook para garantir que o runbook não seja executado mais de uma vez. O exemplo neste artigo mostra como procurar alertas somente com o estado Ativo.

O runbook usa [Executar como conta](./automation-security-overview.md) do ativo de conexão `AzureRunAsConnection`para se autenticar com o Azure para executar a ação de gerenciamento na VM.

Use este exemplo para criar um runbook chamado **Stop-AzureVmInResponsetoVMAlert**. Você pode modificar o script do PowerShell e usá-lo com muitos recursos diferentes.

1. Vá para sua conta da Automação do Azure.
2. Em **Automação de Processo**, selecione **Runbooks**.
3. Na parte superior da lista de runbooks, selecione **+ Criar um runbook**.
4. Na página **Adicionar Runbook**, insira **Stop-AzureVmInResponsetoVMAlert** como nome do runbook. Para o tipo de runbook, selecione **PowerShell**. Em seguida, selecione **Criar**.  
5. Copie o exemplo do PowerShell a seguir para a página **Editar**.

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Selecione **Publicar** para salvar e publicar o runbook.

## <a name="create-the-alert"></a>Criar o alerta

Os alertas usam grupos de ações, que são coleções de ações disparadas pelo alerta. Runbooks são apenas uma das muitas ações que você pode usar com grupos de ações.

1. Na sua conta de Automação, selecione **Alertas** em **Monitoramento**.
1. Selecione **+ Nova regra de alerta**.
1. Clique em **Selecionar** em **Recurso**. Na página **Selecionar um recurso**, selecione sua VM para alertar e clique em **Concluído**.
1. Clique em **Adicionar condição** em **Condição**. Selecione o sinal que você deseja usar, por exemplo **Percentual de CPU** e clique em **Concluído**.
1. Na página **Configurar lógica de sinal**, insira seu **Limite de valor** em **Lógica de alerta** e clique em **Concluído**.
1. Em **Grupos de ações**, selecione **Criar Novo**.
1. Na página **Adicionar grupo de ações**, dê um nome a seu grupo de ações e um nome curto.
1. Dê um nome à ação. Para o tipo de ação, selecione **Runbook de Automação**.
1. Selecione em **Editar Detalhes**. Na página **Configurar Runbook**, em **Origem do runbook**, selecione **Usuário**.  
1. Selecione sua **Assinatura** e **Conta de automação** e selecione o runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. Selecione **Sim** para **Habilitar o esquema de alerta comum**.
1. Selecione **OK** para criar o grupo de ações.

    ![Adicionar página do grupo de ações](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Você pode usar este grupo de ações nos [alertas de log de atividades](../azure-monitor/platform/activity-log-alerts.md) e [alertas quase em tempo real](../azure-monitor/platform/alerts-overview.md) que você criar.

1. Em **Detalhes do Alerta**, adicione um nome de regra de alerta e uma descrição e clique em **Criar regra de alerta**.

## <a name="next-steps"></a>Próximas etapas

* Para iniciar um runbook usando um webhook, confira [Iniciar um runbook de um webhook](automation-webhooks.md).
* Para descobrir diferentes maneiras de iniciar um runbook, confira [Iniciar um runbook](./start-runbooks.md).
* Para criar um alerta de log de atividades, confira [Criar alertas do log de atividades](../azure-monitor/platform/activity-log-alerts.md).
* Para saber como criar um alerta quase em tempo real, veja [Criar uma regra de alerta no Portal do Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
