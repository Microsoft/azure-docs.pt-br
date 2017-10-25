---
title: " Corrigir Alertas da VM do Azure com Runbooks de Automação | Microsoft Docs"
description: "Este artigo demonstra como integrar os alertas de Máquina Virtual do Azure com runbooks de Automação do Azure e corrigir automaticamente os problemas"
services: automation
documentationcenter: 
author: eslesar
manager: jwhit
editor: tysonn
ms.assetid: 1f7baa7f-7283-4a4f-9385-3f5cd1062c7f
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: csand;magoedte
ms.openlocfilehash: 18cccc88ab74235722e2f4886671fc483ab67da8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-automation-scenario---remediate-azure-vm-alerts"></a>Cenário de Automação do Azure - corrigir alertas de VM do Azure
As Máquinas Virtuais do Azure e a Automação do Azure lançaram um novo recurso que permite que você configure os alertas de VM (Máquina Virtual) para executar runbooks de Automação. Essa nova funcionalidade permite que você execute a correção standard automaticamente em resposta a alertas de VM, como reiniciar ou parar a VM.

Anteriormente, durante a criação de regra de alerta de VM você podia [especificar um webhook de Automação](https://azure.microsoft.com/blog/using-azure-automation-to-take-actions-on-azure-alerts/) para um runbook para executar o runbook sempre que o alerta fosse disparado. No entanto, isso exigia que você realizasse o trabalho de criar o runbook, criar o webhook para o runbook e, em seguida, copiasse e colasse o webhook durante a criação da regra de alerta. Com essa nova versão, o processo é muito mais fácil, porque você pode escolher diretamente um runbook em uma lista durante a criação da regra de alerta e pode escolher uma conta da Automação que executa o runbook ou, facilmente, criar uma conta.

Neste artigo, mostramos como é fácil configurar um alerta de VM do Azure e configurar um runbook de Automação para ser executado sempre que o alerta for disparado. Os cenários de exemplo incluem reiniciar uma VM quando o uso de memória exceder algum limite devido a um aplicativo na VM com um vazamento de memória ou parar uma VM quando o tempo de usuário da CPU foi abaixo de 1% na hora anterior e não está em uso. Explicaremos também como a criação automatizada de uma entidade de serviço em sua conta de Automação simplifica o uso de runbooks na correção de alerta do Azure.

## <a name="create-an-alert-on-a-vm"></a>Criar um alerta em uma VM
Execute as seguintes etapas para configurar um alerta para iniciar um runbook quando o limite for atingido.

> [!NOTE]
> Com esta versão, damos suporte apenas a máquinas virtuais V2 e o suporte para VMs clássicas será adicionado em breve.  
> 
> 

1. Faça logon no portal do Azure e clique em **Máquinas Virtuais**.  
2. Selecione uma das máquinas virtuais.  
3. Na tela da VM, na seção **Monitoramento**, clique em **Regras de alerta**.
4. No painel **Regras de alerta**, clique em **Adicionar alerta**.

Isso abrirá a página **Adicionar uma regra de alerta**, na qual você pode configurar as condições do alerta e escolher entre uma ou todas essas opções: enviar email para alguém, usar um webhook para encaminhar o alerta para outro sistema e/ou executar um runbook de Automação na tentativa de resposta para corrigir o problema.

## <a name="configure-a-runbook"></a>Configurar um runbook
Para configurar um runbook para ser executado quando o limite de alerta da VM for atingido, selecione **Runbook de Automação**. No painel **Configurar runbook**, você pode selecionar o runbook a ser executado e a conta de Automação na qual executar o runbook.

![Configurar um runbook de Automação e criar uma nova conta de Automação](media/automation-azure-vm-alert-integration/ConfigureRunbookNewAccount.png)

> [!NOTE]
> Para esta versão, você pode escolher entre três runbooks que o serviço fornece: Reiniciar VM, Parar VM ou Remover VM (excluí-la).  A capacidade de selecionar outros runbooks ou um de seus próprios runbooks estará disponível em uma versão futura.
> 
> 

![Runbooks dentre os quais escolher](media/automation-azure-vm-alert-integration/RunbooksToChoose.png)

Depois de selecionar um dos três runbooks disponíveis, a lista suspensa **Conta de Automação** é exibida e você pode selecionar uma conta de automação como a qual o runbook será executado. Os runbooks precisam ser executados no contexto de uma [Conta de Automação](automation-security-overview.md) que esteja na sua assinatura do Azure. Você pode selecionar uma conta de Automação que já criou ou pode ter uma nova conta de Automação criada para você.

Os runbooks fornecidos se autenticam no Azure usando uma entidade de serviço. Se você optar por executar o runbook em uma de suas contas de Automação existentes, nós criaremos automaticamente a entidade de serviço para você. Se você optar por criar uma nova conta de Automação, nós criaremos automaticamente a conta e a entidade de serviço. Em ambos os casos, dois ativos também são criados na conta de Automação – um ativo de certificado denominado **AzureRunAsCertificate** e um ativo de conexão denominado **AzureRunAsConnection**. Os runbooks usam **AzureRunAsConnection** para se autenticar no Azure para executar a ação de gerenciamento na VM.

> [!NOTE]
> A entidade de serviço é criada no escopo de assinatura e é atribuída à função de Colaborador. Essa função é necessária para a conta ter permissão para executar runbooks de Automação para gerenciar VMs do Azure.  A criação de uma conta de Automação e/ou entidade de serviço é um evento único. Depois que elas forem criadas, você poderá usar essa conta para executar runbooks para outros alertas de VM do Azure.
> 
> 

Quando você clica em **OK** , o alerta é configurado e, se você selecionou a opção para criar uma nova conta de automação, ela é criada junto com a entidade de serviço.  Isso pode levar alguns segundos para ser concluído.  

![Runbook sendo configurado](media/automation-azure-vm-alert-integration/RunbookBeingConfigured.png)

Após a configuração ser concluída, você verá o nome do runbook na página **Adicionar uma regra de alerta**.

![Runbook configurado](media/automation-azure-vm-alert-integration/RunbookConfigured.png)

Clique em **OK** na página **Adicionar uma regra de alerta**.  A regra de alerta será criada e ativada se a máquina virtual estiver em estado de execução.

### <a name="enable-or-disable-a-runbook"></a>Habilitar ou desabilitar um runbook
Se você tiver um runbook configurado para um alerta, poderá desabilitá-lo sem remover a configuração do runbook. Isso permite que você mantenha o alerta em execução e talvez teste algumas das regras de alerta e depois habilite novamente o runbook.

## <a name="create-a-runbook-that-works-with-an-azure-alert"></a>Criar um runbook que funciona com um alerta do Azure
Quando você escolhe um runbook como parte de uma regra de alerta do Azure, o runbook deve conter lógica para gerenciar os dados de alerta que são passados para ele.  Quando um runbook é configurado em uma regra de alerta, um Webhook é criado para o runbook; esse Webhook, em seguida, é usado para iniciar o runbook toda vez o alerta é disparado.  A chamada real para iniciar o runbook é uma solicitação HTTP POST para a URL do Webhook. O corpo da solicitação POST contém um objeto com o formato JSON que contém propriedades úteis relacionadas ao alerta.  Como você pode ver abaixo, os dados de alerta contém detalhes como subscriptionID, resourceGroupName, resourceName e resourceType.

### <a name="example-of-alert-data"></a>Exemplo de Dados de alerta
```
{
    "WebhookName": "AzureAlertTest",
    "RequestBody": "{
    \"status\":\"Activated\",
    \"context\": {
        \"id\":\"/subscriptions/<subscriptionId>/resourceGroups/MyResourceGroup/providers/microsoft.insights/alertrules/AlertTest\",
        \"name\":\"AlertTest\",
        \"description\":\"\",
        \"condition\": {
            \"metricName\":\"CPU percentage guest OS\",
            \"metricUnit\":\"Percent\",
            \"metricValue\":\"4.26337916666667\",
            \"threshold\":\"1\",
            \"windowSize\":\"60\",
            \"timeAggregation\":\"Average\",
            \"operator\":\"GreaterThan\"},
        \"subscriptionId\":\<subscriptionID> \",
        \"resourceGroupName\":\"TestResourceGroup\",
        \"timestamp\":\"2016-04-24T23:19:50.1440170Z\",
        \"resourceName\":\"TestVM\",
        \"resourceType\":\"microsoft.compute/virtualmachines\",
        \"resourceRegion\":\"westus\",
        \"resourceId\":\"/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\",
        \"portalLink\":\"https://portal.azure.com/#resource/subscriptions/<subscriptionId>/resourceGroups/TestResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM\"
        },
    \"properties\":{}
    }",
    "RequestHeader": {
        "Connection": "Keep-Alive",
        "Host": "<webhookURL>"
    }
}
```

Quando o serviço de Automação de Webhook recebe o HTTP POST, ele extrai os dados de alerta e passa-os para o runbook no parâmetro de entrada de runbook WebhookData.  Abaixo está um exemplo de runbook que mostra como usar o parâmetro WebhookData, extrair os dados de alerta e usá-lo para gerenciar os recursos do Azure que disparou o alerta.

### <a name="example-runbook"></a>Runbook de exemplo
```
#  This runbook restarts an ARM (V2) VM in response to an Azure VM alert.

[OutputType("PSAzureOperationResponse")]

param ( [object] $WebhookData )

if ($WebhookData)
{
    # Get the data object from WebhookData
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Assure that the alert status is 'Activated' (alert condition went from false to true)
    # and not 'Resolved' (alert condition went from true to false)
    if ($WebhookBody.status -eq "Activated")
    {
        # Get the info needed to identify the VM
        $AlertContext = [object] $WebhookBody.context
        $ResourceName = $AlertContext.resourceName
        $ResourceType = $AlertContext.resourceType
        $ResourceGroupName = $AlertContext.resourceGroupName
        $SubId = $AlertContext.subscriptionId

        # Assure that this is the expected resource type
        Write-Verbose "ResourceType: $ResourceType"
        if ($ResourceType -eq "microsoft.compute/virtualmachines")
        {
            # This is an ARM (V2) VM

            # Authenticate to Azure with service principal and certificate
            $ConnectionAssetName = "AzureRunAsConnection"
            $Conn = Get-AutomationConnection -Name $ConnectionAssetName
            if ($Conn -eq $null) {
                throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
            }
            Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
            Set-AzureRmContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

            # Restart the VM
            Restart-AzureRmVM -Name $ResourceName -ResourceGroupName $ResourceGroupName
        } else {
            Write-Error "$ResourceType is not a supported resource type for this runbook."
        }
    } else {
        # The alert status was not 'Activated' so no action taken
        Write-Verbose ("No action taken. Alert status: " + $WebhookBody.status)
    }
} else {
    Write-Error "This runbook is meant to be started from an Azure alert only."
}
```

## <a name="summary"></a>Resumo
Quando você configura um alerta em uma VM do Azure, agora você tem a capacidade de configurar facilmente um runbook de Automação para executar a ação de correção automaticamente quando o alerta for disparado. Para esta versão, você pode escolher dentre runbooks para reiniciar, parar ou excluir uma VM dependendo do cenário de alerta. Isso é apenas o começo da habilitação de cenários em que você controla as ações (notificação, solução de problemas, correção) que serão executadas automaticamente quando um alerta for disparado.

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar runbooks gráficos, veja [O meu primeiro runbook gráfico](automation-first-runbook-graphical.md)
* Para começar a usar os runbooks de fluxo de trabalho do PowerShell, confira [Meu primeiro runbook de fluxo de trabalho do PowerShell](automation-first-runbook-textual.md)
* Para saber mais sobre os tipos de runbook, suas vantagens e limitações, veja [Tipos de runbook da Automação do Azure](automation-runbook-types.md)

