---
title: "Validar a conta de automação do Azure | Microsoft Docs"
description: "Este artigo descreve como confirmar se a configuração de sua conta de automação está configurada corretamente."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: magoedte
ms.openlocfilehash: 55f5d5524019ac63565e5ddd1f47dbdd65f05065
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="test-azure-automation-run-as-account-authentication"></a>Como testar a autenticação da conta Executar como de Automação do Azure
Depois que uma conta de automação é criada com êxito, você pode executar um teste simples para confirmar que você é capaz de autenticar com êxito no Azure Resource Manager ou na implantação clássica do Azure usando sua conta Executar como de Automação recém-criada ou atualizada.    

## <a name="automation-run-as-authentication"></a>Autenticação da conta Executar como de automação
Use o exemplo de código abaixo para [criar um runbook do PowerShell](automation-creating-importing-runbook.md) a fim de verificar a autenticação usando a conta Executar como e também em seus runbooks personalizados para autenticar e gerenciar os recursos do Resource Manager com sua conta de Automação.   

    $connectionName = "AzureRunAsConnection"
    try
    {
        # Get the connection "AzureRunAsConnection "
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         

        "Logging in to Azure..."
        Add-AzureRmAccount `
           -ServicePrincipal `
           -TenantId $servicePrincipalConnection.TenantId `
           -ApplicationId $servicePrincipalConnection.ApplicationId `
           -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
    }
    catch {
       if (!$servicePrincipalConnection)
       {
          $ErrorMessage = "Connection $connectionName not found."
          throw $ErrorMessage
      } else{
          Write-Error -Message $_.Exception
          throw $_.Exception
      }
    }

    #Get all ARM resources from all resource groups
    $ResourceGroups = Get-AzureRmResourceGroup 

    foreach ($ResourceGroup in $ResourceGroups)
    {    
       Write-Output ("Showing resources in resource group " + $ResourceGroup.ResourceGroupName)
       $Resources = Find-AzureRmResource -ResourceGroupNameContains $ResourceGroup.ResourceGroupName | Select ResourceName, ResourceType
       ForEach ($Resource in $Resources)
       {
          Write-Output ($Resource.ResourceName + " of type " +  $Resource.ResourceType)
       }
       Write-Output ("")
    } 

Observe que o cmdlet usado para autenticar o runbook - **Add-AzureRmAccount**, usa o conjunto de parâmetros *ServicePrincipalCertificate* .  Ele se autentica usando o certificado de entidade de serviço, não as credenciais.  

Quando você [executa o runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) para validar sua conta Executar como, um [trabalho de runbook](automation-runbook-execution.md) é criado, a folha Trabalho é exibida e o status do trabalho é mostrado no bloco **Resumo do Trabalho**. O status do trabalho será iniciado como *Na fila* , indicando que ele está aguardando um runbook worker ficar disponível na nuvem. Mudará para *Iniciando* quando um trabalhador reivindicar o trabalho, em seguida, para *Executando* quando o runbook realmente começar a ser executado.  Quando o trabalho do runbook concluir, deveremos ver um status de **Concluído**.

Para ver os resultados detalhados do runbook, clique no bloco **Saída** .  Na folha **Saída**, você deverá ver que ele foi autenticado com êxito e que retornou uma lista de todos os recursos em todos os grupos de recursos de sua assinatura.  

Apenas lembre-se de remover o bloco de código que começa com o comentário `#Get all ARM resources from all resource groups` ao reutilizar o código para seus runbooks.

## <a name="classic-run-as-authentication"></a>Autenticação da conta Executar como clássica
Use o exemplo de código abaixo para [criar um runbook do PowerShell](automation-creating-importing-runbook.md) a fim de verificar a autenticação usando a conta Executar como clássica e também em seus runbooks personalizados para autenticar e gerenciar os recursos no modelo de implantação clássico.  

    $ConnectionAssetName = "AzureClassicRunAsConnection"
    # Get the connection
    $connection = Get-AutomationConnection -Name $connectionAssetName        

    # Authenticate to Azure with certificate
    Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
    $Conn = Get-AutomationConnection -Name $ConnectionAssetName
    if ($Conn -eq $null)
    {
       throw "Could not retrieve connection asset: $ConnectionAssetName. Assure that this asset exists in the Automation account."
    }

    $CertificateAssetName = $Conn.CertificateAssetName
    Write-Verbose "Getting the certificate: $CertificateAssetName" -Verbose
    $AzureCert = Get-AutomationCertificate -Name $CertificateAssetName
    if ($AzureCert -eq $null)
    {
       throw "Could not retrieve certificate asset: $CertificateAssetName. Assure that this asset exists in the Automation account."
    }

    Write-Verbose "Authenticating to Azure with certificate." -Verbose
    Set-AzureSubscription -SubscriptionName $Conn.SubscriptionName -SubscriptionId $Conn.SubscriptionID -Certificate $AzureCert
    Select-AzureSubscription -SubscriptionId $Conn.SubscriptionID
    
    #Get all VMs in the subscription and return list with name of each
    Get-AzureVM | ft Name

Quando você [executa o runbook](automation-starting-a-runbook.md#starting-a-runbook-with-the-azure-portal) para validar sua conta Executar como, um [trabalho de runbook](automation-runbook-execution.md) é criado, a folha Trabalho é exibida e o status do trabalho é mostrado no bloco **Resumo do Trabalho**. O status do trabalho será iniciado como *Na fila* , indicando que ele está aguardando um runbook worker ficar disponível na nuvem. Mudará para *Iniciando* quando um trabalhador reivindicar o trabalho, em seguida, para *Executando* quando o runbook realmente começar a ser executado.  Quando o trabalho do runbook concluir, deveremos ver um status de **Concluído**.

Para ver os resultados detalhados do runbook, clique no bloco **Saída** .  Na folha **Saída**, você deverá ver que ele foi autenticado com êxito e que retornou uma lista de todas as VMs do Azure por VMName implantadas em sua assinatura.  

Apenas lembre-se de remover o cmdlet **Get-AzureVM** ao reutilizar o código para seus runbooks.

## <a name="next-steps"></a>Próximas etapas
* Para começar a usar os runbooks do PowerShell, confira [Meu primeiro runbook do PowerShell](automation-first-runbook-textual-powershell.md).
* Para saber mais sobre a autorização gráfica, confira [Criação gráfica na automação do Azure](automation-graphical-authoring-intro.md).
