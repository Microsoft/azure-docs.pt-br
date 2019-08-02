---
title: Configurar o acesso do WinRM para uma VM do Azure | Microsoft Docs
description: Configure o acesso do WinRM para uso com uma máquina virtual do Azure criada no modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 2a106c639eb72d3793b0df8f4ddf36a4724f7418
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707872"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Configurando o acesso do WinRM para as máquinas virtuais no Azure Resource Manager

Aqui estão as etapas que você precisa realizar para configurar uma VM com conectividade do WinRM

1. Criar um cofre de chaves
2. Crie um certificado autoassinado
3. Carregar seu certificado autoassinado no Cofre de Chaves
4. Obtenha a URL para seu certificado autoassinado no Cofre de Chaves
5. Referenciar a URL de seus certificados autoassinados ao criar uma VM

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-create-a-key-vault"></a>Etapa 1: Criar um cofre de chaves
Você pode usar o comando abaixo para criar o Cofre de Chaves

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Etapa 2: Crie um certificado autoassinado
Você pode criar um certificado autoassinado usando esse script do PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Etapa 3: Carregar seu certificado autoassinado no Cofre de Chaves
Antes de carregar o certificado para o Cofre de Chaves criado na Etapa 1, ele precisa ser convertido em um formato que o provedor de recursos Microsoft.Compute entenda. O script do PowerShell abaixo permitirá que você faça isso

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Etapa 4: Obtenha a URL para seu certificado autoassinado no Cofre de Chaves
O provedor de recursos Microsoft.Compute precisa de uma URL para o segredo do Cofre de Chaves ao provisionar a VM. Isso permite que o provedor de recursos Microsoft.Compute baixe o segredo e crie o certificado equivalente na VM.

> [!NOTE]
> A URL do segredo precisa incluir a versão também. Um exemplo de URL a aparência abaixo https:\//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Modelos
Você pode obter o link para a URL no modelo usando o código abaixo

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Você pode obter essa URL usando o comando do PowerShell abaixo

    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Etapa 5: Referenciar a URL de seus certificados autoassinados ao criar uma VM
#### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager
Ao criar uma VM por meio de modelos, o certificado é referenciado na seção de segredos e na seção winRM como é apresentado abaixo:

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

É possível encontrar um modelo de exemplo para o indicado acima em [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

O código-fonte para este modelo pode ser encontrado no [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Etapa 6: Conectando-se à VM
Antes de poder se conectar à VM você precisará se certificar de que seu computador esteja configurado para o gerenciamento remoto do WinRM. Inicie o PowerShell como administrador e execute o comando abaixo para garantir que você esteja configurado.

    Enable-PSRemoting -Force

> [!NOTE]
> Você precisará garantir que o serviço WinRM está em execução se o indicado acima não funcionar. Você pode fazer isso usando `Get-Service WinRM`
> 
> 

Quando a instalação estiver concluída, você poderá se conectar à VM usando o comando abaixo

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
