---
title: Gerenciar certificados na Automação do Azure
description: Este artigo fala de como trabalhar com certificados para acesso por runbooks e configurações de DSC.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: cbf9eb6c97dcceeca5e86e8bef47a39fb685792f
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734803"
---
# <a name="manage-certificates-in-azure-automation"></a>Gerenciar certificados na Automação do Azure

A Automação do Azure armazena certificados com segurança para acesso por runbooks e configurações de DSC. Ela faz isso usando o cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) para recursos do Azure Resource Manager. O armazenamento de certificado seguro permite criar runbooks e configurações de DSC que usam certificados para autenticação ou adicioná-los a recursos do Azure ou de terceiros.

>[!NOTE]
>Os ativos protegidos na Automação do Azure incluem credenciais, certificados, conexões e variáveis criptografadas. Esses ativos são criptografados e armazenados na Automação usando uma chave exclusiva que é gerada para cada conta da Automação do Azure. A Automação armazena a chave no serviço do Key Vault gerenciado pelo sistema. Antes de armazenar um ativo seguro, a Automação do Azure carrega a chave do Key Vault e depois a usa para criptografar o ativo.

## <a name="powershell-cmdlets-to-access-certificates"></a>Cmdlets do PowerShell para acessar certificados

Os cmdlets na tabela a seguir são usados para criar e gerenciar certificados de Automação com o PowerShell. Eles são fornecidos como parte dos [módulos AZ](modules.md#az-modules).

|Cmdlet |Descrição|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Obtém informações sobre um certificado a ser usado em um runbook ou configuração DSC. Só é possível recuperar o certificado em si usando o cmdlet `Get-AutomationCertificate` interno.|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Cria um novo certificado na Automação.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Remove um certificado da Automação.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Define as propriedades para um certificado existente, incluindo o carregamento do arquivo de certificado e a definição da senha para um arquivo **.pfx**.|

O cmdlet [Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) também pode ser usado para carregar um certificado de serviço para o serviço de nuvem especificado.

## <a name="internal-cmdlets-to-access-certificates"></a>Cmdlets internos para acessar certificados

O cmdlet interno na tabela a seguir é usado para acessar certificados em seus runbooks. Esse cmdlet vem com o módulo global`Orchestrator.AssetManagement.Cmdlets`. Para obter mais informações, confira [Cmdlets internos](modules.md#internal-cmdlets).

| Cmdlet Interno | Descrição |
|:---|:---|
|`Get-AutomationCertificate`|Obtém um certificado a ser usado em um runbook ou configuração DSC. Retorna um objeto [System.Security.Cryptography.X509Certificates.X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2).|

> [!NOTE]
> Evite usar variáveis no parâmetro `Name` de `Get-AutomationCertificate` em um runbook ou configuração DSC. Tais variáveis podem complicar a descoberta de dependências entre runbooks ou configurações DSC e variáveis de Automação no tempo de design.

## <a name="python-functions-to-access-certificates"></a>Funções do Python para acessar certificados

Use a função na tabela a seguir para acessar certificados em um runbook do Python 2 e 3. Atualmente, os runbooks do Python 3 estão em versão prévia.

| Função | Descrição |
|:---|:---|
| `automationassets.get_automation_certificate` | Recupera informações sobre um ativo de certificado. |

> [!NOTE]
> É necessário importar o módulo `automationassets`, no começo do runbook do Python para acessar as funções do ativo.

## <a name="create-a-new-certificate"></a>Criar um novo certificado

Ao criar um novo certificado, você carrega um arquivo .cer ou .pfx na Automação. Se marcar certificado como exportável, você poderá transferi-lo do repositório de certificados da Automação. Se não for exportável, ele só poderá ser usado para a assinatura no runbook ou na configuração DSC. A Automação exige que o certificado tenha o provedor **Microsoft Enhanced RSA e AES Cryptographic Provider**.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Criar um novo certificado com o portal do Azure

1. Na sua conta de automação, no painel esquerdo, selecione **certificados** em **recurso compartilhado**.
1. Na página **certificados** , selecione **Adicionar um certificado**.
1. No campo **Nome**, digite um nome para o certificado.
1. Para procurar um arquivo **.cer** ou **.pfx**, em **Carregar um arquivo de certificado**, escolha **Selecionar um arquivo**. Se você selecionar um arquivo **.pfx**, especifique uma senha e indique se ela pode ser exportada.
1. Selecione **Criar** para salvar o novo ativo de certificado.

### <a name="create-a-new-certificate-with-powershell"></a>Criar um novo certificado com o PowerShell

O exemplo a seguir mostra como criar um novo Certificado de automação e marcá-lo como exportável. Esse exemplo importa um arquivo **.pfx** existente.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Criar um novo certificado com um modelo do Resource Manager

O exemplo a seguir demonstra como implantar um certificado em sua conta da Automação através do uso de um modelo do Resource Manager por meio do PowerShell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
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
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Obter um certificado

Para recuperar um certificado, use o cmdlet `Get-AutomationCertificate` interno. Não é possível usar o cmdlet [Get-AzureAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate), porque ele retorna informações sobre o ativo de certificado, mas não sobre o próprio certificado.

### <a name="textual-runbook-examples"></a>Exemplos de runbook textual

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O exemplo a seguir mostra como adicionar um certificado a um serviço de nuvem em um runbook. Nesse exemplo, a senha é recuperada de uma variável de automação criptografada.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

O exemplo a seguir mostra como acessar certificados em runbooks do Python 2.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

O exemplo a seguir mostra como acessar certificados em runbooks do Python 3 (versão prévia).

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Exemplo de runbook gráfico

Adicione uma atividade do cmdlet `Get-AutomationCertificate` interno a um runbook gráfico. Para isso, clique com o botão direito do mouse no certificado no painel Biblioteca e selecione **Adicionar à tela**.

![Captura de tela mostrando a adição de um certificado à tela](../media/certificates/automation-certificate-add-to-canvas.png)

A imagem a seguir mostra um exemplo do uso de um certificado em um runbook gráfico.

![Captura de tela de um exemplo de criação gráfica](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre os cmdlets usados para acessar certificados, consulte [Gerenciar módulos na Automação do Azure](modules.md).
* Para obter informações gerais sobre runbooks, consulte [Execução de runbook na Automação do Azure](../automation-runbook-execution.md).
* Para obter detalhes sobre as configurações de DSC, consulte [visão geral do State Configuration da Automação do Azure](../automation-dsc-overview.md).
