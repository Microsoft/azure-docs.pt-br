---
title: "Execute runbooks no Hybrid Runbook Workers da Automação do Azure | Microsoft Docs"
description: "Este artigo fornece informações sobre a execução de runbooks em computadores em seu datacenter local ou provedor de nuvem com a função de Hybrid Runbook Worker."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: tysonn
ms.assetid: 06227cda-f3d1-47fe-b3f8-436d2b9d81ee
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2017
ms.author: magoedte
ms.openlocfilehash: d069b5040e0e280e54d4ffd8eccdacca302b7cc5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Executar runbooks em um Hybrid Runbook Worker 
Não há nenhuma diferença na estrutura de runbooks executados na Automação do Azure e daqueles que executam em um Runbook Worker Híbrido. Os runbooks que você usar com cada um provavelmente serão bem diferentes uns dos outros, já que os runbooks direcionados a um Hybrid Runbook Worker normalmente gerenciam recursos no próprio computador local ou em recursos no ambiente local onde é implantado, enquanto runbooks na Automação do Azure normalmente gerenciam recursos na nuvem do Azure.

Você pode editar um runbook para Runbook Worker Híbrido na Automação do Azure, mas pode ter problemas se tentar testá-lo no editor.  Os módulos do PowerShell que acessam os recursos locais podem não estar instalados em seu ambiente de Automação do Azure. Nesse caso, o teste falhará.  Se você instalar os módulos necessários, o runbook será executado, mas não será capaz de acessar recursos locais para um teste completo.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Iniciar um runbook no Hybrid Runbook Worker
[Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md) descreve métodos diferentes para se iniciar um runbook.  O Runbook Worker Híbrido adiciona uma opção **RunOn** em que você pode especificar o nome de um Grupo de Runbook Worker Híbrido.  Se um grupo for especificado, o runbook é recuperado e executado pelos trabalhadores nesse grupo.  Se essa opção não for especificada, ele é executado na Automação do Azure como de costume.

Ao iniciar um runbook no portal do Azure, a opção **Executar em** ficará disponível e você poderá selecionar **Azure** ou **Hybrid Worker**.  Se você selecionar **Worker Híbrido**, pode selecionar o grupo de uma lista suspensa.

Use o parâmetro **RunOn**.  Você pode usar o comando a seguir para iniciar um runbook denominado Runbook de Teste em um Grupo Hybrid Runbook Worker chamado MyHybridGroup usando o Windows PowerShell.

    Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"

> [!NOTE]
> O parâmetro **RunOn** foi adicionado ao cmdlet **Start-AzureAutomationRunbook** na versão 0.9.1 do Microsoft Azure PowerShell.  Você deve [baixar a versão mais recente](https://azure.microsoft.com/downloads/) se tiver uma anterior instalada.  Você só precisa instalar essa versão em uma estação de trabalho em que vá iniciar o runbook do Windows PowerShell.  Você não precisa instalá-lo no computador de trabalho, a menos que pretenda iniciar runbooks desse computador.  Atualmente, você não pode iniciar um runbook em um Runbook Worker Híbrido de outro runbook, pois isso exigiria que a versão mais recente do Powershell do Azure estivesse instalada em sua Conta de automação.  A versão mais recente será automaticamente atualizada na Automação do Azure e enviada aos trabalhadores em breve.
>
>

## <a name="runbook-permissions"></a>Permissões de runbook
Os runbooks em execução em um Hybrid Runbook Worker não podem usar o mesmo método que é normalmente usado para autenticação de runbooks nos recursos do Azure, já que eles acessarão recursos fora do Azure.  O runbook pode fornecer sua própria autenticação aos recursos locais, ou você pode especificar uma conta RunAs para fornecer um contexto de usuário a todos os runbooks.

### <a name="runbook-authentication"></a>Autenticação de runbook
Por padrão, os runbooks serão executados no contexto da conta de Sistema local no computador local; portanto, eles devem fornecer sua própria autenticação aos recursos que acessarão.  

Você pode usar ativos de [Credencial](http://msdn.microsoft.com/library/dn940015.aspx) e [Certificado](http://msdn.microsoft.com/library/dn940013.aspx) no runbook com cmdlets que permitem a especificação das credenciais para autenticar com recursos diferentes.  O exemplo a seguir mostra uma parte de um runbook que reinicia um computador.  Ele recupera as credenciais de um ativo de credencial e o nome do computador de um ativo variável, para então usar esses valores com o cmdlet Restart-Computer.

    $Cred = Get-AzureRmAutomationCredential -ResourceGroupName "ResourceGroup01" -Name "MyCredential"
    $Computer = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" -Name  "ComputerName"

    Restart-Computer -ComputerName $Computer -Credential $Cred

Você também pode aproveitar o [InlineScript](automation-powershell-workflow.md#inlinescript), que permitirá a execução de blocos de código em outro computador com as credenciais especificadas pelo [parâmetro comum PSCredential](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="runas-account"></a>Conta RunAs
Em vez de os runbooks fornecerem sua própria autenticação aos recursos locais, você pode especificar uma conta **RunAs** para um grupo Hybrid Worker.  Você especifica um [ativo de credencial](automation-credentials.md) que tenha acesso aos recursos locais e todos os runbooks serão executados sob essas credenciais ao serem executados em um Hybrid Runbook Worker no grupo.  

O nome de usuário da credencial deve estar em um dos seguintes formatos:

* domínio\nome de usuário
* username@domain
* nome de usuário (para contas locais do computador local)

Use o procedimento a seguir para especificar uma conta RunAs para um grupo do Hybrid Worker:

1. Crie um [ativo de credencial](automation-credentials.md) com acesso a recursos locais.
2. Abra a conta de Automação no Portal do Azure.
3. Escolha o bloco **Grupos do Hybrid Worker** e selecione o grupo.
4. Escolha **Todas as configurações** e **Configurações do grupo do Hybrid Worker**.
5. Altere **Executar como** de **Padrão** para **Personalizado**.
6. Escolha a credencial e clique em **Salvar**.

### <a name="automation-run-as-account"></a>Conta de automação Executar como
Como parte do processo de compilação automatizado para a implantação de recursos no Azure, você talvez precise acessar os sistemas no local para dar suporte a uma tarefa ou um conjunto de etapas na sequência de implantação.  Para dar suporte à autenticação no Azure usando a conta Executar como, você precisa instalar o certificado da conta Executar como.  

O runbook do PowerShell a seguir, *Export-RunAsCertificateToHybridWorker*, exporta o certificado de Executar como de sua conta de Automação do Azure e baixa e importa para o repositório de certificados do computador local em um Hybrid Worker conectado à mesma conta.  Quando essa etapa for concluída, ele verificará que o Worker pode ser autenticado com sucesso no Azure usando a conta Executar como.

    <#PSScriptInfo
    .VERSION 1.0
    .GUID 3a796b9a-623d-499d-86c8-c249f10a6986
    .AUTHOR Azure Automation Team
    .COMPANYNAME Microsoft
    .COPYRIGHT 
    .TAGS Azure Automation 
    .LICENSEURI 
    .PROJECTURI 
    .ICONURI 
    .EXTERNALMODULEDEPENDENCIES 
    .REQUIREDSCRIPTS 
    .EXTERNALSCRIPTDEPENDENCIES 
    .RELEASENOTES
    #>

    <#  
    .SYNOPSIS  
    Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. 
  
    .DESCRIPTION  
    This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
    Run this runbook in the hybrid worker where you want the certificate installed.
    This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

    .EXAMPLE
    .\Export-RunAsCertificateToHybridWorker

    .NOTES
    AUTHOR: Azure Automation Team 
    LASTEDIT: 2016.10.13
    #>

    [OutputType([string])] 

    # Set the password used for this certificate
    $Password = "YourStrongPasswordForTheCert"

    # Stop on errors
    $ErrorActionPreference = 'stop'

    # Get the management certificate that will be used to make calls into Azure Service Management resources
    $RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"
       
    # location to store temporary certificate in the Automation service host
    $CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"
   
    # Save the certificate
    $Cert = $RunAsCert.Export("pfx",$Password)
    Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose 

    Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
    $SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
    Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

    # Test that authentication to Azure Resource Manager is working
    $RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection" 
    
    Add-AzureRmAccount `
      -ServicePrincipal `
      -TenantId $RunAsConnection.TenantId `
      -ApplicationId $RunAsConnection.ApplicationId `
      -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

    Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

    # List automation accounts to confirm Azure Resource Manager calls are working
    Get-AzureRmAutomationAccount | Select AutomationAccountName

Salve o runbook *Export-RunAsCertificateToHybridWorker* no seu computador com uma extensão `.ps1`.  Importe-o para sua conta de Automação e edite o runbook, alterando o valor da variável `$Password` pela sua própria senha.  Publique e, em seguida, execute o runbook direcionando o grupo Hybrid Worker que executa e autentica runbooks usando a conta Executar como.  O fluxo de trabalho informa a tentativa de importar o certificado para o armazenamento do computador local e vem com várias linhas, dependendo de quantas contas de Automação são definidas em sua assinatura e se a autenticação tiver sido bem-sucedida.  

## <a name="troubleshooting-runbooks-on-hybrid-runbook-worker"></a>Solucionando problemas de runbooks no Hybrid Runbook Worker
Os logs são armazenados localmente em cada hybrid worker, em C:\ProgramData\Microsoft\System Center\Orchestrator\7.2\SMA\Sandboxes.  O Hybrid Worker também registra erros e eventos no log de eventos do Windows em **Logs de Aplicativos e Serviços\Microsoft-SMA\Operacional**.  Eventos relacionados a runbooks executados no worker são gravados em **Logs de Aplicativos e Serviços\Microsoft-Automation\Operacional**.  O log **Microsoft SMA** inclui muitos outros eventos relacionados ao trabalho de runbook enviado por push para o worker e ao processamento do runbook.  Embora o log de eventos de **Microsoft-Automation** não tenha muitos eventos com detalhes para ajudar na solução de problemas de execução de runbooks, você pelo menos encontrará os resultados do trabalho de runbook.  

[A saída e as mensagens do runbook](automation-runbook-output-and-messages.md) são enviadas à Automação do Azure do Hybrid Workers assim como os trabalhos do runbook são executados na nuvem.  Também é possível habilitar os fluxos Verbose e Progress da mesma forma que você faria para outros runbooks.  

Se seus runbooks não forem concluídos com êxito e o resumo do trabalho mostrar um status **Suspenso**, leia o artigo de solução de problemas [Hybrid Runbook Worker: um trabalho de runbook termina com o status Suspenso](automation-troubleshooting-hybrid-runbook-worker.md#a-runbook-job-terminates-with-a-status-of-suspended).   

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook, confira [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md).  
* Para entender os diferentes procedimentos para trabalhar com runbooks do PowerShell e de Fluxo de Trabalho do PowerShell na Automação do Azure usando o editor de texto, confira [Editar um Runbook na Automação do Azure](automation-edit-textual-runbook.md)