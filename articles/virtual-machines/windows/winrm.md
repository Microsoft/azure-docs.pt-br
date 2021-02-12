---
title: Configurar o acesso do WinRM para uma VM do Azure
description: Configure o acesso do WinRM para uso com uma máquina virtual do Azure criada no modelo de implantação do Resource Manager.
author: mimckitt
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/16/2016
ms.author: mimckitt
ms.openlocfilehash: fbba14980083287c91fdd3defec78b229808fb15
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621681"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configurando o acesso do WinRM para as máquinas virtuais no Azure Resource Manager

Aqui estão as etapas que você precisa realizar para configurar uma VM com conectividade do WinRM

1. Criar um cofre de chaves
2. Criará um certificado autoassinado
3. Carregar seu certificado autoassinado no Cofre de Chaves
4. Obtenha a URL para seu certificado autoassinado no Cofre de Chaves
5. Referenciar a URL de seus certificados autoassinados ao criar uma VM

 

## <a name="step-1-create-a-key-vault"></a>Etapa 1: Criar um cofre de chaves
Você pode usar o comando abaixo para criar o Cofre de Chaves

```azurepowershell
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Etapa 2: Criar um certificado autoassinado
Você pode criar um certificado autoassinado usando esse script do PowerShell

```azurepowershell
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Etapa 3: Carregar seu certificado autoassinado no Cofre de Chaves
Antes de carregar o certificado para o Cofre de Chaves criado na Etapa 1, ele precisa ser convertido em um formato que o provedor de recursos Microsoft.Compute entenda. O script do PowerShell abaixo permitirá que você faça isso

```azurepowershell
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

[System.Collections.HashTable]$TableForJSON = @{
    "data"     = $filecontentencoded;
    "dataType" = "pfx";
    "password" = "<password>";
}
[System.String]$JSONObject = $TableForJSON | ConvertTo-Json

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Etapa 4: Obter a URL para seu certificado autoassinado no Cofre de Chaves
O provedor de recursos Microsoft.Compute precisa de uma URL para o segredo do Cofre de Chaves ao provisionar a VM. Isso permite que o provedor de recursos Microsoft.Compute baixe o segredo e crie o certificado equivalente na VM.

> [!NOTE]
> A URL do segredo precisa incluir a versão também. Uma URL de exemplo é semelhante a https: \/ /contosovault.Vault.Azure.net:443/Secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Modelos
Você pode obter o link para a URL no modelo usando o código abaixo

```json
"certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"
```

#### <a name="powershell"></a>PowerShell
Você pode obter essa URL usando o comando do PowerShell abaixo

```azurepowershell
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
```

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Etapa 5: Referenciar a URL de seus certificados autoassinados ao criar uma VM
#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Ao criar uma VM por meio de modelos, o certificado é referenciado na seção de segredos e na seção winRM como é apresentado abaixo:

```json
"osProfile": {
      ...
      "secrets": [
        {
          "sourceVault": {
            "id": "<resource id of the Key Vault containing the secret>"
          },
          "vaultCertificates": [
            {
              "certificateUrl": "<URL for the certificate you got in Step 4>",
              "certificateStore": "<Name of the certificate store on the VM>"
            }
          ]
        }
      ],
      "windowsConfiguration": {
        ...
        "winRM": {
          "listeners": [
            {
              "protocol": "http"
            },
            {
              "protocol": "https",
              "certificateUrl": "<URL for the certificate you got in Step 4>"
            }
          ]
        },
        ...
      }
    },
```

É possível encontrar um modelo de exemplo para o indicado acima em [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

O código-fonte para este modelo pode ser encontrado no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
```azurepowershell
$vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
$credential = Get-Credential
$secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
$vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -ProvisionVMAgent -WinRMCertificateUrl $secretURL
$sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
$CertificateStore = "My"
$vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL
```

## <a name="step-6-connecting-to-the-vm"></a>Etapa 6: Conectando-se à VM
Antes de poder se conectar à VM você precisará se certificar de que seu computador esteja configurado para o gerenciamento remoto do WinRM. Inicie o PowerShell como administrador e execute o comando abaixo para garantir que você esteja configurado.

```azurepowershell
Enable-PSRemoting -Force
```

> [!NOTE]
> Você precisará garantir que o serviço WinRM está em execução se o indicado acima não funcionar. Você pode fazer isso usando `Get-Service WinRM`
> 
> 

Quando a instalação estiver concluída, você poderá se conectar à VM usando o comando abaixo

```azurepowershell
Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
```
