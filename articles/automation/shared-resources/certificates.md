---
title: Ativos de certificado na Automação do Azure
description: Os certificados são com segurança na automação do Azure para que possam ser acessados pelos runbooks ou configurações de DSC para serem autenticados em relação a recursos do Azure e produtos de terceiros.  Este artigo explica os detalhes de certificados e como trabalhar com elas na criação textual e gráfica.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d230fa97d009f0ee2a3bc86a0b6b7c8d40687a46
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61216011"
---
# <a name="certificate-assets-in-azure-automation"></a>Ativos de certificado na Automação do Azure

Certificados são armazenados com segurança na automação do Azure para que possam ser acessados pelos runbooks ou configurações DSC usando a **Get-AzureRmAutomationCertificate** atividade para recursos do Azure Resource Manager. Esse recurso permite criar runbooks e configurações DSC que usam certificados para autenticação ou adicioná-los a recursos do Azure ou de terceiros.

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação do Azure usando uma chave exclusiva que é gerada para cada conta de automação. Essa chave é armazenada em um Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a chave é carregada do Key Vault e usada para criptografar o ativo. Esse processo é gerenciado pela Automação do Azure.

## <a name="azurerm-powershell-cmdlets"></a>Cmdlets do AzureRM PowerShell

Para o AzureRM, os cmdlets na tabela a seguir são usados para criar e gerenciar ativos de credenciais de automação com o Windows PowerShell. Eles são fornecidos como parte dos [módulo Azurerm](/powershell/azure/overview), que está disponível para uso em runbooks de automação e configurações de DSC.

|Cmdlets|DESCRIÇÃO|
|:---|:---|
|[Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate)|Obtém informações sobre um certificado a ser usado em um runbook ou configuração DSC. Você só pode recuperar o certificado propriamente dito da atividade Get-AutomationCertificate.|
|[New-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/new-azurermautomationcertificate)|Cria um novo certificado para a Automação do Azure.|
[Remove-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/remove-azurermautomationcertificate)|Remove um certificado da Automação do Azure.|
|[Set-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/set-azurermautomationcertificate)|Define as propriedades para um certificado existente, incluindo o carregamento do arquivo de certificado e a definição da senha para um. pfx.|
|[Add-AzureCertificate](/powershell/module/servicemanagement/azure/add-azurecertificate)|Carrega um certificado de serviço para o serviço de nuvem especificado.|

## <a name="activities"></a>Atividades

As atividades na tabela a seguir são usadas para acessar certificados em um runbook e em configurações de DSC.

| Atividades | DESCRIÇÃO |
|:---|:---|
|Get-AutomationCertificate|Obtém um certificado a ser usado em um runbook ou configuração DSC. Retorna um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE] 
> Evite usar variáveis no parâmetro –Name de **Get-AutomationCertificate** em um runbook ou em uma configuração de DSC, pois isso complica a descoberta de dependências entre runbooks ou configurações de DSC e variáveis da Automação no tempo de design.

## <a name="python2-functions"></a>Funções Python2

A função na tabela a seguir é usada para acessar certificados em um runbook Python2.

| Função | DESCRIÇÃO |
|:---|:---|
| automationassets.get_automation_certificate | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> É necessário importar o módulo **automationassets** no início do runbook Python para acessar as funções do ativo.

## <a name="creating-a-new-certificate"></a>Criando um novo certificado

Ao criar um novo certificado, você carrega um arquivo .cer ou .pfx na Automação do Azure. Se marcar certificado como exportável, você poderá transferi-lo do repositório de certificados da Automação do Azure. Se não for exportável, ele pode apenas ser usado para a assinatura no runbook ou configuração DSC. A Automação do Azure exige que o certificado tenha o provedor: **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="to-create-a-new-certificate-with-the-azure-portal"></a>Para criar um novo certificado com o portal do Azure

1. Na sua conta de automação, clique o **ativos** bloco para abrir o **ativos** página.
2. Clique o **certificados** bloco para abrir o **certificados** página.
3. Clique em **adicionar um certificado** na parte superior da página.
4. Digite um nome para o certificado na caixa **Nome** .
5. Para procurar um arquivo .cer ou .pfx., clique em **Selecionar um arquivo** em **Carregar um arquivo de certificado**. Se você selecionar um arquivo. pfx, especifique uma senha e se ele pode ser exportado.
6. Clique em **Criar** para salvar o novo ativo de certificado.

### <a name="to-create-a-new-certificate-with-powershell"></a>Para criar um novo certificado com o PowerShell

O exemplo a seguir mostra como criar um novo Certificado de automação e marcá-lo como exportável. Isso importa um arquivo .pfx existente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzureRmAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-resource-manager-template"></a>Criar um novo certificado com o modelo do Resource Manager

O exemplo a seguir demonstra como implantar um certificado à sua conta de automação usando um modelo do Resource Manager por meio do PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>'
$AutomationAccountName = '<automation account name>'
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzureRmResourceGroupDeployment -Name NewCert -ResourceGroupName TestAzureAuto -TemplateFile .\template.json
```

## <a name="using-a-certificate"></a>Usando um certificado

Para usar um certificado, use a atividade **Get-AutomationCertificate**. Não é possível usar o [Get-AzureRmAutomationCertificate](/powershell/module/azurerm.automation/get-azurermautomationcertificate) cmdlet, pois ele retorna informações sobre o ativo de certificado, mas não o próprio certificado.

### <a name="textual-runbook-sample"></a>Exemplo de runbook textual

O código de exemplo a seguir mostra como adicionar um certificado a um serviço de nuvem em um runbook. Nesse exemplo, a senha é recuperada de uma variável de automação criptografada.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzureRmAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

### <a name="graphical-runbook-sample"></a>Exemplo de runbook gráfico

Você adiciona uma **Get-AutomationCertificate** a um runbook gráfico clicando duas vezes no certificado no painel Biblioteca e selecionando **adicionar à tela**.

![Adicionar certificado à tela](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem a seguir mostra um exemplo do uso de um certificado em um runbook gráfico. Isso é o mesmo que o exemplo anterior que mostra como adicionar um certificado para um serviço de nuvem de um runbook textual.

![Exemplo de criação gráfica](../media/certificates/graphical-runbook-add-certificate.png)

### <a name="python2-sample"></a>Exemplo de Python2

O exemplo a seguir mostra como acessar certificados em runbooks do Python2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre como trabalhar com links para controlar o fluxo lógico de atividades que o seu runbook é projetado para realizar, consulte [Links na criação gráfica](../automation-graphical-authoring-intro.md#links-and-workflow). 
