---
title: Usar um alerta para disparar um runbook de Automação do Azure
description: Saiba como disparar um runbook para executar quando um alerta do Azure é acionado.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 88fe7740170638e9e0d7398a02dcf83ab81f6ffc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61073838"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Usar um alerta para disparar um runbook de Automação do Azure

Você pode usar o [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) para monitorar logs e métricas de nível básico para a maioria dos serviços no Azure. Você pode chamar os runbooks da Automação do Azure usando [grupos de ações](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) ou usando alertas clássicos para automatizar tarefas com base em alertas. Este artigo mostra como configurar e executar um runbook usando alertas.

## <a name="alert-types"></a>Tipos de alerta

Você pode usar runbooks de automação com três tipos de alerta:
* Alertas de métrica clássicos
* Alertas do log de atividades
* Alertas de métrica quase em tempo real

Quando um alerta chama um runbook, a chamada real é uma solicitação HTTP POST para o webhook. O corpo da solicitação POST contém um objeto com o formato JSON que tem propriedades úteis relacionadas ao alerta. A tabela a seguir lista links para o esquema de conteúdo para cada tipo de alerta:

|Alerta  |DESCRIÇÃO|Esquema de conteúdo  |
|---------|---------|---------|
|[Alerta de métrica clássico](../monitoring-and-diagnostics/insights-alerts-portal.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação quando qualquer métrica de nível de plataforma atende a uma condição específica. Por exemplo, quando o valor de **% de CPU** em uma VM é maior que **90** para os últimos 5 minutos.| [Esquema de conteúdo de alerta de métrica de classe](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)         |
|[Alerta do log de atividades](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação quando qualquer novo evento no log de atividades do Azure corresponde a condições específicas. Por exemplo, quando uma operação `Delete VM` ocorre em **myProductionResourceGroup** ou quando um novo evento da Integridade do Serviço do Azure com um status **Ativo** é exibido.| [Esquema de conteúdo de alerta de log de atividades](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Alertas de métrica quase em tempo real](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Envia uma notificação mais rápido do que alertas de métrica quando uma ou mais métricas de nível de plataforma atendem às condições especificadas. Por exemplo, quando o valor de **% de CPU** em uma VM é maior que **90**e o valor de **Entrada de Rede** é maior do que **500 MB** para os últimos 5 minutos.| [Esquema de conteúdo de alerta de métrica quase em tempo real](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fautomation%2ftoc.json#payload-schema)          |

Já que os dados fornecidos por cada alerta são diferentes, cada tipo de alerta é tratado de forma diferente. Na próxima seção, você aprenderá a criar um runbook para lidar com diferentes tipos de alertas.

## <a name="create-a-runbook-to-handle-alerts"></a>Criar um runbook para tratar alertas

Para usar a Automação com alertas, será necessário um runbook que tenha lógica que gerencie o conteúdo JSON de alerta que é passado ao runbook. O seguinte exemplo de runbook deverá ser chamado a partir de um alerta do Azure.

Conforme descrito na seção anterior, cada tipo de alerta tem um esquema diferente. O script aceita os dados de webhook no parâmetro de entrada do runbook `WebhookData` de um alerta. Em seguida, o script avalia o conteúdo de JSON para determinar qual tipo de alerta foi usado.

Este exemplo usa um alerta de uma VM. Ele recupera os dados da VM do conteúdo e utiliza essas informações para interromper a VM. A conexão deve ser configurada na conta de Automação em que o runbook é executado. Ao usar alertas para disparar runbooks, é importante verificar o status do alerta no runbook que é disparado. O runbook vai disparar sempre que o alerta mudar de estado. Alertas têm vários estados. Os dois estados mais comuns são `Activated` e `Resolved`. Verifique se há esse estado em sua lógica de runbook para garantir que seu runbook não seja executado mais de uma vez. O exemplo neste artigo mostra como procurar alertas `Activated` somente.

O runbook usa o **AzureRunAsConnection** [Executar como conta](automation-create-runas-account.md) para se autenticar com o Azure para executar a ação de gerenciamento na VM.

Use este exemplo para criar um runbook chamado **Stop-AzureVmInResponsetoVMAlert**. Você pode modificar o script do PowerShell e usá-lo com muitos recursos diferentes.

1. Vá para sua conta da Automação do Azure.
1. Em **AUTOMAÇÃO DE PROCESSO**, selecione **Runbooks**.
1. Na parte superior da lista de runbooks, selecione **Adicionar um runbook**. 
1. Na página **Adicionar Runbook**, selecione **Criação Rápida**.
1. Para o nome do runbook, insira **Stop-AzureVmInResponsetoVMAlert**. Para o tipo de runbook, selecione **PowerShell**. Em seguida, selecione **Criar**.  
1. Copie o exemplo do PowerShell a seguir no painel **Editar**. 

    ```powershell-interactive
    <#
    .SYNOPSIS
    This runbook stops a resource management VM in response to an Azure alert trigger.

    .DESCRIPTION
    This runbook stops a resource management VM in response to an Azure alert trigger.
    The input is alert data that has the information required to identify which VM to stop.
    
    DEPENDENCIES
    - The runbook must be called from an Azure alert via a webhook.
    
    REQUIRED AUTOMATION ASSETS
    - An Automation connection asset called "AzureRunAsConnection" that is of type AzureRunAsConnection.
    - An Automation certificate asset called "AzureRunAsCertificate".

    .PARAMETER WebhookData
    Optional. (The user doesn't need to enter anything, but the service always passes an object.)
    This is the data that's sent in the webhook that's triggered from the alert.

    .NOTES
    AUTHOR: Azure Automation Team
    LASTEDIT: 2017-11-22
    #>

    [OutputType("PSAzureOperationResponse")]

    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )

    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData.
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema).
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # The schema isn't supported.
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if ($status -eq "Activated")
        {
            $ResourceType = $AlertContext.resourceType
            $ResourceGroupName = $AlertContext.resourceGroupName
            $SubId = $AlertContext.subscriptionId
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Use this only if this is a resource management VM.
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is the VM.
                Write-Verbose "This is a resource management VM." -Verbose

                # Authenticate to Azure by using the service principal and certificate. Then, set the subscription.
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Connect-AzureRmAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the VM.
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType isn't supported.
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated', so no action taken.
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```
1. Selecione **Publicar** para salvar e publicar o runbook.

## <a name="create-an-action-group"></a>Criar um grupo de ações

Um grupo de ação é uma coleção de ações que são disparadas por um alerta. Runbooks são apenas uma das muitas ações que você pode usar com grupos de ações.

1. No Portal do Azure, selecione **Monitor** > **Configurações** > **Grupos de ações**.
1. Selecione **Adicionar grupo de ações** e, em seguida, insira as informações necessárias:  
    1. Na caixa **Nome do grupo de ações**, digite um nome.
    1. Na caixa **Nome curto**, digite um nome. O nome curto é usado no lugar de um nome de grupo de ações completo quando as notificações são enviadas usando esse grupo de ações.
    1. A caixa **Assinatura** é automaticamente preenchida com a sua assinatura atual. Essa assinatura é aquela na qual o grupo de ações está salvo.
    1. Selecione o grupo de recursos no qual o grupo de ações está salvo.

Para esse exemplo, você cria duas ações: uma ação de runbook e uma ação de notificação.

### <a name="runbook-action"></a>Ação de runbook

Para criar uma ação de runbook no grupo de ações:

1. Em **AÇÕES**, para **NOME DA AÇÃO**, insira um nome para a ação. Para o **TIPO DE AÇÃO**, selecione **Runbook de Automação**.
1. Em **DETALHES**, selecione **Editar Detalhes**.  
1. Na página **Configurar Runbook**, em **Origem do runbook**, selecione **Usuário**.  
1. Selecione sua **Assinatura** e **Conta de automação** e selecione o runbook **Stop-AzureVmInResponsetoVMAlert**.  
1. Quando tiver terminado, selecione **OK**.

### <a name="notification-action"></a>Ação de notificação

Para criar uma ação de notificação no grupo de ações:

1. Em **AÇÕES**, para **NOME DA AÇÃO**, insira um nome para a ação. Para **TIPO DE AÇÃO**, selecione **Email**.  
1. Em **DETALHES**, selecione **Editar Detalhes**.  
1. Na página **Email**, insira o endereço de email a ser usado para notificação e, em seguida, selecione **OK**. Adicionar um endereço de email além do runbook como uma ação é algo útil. Você será notificado quando o runbook for iniciado.  

    O grupo de ações deverá ser semelhando à seguinte imagem:

   ![Adicionar página do grupo de ações](./media/automation-create-alert-triggered-runbook/add-action-group.png)
1. Selecione **OK** para criar o grupo de ações.

Você pode usar este grupo de ações nos [alertas de log de atividades](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) e [alertas quase em tempo real](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) que você criar.

## <a name="classic-alert"></a>Alerta clássico

Os alertas clássicos são baseados em métricas e não usam grupos de ações. No entanto, você pode configurar uma ação de runbook com base em um alerta clássico. 

Para criar um alerta clássico:

1. Selecione **Adicionar alerta de métrica**.
1. Nomeie seu alerta de métrica **myVMCPUAlert**. Insira uma descrição breve do alerta.
1. Para a condição de alerta de métrica, selecione **Maior que**. Para o valor **Limite**, selecione **10**. Para o valor **Período**, selecione **Nos últimos cinco minutos**.
1. Em **Executar uma ação**, selecione **Executar um runbook neste alerta**.
1. Na página **Configurar Runbook**, selecione **Usuário** para **Origem do runbook**. Escolha sua conta de automação e selecione o runbook **Stop-AzureVmInResponsetoVMAlert**. Selecione **OK**.
1. Para salvar a regra de alerta, selecione **OK**.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre como iniciar um runbook da Automação usando um webhook, veja [Iniciar um runbook com base em um webhook](automation-webhooks.md).
* Para obter detalhes sobre diferentes maneiras de iniciar um runbook, consulte [Como iniciar um runbook](automation-starting-a-runbook.md).
* Para saber mais sobre como criar um alerta de log de atividades, veja [Criar alertas do log de atividades](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Para saber como criar um alerta quase em tempo real, veja [Criar uma regra de alerta no Portal do Azure](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).

