---
title: Executar runbooks da Automação do Azure em um Hybrid Runbook Worker
description: Este artigo descreve como executar runbooks em computadores em seu datacenter local ou outro provedor de nuvem com o Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 6d1f504458aed440464015a34479d75992fe5c45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149368"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Executar runbooks em um Hybrid Runbook Worker

Os runbooks executados em um [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) normalmente gerenciam recursos no computador local ou em recursos no ambiente local em que o trabalho é implantado. Os runbooks na Automação do Azure normalmente gerenciam recursos na nuvem do Azure. Embora sejam usados de maneira diferente, os runbooks executados na Automação do Azure e os runbooks executados em um Hybrid Runbook Worker são idênticos na estrutura.

Ao criar runbooks para executar em um Hybrid Runbook Worker, é necessário editar e testar o runbook no computador que hospeda o trabalho. O computador host tem todos os módulos do PowerShell e o acesso à rede necessários para gerenciar os recursos locais. Após testar o runbook no computador do Hybrid Runbook Worker, você pode carregá-lo no ambiente da Automação do Azure, local em que ele pode ser executado no trabalho.

## <a name="plan-runbook-job-behavior"></a>Planejar o comportamento de trabalho do runbook

A automação do Azure lida com trabalhos em Hybrid runbook Workers de forma diferente dos trabalhos executados em áreas restritas do Azure. Se você tiver um runbook de execução longa, verifique se ele é resiliente a uma possível reinicialização. Para obter detalhes sobre o comportamento do trabalho, confira [Trabalhos do Hybrid Runbook Worker](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

Os trabalhos para Hybrid runbook Workers são executados sob a conta **sistema** local no Windows ou a conta **nxautomation** no Linux. Para o Linux, verifique se a conta **nxautomation** tem acesso ao local onde os módulos runbook estão armazenados. Ao usar o cmdlet [Install-Module](/powershell/module/powershellget/install-module), especifique AllUsers para o parâmetro `Scope` para garantir que a conta **nxautomation** tenha acesso. Para obter mais informações sobre o PowerShell no Linux, confira [Problemas conhecidos do PowerShell em plataformas que não são do Windows](/powershell/scripting/whats-new/what-s-new-in-powershell-70).

## <a name="configure-runbook-permissions"></a>Configurar permissões de runbook

Defina as permissões para que o runbook seja executado no Hybrid Runbook Worker das seguintes maneiras:

* Faça com que o runbook forneça sua autenticação para recursos locais.
* Configure a autenticação usando [identidades gerenciadas para recursos do Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Especifique uma conta Executar como para fornecer um contexto de usuário a todos os runbooks.

### <a name="use-runbook-authentication-to-local-resources"></a>Usar autenticação do runbook para recursos locais

Se estiver preparando um runbook que forneça a própria autenticação para recursos, use os ativos [credencial](./shared-resources/credentials.md) e [certificado](./shared-resources/certificates.md) em seu runbook. Há vários cmdlets que permitem especificar credenciais para que o runbook possa se autenticar em diferentes recursos. O exemplo a seguir mostra uma parte de um runbook que reinicia um computador. Ele recupera as credenciais de um ativo de credencial e o nome do computador de um ativo variável, para então usar esses valores com o cmdlet `Restart-Computer`.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Você também pode usar uma atividade [InlineScript](automation-powershell-workflow.md#use-inlinescript). A `InlineScript` permite executar blocos de código em outro computador com credenciais.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Usar autenticação de runbook com identidades gerenciadas

Os Hybrid Runbook Workers em máquinas virtuais do Azure podem usar identidades gerenciadas para autenticação em recursos do Azure. O uso de identidades gerenciadas para recursos do Azure em vez das contas Executar como oferece benefícios porque você não precisa:

* Exportar o certificado Executar como e, em seguida, importá-lo no Hybrid Runbook Worker.
* Renovar o certificado usado pela conta Executar como.
* Lidar com o objeto de conexão Executar como no código do runbook.

Siga as próximas etapas para usar uma identidade gerenciada para recursos do Azure em um Hybrid Runbook Worker:

1. Criar uma VM do Azure.
1. Configurar identidades gerenciadas para recursos do Azure na VM. Confira [Configurar identidades gerenciadas para recursos do Azure em uma VM usando o portal do Azure](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
1. Conceda à VM o acesso a um grupo de recursos no Resource Manager. Veja [Usar uma identidade gerenciada atribuída pelo sistema da VM do Windows para acessar o Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
1. Instale o Hybrid Runbook Worker na VM. Confira [Implantar um Hybrid Runbook Worker do Windows](automation-windows-hrw-install.md) ou [Implantar um Hybrid Runbook Worker do Linux](automation-linux-hrw-install.md).
1. Atualize o runbook para usar o cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) com o parâmetro `Identity` para autenticar em recursos do Azure. Essa configuração reduz a necessidade de usar uma conta Executar como e executar o gerenciamento de conta associado.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > O `Connect-AzAccount -Identity` funciona para um Hybrid Runbook Worker usando uma identidade atribuída pelo sistema e uma identidade atribuída pelo usuário. Se você usar várias identidades atribuídas pelo usuário no Hybrid Runbook Worker, seu runbook deverá especificar o parâmetro `AccountId` para `Connect-AzAccount` para selecionar uma identidade específica atribuída pelo usuário.

### <a name="use-runbook-authentication-with-run-as-account"></a>Usar autenticação de runbook com a conta Executar como

Em vez dos runbooks fornecerem a própria autenticação aos recursos locais, você pode especificar uma conta Executar como para um grupo do Hybrid Runbook Worker. Para especificar uma conta Executar como, você deve definir um [ativo de credencial](./shared-resources/credentials.md) que tenha acesso aos recursos locais. Esses recursos incluem repositórios de certificados e todos os runbooks são executados sob essas credenciais em um Hybrid Runbook Worker no grupo.

- O nome de usuário da credencial deve estar em um dos seguintes formatos:

   * domínio\nome de usuário
   * username@domain
   * nome de usuário (para contas locais do computador local)

- Para usar o runbook **Export-RunAsCertificateToHybridWorker** do PowerShell, você precisa instalar os módulos AZ para a automação do Azure no computador local.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Usar um ativo de credencial para especificar uma conta Executar como

Use o seguinte procedimento para especificar uma conta Executar como para um grupo do Hybrid Runbook Worker:

1. Crie um [ativo de credencial](./shared-resources/credentials.md) com acesso a recursos locais.
1. Abra a conta de Automação no Portal do Azure.
1. Selecione **Grupos de Hybrid Worker** e, em seguida, selecione o grupo específico.
1. Selecione **Todas as configurações** e, em seguida, **Configurações de grupo do Hybrid Worker**.
1. Altere o valor de **Executar Como** de **Padrão** para **Personalizado**.
1. Escolha a credencial e clique em **Salvar**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Instalar certificado da conta Executar como

Como parte do processo de build automatizado para a implantação de recursos no Azure, você pode precisar de acesso aos sistemas locais para dar suporte a uma tarefa ou um conjunto de etapas na sequência de implantação. Para fornecer autenticação no Azure usando a conta Executar como, você deve instalar o certificado da conta Executar como.

>[!NOTE]
>Esse runbook do PowerShell atualmente não é executado em computadores Linux. Ele é executado somente em computadores Windows.
>

O runbook do PowerShell a seguir, chamado **Export-RunAsCertificateToHybridWorker**, exporta o certificado Executar como da sua conta da Automação do Azure. O runbook baixa e importa o certificado para o repositório de certificados do computador local em um Hybrid Runbook Worker que esteja conectado à mesma conta. Quando concluir essa etapa, o runbook verificará se o trabalho pode se autenticar com êxito no Azure usando a conta Executar como.

>[!NOTE]
>Este runbook do PowerShell não foi projetado ou deve ser executado fora da sua conta de automação como um script no computador de destino.
>

```azurepowershell-interactive
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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

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

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Para runbooks do PowerShell, `Add-AzAccount` e `Add-AzureRMAccount` são aliases para `Connect-AzAccount`. Ao pesquisar os itens de biblioteca, se você não vir `Connect-AzAccount`, poderá usar `Add-AzAccount` ou poderá atualizar seus módulos na conta da Automação.

Para concluir a preparação da conta Executar como:

1. Salve o runbook **Export-RunAsCertificateToHybridWorker** no seu computador com uma extensão **.ps1**.
1. Importe-o para a conta da Automação.
1. Edite o runbook, alterando o valor da variável `Password` para a sua senha.
1. Publique o runbook.
1. Execute o runbook, direcionando ao grupo do Hybrid Runbook Worker que executa e autentica runbooks usando a conta Executar como. 
1. Examine o fluxo do trabalho para ver que ele informa a tentativa de importar o certificado no armazenamento do computador local, seguido por várias linhas. Esse comportamento depende de quantas contas da Automação você define em sua assinatura e do grau de sucesso da autenticação.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Trabalhar com runbooks assinados em um Hybrid Runbook Worker do Windows

Você pode configurar um Hybrid Runbook Worker do Windows para executar apenas runbooks assinados.

> [!IMPORTANT]
> Depois de configurar um Hybrid Runbook Worker para executar apenas runbooks assinados, os runbooks não assinados falharão ao serem executados no trabalho.

### <a name="create-signing-certificate"></a>Criar certificado de autenticação

O exemplo a seguir cria um certificado autoassinado que pode ser usado para assinar runbooks. Esse código cria o certificado e o exporta para que o Hybrid Runbook Worker possa importá-lo mais tarde. A impressão digital também é retornada para uso posterior na referência ao certificado.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
    -Subject "CN=contoso.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
    -KeyExportPolicy Exportable `
    -KeyUsage DigitalSignature `
    -Type CodeSigningCert

# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importar certificado e configurar trabalhos para validação de assinatura

Copie o certificado criado para cada Hybrid Runbook Worker em um grupo. Execute o script a seguir para importar o certificado e configure os trabalhos para usar a validação de assinatura em runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Assine os runbooks usando o certificado

Com os Hybrid Runbook Workers configurados para usar somente runbooks assinados, você deve assinar os runbooks que devem ser usados no Hybrid Runbook Worker. Use o código do PowerShell de exemplo a seguir para assinar esses runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando um runbook tiver sido assinado, você deverá importá-lo para sua conta da Automação e publicá-lo com o bloco de assinatura. Para saber como importar runbooks, confira [Importar um runbook](manage-runbooks.md#import-a-runbook).

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Trabalhar com runbooks assinados em um Hybrid Runbook Worker do Linux

Para poder trabalhar com runbooks assinados, um Hybrid Runbook Worker do Linux deve ter o [GPG](https://gnupg.org/index.html) executável no computador local.

> [!IMPORTANT]
> Depois de configurar um Hybrid Runbook Worker para executar apenas runbooks assinados, os runbooks não assinados falharão ao serem executados no trabalho.

Você executará as seguintes etapas para concluir esta configuração:

* Criar um token de autenticação e um par de chaves do GPG
* Disponibilizar o token de autenticação para o Hybrid Runbook Worker
* Verificar se a validação de assinatura está ativada
* Assinar um runbook

### <a name="create-a-gpg-keyring-and-keypair"></a>Criar um token de autenticação e um par de chaves do GPG

Para criar o token de autenticação e o par de chaves do GPG, use a conta [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux) do Hybrid Runbook Worker.

1. Use o aplicativo sudo para entrar com a conta **nxautomation**.

    ```bash
    sudo su – nxautomation
    ```

1. Quando estiver usando a **nxautomation**, gere o par de chaves do GPG. O GPG orienta você durante as etapas. Você deve fornecer nome, endereço de email, hora de expiração e frase secreta. Em seguida, você aguarda até que haja uma entropia suficiente no computador para que a chave seja gerada.

    ```bash
    sudo gpg --generate-key
    ```

1. Como o diretório do GPG foi gerado com sudo, você deve alterar seu proprietário para **nxautomation** usando o comando a seguir.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Disponibilizar o token de autenticação para o Hybrid Runbook Worker

Depois que o token de autenticação for criado, disponibilize-o para o Hybrid Runbook Worker. Modifique o arquivo de configurações **Home/nxautomation/State/Worker. conf** para incluir o seguinte código de exemplo na seção arquivo `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Verificar se a validação de assinatura está ativada

Se a validação de assinatura tiver sido desabilitada no computador, você deverá ativá-la executando o comando sudo a seguir. Substitua `<LogAnalyticsworkspaceId>` por sua ID do workspace.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Assinar um runbook

Depois de configurar a validação de assinatura, use o comando do GPG a seguir para assinar o runbook.

```bash
gpg –-clear-sign <runbook name>
```

O runbook assinado é chamado **<runbook name>.asc**.

Agora você pode carregar o runbook assinado na Automação do Azure e executá-lo como um runbook regular.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Iniciar um runbook em um Hybrid Runbook Worker

O artigo [Iniciar um runbook na Automação do Azure](start-runbooks.md) descreve diferentes métodos para se iniciar um runbook. Iniciar um runbook em um Hybrid Runbook Worker usa uma opção **executar em** que permite especificar o nome de um grupo de Hybrid runbook Worker. Quando um grupo é especificado, um dos trabalhos desse grupo recupera e executa o runbook. Se o runbook não especificar essa opção, a Automação do Azure executará o runbook como de costume.

Ao iniciar um runbook no portal do Azure, você verá a opção **Executar em** e poderá selecionar **Azure** ou **Hybrid Worker**. Se você selecionar **Hybrid Worker**, poderá escolher o grupo de Hybrid Runbook Worker em uma lista suspensa.

Ao iniciar um runbook usando o PowerShell, use o parâmetro `RunOn` com o cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). O exemplo a seguir usa o Windows PowerShell para iniciar um runbook denominado **Test-Runbook** em um grupo de Hybrid Runbook Worker chamado MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Registro em log

Para ajudar a solucionar problemas com seus runbooks em execução em um runbook Worker híbrido, os logs são armazenados localmente no seguinte local:

* No Windows em `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` para o log de processos detalhados do tempo de execução do trabalho. Eventos de status de trabalho de runbook de alto nível são gravados no log de eventos **Logs\Microsoft-Automation\Operations de aplicativos e serviços** .

* No Linux, os logs de trabalho híbrido do usuário podem ser encontrados em `/home/nxautomation/run/worker.log` , e os logs do runbook Worker do sistema podem ser encontrados em `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Próximas etapas

* Se os runbooks não estiverem sendo concluídos com êxito, examine o guia de solução de problemas em [falhas de execução de runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Para obter mais informações sobre o PowerShell, incluindo referência de linguagem e módulos de aprendizado, consulte [documentos do PowerShell](/powershell/scripting/overview).
* Para obter uma referência de cmdlet do PowerShell, confira [Az.Automation](/powershell/module/az.automation).
