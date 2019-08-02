---
title: Executar runbooks no Hybrid Runbook Worker da Automação do Azure
description: Este artigo fornece informações sobre a execução de runbooks em computadores em seu datacenter local ou provedor de nuvem com a função de Hybrid Runbook Worker.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 01/29/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 88e3c0514861da0bd11acffd26cced54717e4418
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478496"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Executar runbooks em um Hybrid Runbook Worker

Não há diferença na estrutura de runbooks executados na Automação do Azure daqueles que executam em um Hybrid Runbook Worker. Provavelmente, os runbooks usados em cada um serão bem diferentes. Essa diferença ocorre porque os runbooks direcionados a um Hybrid Runbook Worker normalmente gerenciam recursos no próprio computador local, ou com base em recursos no ambiente local onde ele é implantado. Os runbooks na Automação do Azure normalmente gerenciam recursos na nuvem do Azure.

Ao criar runbooks para executar em um Hybrid Runbook Worker, é necessário editar e testar os runbooks no computador que hospeda o Hybrid Worker. O computador host possui todos os módulos e acesso à rede do PowerShell necessários para gerenciar e acessar os recursos locais. Após um runbook ser testado no computador do Hybrid Worker, você poderá carregá-lo no ambiente de Automação do Azure, onde ficará disponível para execução no Hybrid Worker. É importante conhecer os trabalhos executados na conta Sistema Local para Windows ou em uma conta de usuário especial `nxautomation` no Linux. Esse comportamento pode introduzir diferenças sutis ao criar runbooks para um Hybrid Runbook Worker. Essas alterações devem ser examinadas durante a produção de seus runbooks.

## <a name="starting-a-runbook-on-hybrid-runbook-worker"></a>Iniciar um runbook no Hybrid Runbook Worker

[Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md) descreve métodos diferentes para se iniciar um runbook. O Runbook Worker Híbrido adiciona uma opção **RunOn** em que você pode especificar o nome de um Grupo de Runbook Worker Híbrido. Quando um grupo é especificado, o runbook é recuperado e executado por um dos trabalhadores nesse grupo. Se essa opção não for especificada, ele será executado na Automação do Azure como de costume.

Ao iniciar um runbook no portal do Azure, a opção **Executar em** ficará disponível e você poderá selecionar **Azure** ou **Hybrid Worker**. Se você selecionar **Worker Híbrido**, pode selecionar o grupo de uma lista suspensa.

Use o parâmetro **RunOn**. Você pode usar o comando a seguir para iniciar um runbook denominado Runbook de Teste em um Grupo Hybrid Runbook Worker chamado MyHybridGroup usando o Windows PowerShell.

```azurepowershell-interactive
Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> O parâmetro **RunOn** foi adicionado ao cmdlet **Start-AzureAutomationRunbook** na versão 0.9.1 do Microsoft Azure PowerShell. Você deve [baixar a versão mais recente](https://azure.microsoft.com/downloads/) se tiver uma anterior instalada. Você só precisa instalar essa versão em uma estação de trabalho em que vá iniciar o runbook do PowerShell. Você não precisa instalá-lo no computador de trabalho, a menos que pretenda iniciar runbooks desse computador"

## <a name="runbook-permissions"></a>Permissões de runbook

Os runbooks em execução em um Hybrid Runbook Worker não podem usar o mesmo método normalmente usado para autenticação de runbooks nos recursos do Azure, já que eles acessam recursos fora do Azure. O runbook pode fornecer sua própria autenticação aos recursos locais, ou pode configurar a autenticação usando [entidades gerenciadas para os recursos do Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager
). Você também pode especificar uma conta RunAs para fornecer um contexto de usuário a todos os runbooks.

### <a name="runbook-authentication"></a>Autenticação de runbook

Por padrão, os runbooks são executados no contexto da conta de Sistema Local para Windows e uma conta de usuário especial `nxautomation` para Linux no computador local, portanto, eles devem fornecer sua própria autenticação aos recursos que acessam.

Você pode usar ativos de [Credencial](automation-credentials.md) e [Certificado](automation-certificates.md) no runbook com cmdlets que permitem a especificação das credenciais para autenticar com recursos diferentes. O exemplo a seguir mostra uma parte de um runbook que reinicia um computador. Ele recupera as credenciais de um ativo de credencial e o nome do computador de um ativo variável, para então usar esses valores com o cmdlet Restart-Computer.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Você também pode usar o [InlineScript](automation-powershell-workflow.md#inlinescript), que permite a execução de blocos de código em outro computador com as credenciais especificadas pelo [parâmetro comum PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="runas-account"></a>Conta RunAs

Por padrão o Hybrid Runbook Worker usa Sistema Local para Windows e uma conta de usuário especial `nxautomation` para Linux para executar runbooks. Em vez de os runbooks fornecerem sua própria autenticação aos recursos locais, você pode especificar uma conta **RunAs** para um grupo Hybrid Worker. Você especifica um [ativo de credencial](automation-credentials.md) que tenha acesso aos recursos locais e todos os runbooks serão executados sob essas credenciais ao serem executados em um Hybrid Runbook Worker no grupo.

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

### <a name="managed-identities-for-azure-resources"></a>Identidades gerenciadas para recursos do Azure

Hybrid Runbook Workers em execução em máquinas virtuais do Azure podem usar identidades gerenciadas para recursos do Azure para autenticação para recursos do Azure. Alguns dos benefícios do uso de identidades gerenciadas para recursos do Azure em contas Executar como.

* Não é preciso exportar o certificado Executar como e, em seguida, importá-lo para o Hybrid Runbook Worker
* Não há necessidade de renovar o certificado usado pela conta Executar como
* Não é preciso lidar com o objeto de conexão Executar como no código do runbook

Para usar uma identidade gerenciada para recursos do Azure em um Hybrid Runbook Worker, você precisará concluir as etapas a seguir:

1. Criar uma VM do Azure
2. [Configurar identidades gerenciadas para recursos do Azure em sua VM](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. [Conceder acesso a um grupo de recursos no Resource Manager para sua VM](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
4. [Obter um token de acesso usando a identidade gerenciada atribuída pelo sistema da VM](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#get-an-access-token-using-the-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager)
5. [Instalar o Windows Hybrid Runbook Worker](automation-windows-hrw-install.md#installing-the-windows-hybrid-runbook-worker) na máquina virtual.

Depois que as etapas anteriores forem concluídas, você poderá usar `Connect-AzureRmAccount -Identity` no runbook para autenticação nos recursos do Azure. Essa configuração reduz a necessidade de utilizar uma conta Executar como e gerenciar o certificado para a conta Executar como.

```powershell
# Connect to Azure using the Managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
Connect-AzureRmAccount -Identity

# Get all VM names from the subscription
Get-AzureRmVm | Select Name
```

### <a name="runas-script"></a>Conta de automação Executar como

Como parte do processo de build automatizado para a implantação de recursos no Azure, talvez você precise acessar os sistemas locais para dar suporte a uma tarefa ou um conjunto de etapas na sequência de implantação. Para dar suporte à autenticação no Azure usando a conta Executar como, você precisa instalar o certificado da conta Executar como.

O runbook do PowerShell a seguir, **Export-RunAsCertificateToHybridWorker**, exporta o certificado de Executar como de sua conta de Automação do Azure e baixa e importa para o repositório de certificados do computador local em um Hybrid Worker conectado à mesma conta. Quando essa etapa for concluída, ele verificará que o Worker pode ser autenticado com sucesso no Azure usando a conta Executar como.

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
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.
Run this runbook in the hybrid worker where you want the certificate installed.
This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running in the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
AUTHOR: Azure Automation Team
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

# Test that authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzureRmContext -SubscriptionId $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm Azure Resource Manager calls are working
Get-AzureRmAutomationAccount | Select-Object AutomationAccountName
```

> [!IMPORTANT]
> **Connect-AzureRmAccount** agora é um alias para **Connect-AzureRMAccount**. Ao pesquisar sua biblioteca de itens, se você não vir **Connect-AzureRMAccount**, você pode usar **Connect-AzureRmAccount**, ou você pode atualizar seus módulos em sua Conta de Automação.

Salve o runbook *Export-RunAsCertificateToHybridWorker* no seu computador com uma extensão `.ps1`. Importe-o para sua conta de Automação e edite o runbook, alterando o valor da variável `$Password` pela sua própria senha. Publique e execute o runbook. Direcione o grupo Hybrid Worker que executa e autentica runbooks usando a conta Executar como. O fluxo de trabalho informa a tentativa de importar o certificado no armazenamento do computador local e mostra várias linhas em seguida. Esse comportamento depende de quantas contas de Automação você define em sua assinatura, e do sucesso da autenticação.

## <a name="job-behavior"></a>Comportamento do trabalho

Os trabalhos são tratados de forma ligeiramente diferente em Hybrid Runbook Workers do que quando são executados em áreas restritas do Azure. Uma diferença fundamental é que não há limite na duração do trabalho em Hybrid Runbook Workers. Os runbooks executados em áreas restritas do Azure são limitados a 3 horas devido à [fração justa](automation-runbook-execution.md#fair-share). No caso de um runbook de execução longa, convém verificar se ele é resiliente a uma possível reinicialização. Por exemplo, se o computador que hospeda o Hybrid Worker for reinicializado. Se o computador host do Hybrid Worker reiniciar, qualquer trabalho de runbook em execução será reiniciado desde o início ou do último ponto de verificação para runbooks do Fluxo de trabalho do PowerShell. Se um trabalho do runbook for reiniciado mais de três vezes, será suspenso.

## <a name="run-only-signed-runbooks"></a>Executar somente runbooks assinados

Os Hybrid Runbook Workers podem ser configurados para executar somente runbooks assinados com alguma configuração. A seção a seguir descreve como configurar os Hybrid Runbook Workers para executar o [Hybrid Runbook Worker do Windows](#windows-hybrid-runbook-worker) e o [Hybrid Runbook Worker do Linux](#linux-hybrid-runbook-worker) assinados

> [!NOTE]
> Após configurar um Hybrid Runbook Worker para executar somente runbooks assinados, os runbooks que **não** foram assinados não serão executados no trabalho.

### <a name="windows-hybrid-runbook-worker"></a>Hybrid Runbook Worker do Windows

#### <a name="create-signing-certificate"></a>Criar certificado de assinatura

O exemplo a seguir cria um certificado autoassinado que pode ser usado para assinar runbooks. O exemplo cria o certificado e o exporta. O certificado será importado para os Hybrid Runbook Workers posteriormente. A impressão digital também retorna, e será utilizada posteriormente para referenciar o certificado.

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

#### <a name="configure-the-hybrid-runbook-workers"></a>Configurar os Hybrid Runbook Workers

Copie o certificado criado para cada Hybrid Runbook Worker em um grupo. Execute o script a seguir para importar o certificado e configure o Hybrid Worker para usar a validação de assinatura em runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

#### <a name="sign-your-runbooks-using-the-certificate"></a>Assine os Runbooks usando o certificado

Com o Hybrid Runbook workers configurados para usar somente assinado runbooks, você deve entrar runbooks que devem ser usados no Hybrid Runbook Worker. Use o exemplo de PowerShell a seguir para assinar os runbooks.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Quando o runbook tiver sido assinado, ele deve ser importado para sua conta de automação e publicado com o bloco de assinatura. Para saber como importar os runbooks, consulte [Importando um runbook de um arquivo para a Automação do Azure](manage-runbooks.md#import-a-runbook).

### <a name="linux-hybrid-runbook-worker"></a>Hybrid Runbook Worker do Linux

Para assinar os runbooks em um Hybrid Runbook Worker do Linux, o Hybrid Runbook Worker precisa ter o [GPG](https://gnupg.org/index.html) executável presente no computador.

#### <a name="create-a-gpg-keyring-and-keypair"></a>Criar um token de autenticação e um par de chaves do GPG

Para criar o token de autenticação e o par de chaves, você precisará usar a conta do Hybrid Runbook Worker `nxautomation`.

Use `sudo` para fazer logon como a conta `nxautomation`.

```bash
sudo su – nxautomation
```

Uma vez usando a conta `nxautomation`, gere o par de chaves do GPG.

```bash
sudo gpg --generate-key
```

O GPG guiará você pelas etapas para criar o par de chaves. Você precisará fornecer um nome, um endereço de email, uma data de validade, uma frase secreta e aguardar entropia suficiente no computador para a chave ser gerada.

Como o diretório do GPG foi gerado com sudo, você precisará alterar seu proprietário para `nxautomation`. 

Execute o seguinte comando para alterar o proprietário.

```bash
sudo chown -R nxautomation ~/.gnupg
```

#### <a name="make-the-keyring-available-the-hybrid-runbook-worker"></a>Disponibilizar o token de autenticação para o Hybrid Runbook Worker

Depois de criar o token de autenticação, você precisará disponibilizá-lo para o Hybrid Runbook Worker. Modifique o arquivo de configurações `/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf` para incluir o exemplo a seguir na seção `[worker-optional]`

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

#### <a name="verify-signature-validation-is-on"></a>Verificar se a validação de assinatura está ativada

Se a validação de assinatura tiver sido desabilitada no computador, você precisará ativá-la. Execute o seguinte comando para habilitar a validação de assinatura. Substituindo `<LogAnalyticsworkspaceId>` por sua ID do workspace.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

#### <a name="sign-a-runbook"></a>Assinar um runbook

Após configurar a validação de assinatura, você poderá usar o comando a seguir para assinar um runbook:

```bash
gpg –-clear-sign <runbook name>
```

O runbook assinado terá o nome `<runbook name>.asc`.

O runbook assinado agora pode ser carregado na Automação do Azure e pode ser executado como um runbook normal.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os diferentes métodos que podem ser usados para iniciar um runbook, confira [Como iniciar um Runbook na Automação do Azure](automation-starting-a-runbook.md).
* Para entender as várias maneiras de trabalhar com runbooks do PowerShell na Automação do Azure usando o editor de texto, confira [Editar um runbook na Automação do Azure](automation-edit-textual-runbook.md)
* Se os runbooks não estiverem sendo concluídos com êxito, revise o guia de solução de problemas em [falhas de execução do runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).