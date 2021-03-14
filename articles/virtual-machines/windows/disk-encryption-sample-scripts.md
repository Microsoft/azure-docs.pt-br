---
title: Azure Disk Encryption scripts de exemplo para VMs do Windows
description: Este artigo é o apêndice para Microsoft Azure a criptografia de disco para VMs do Windows.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 413553165fcf74fa4590cb4661212b885a277579
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550630"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption scripts de exemplo 

Este artigo fornece scripts de exemplo para a preparação de VHDs e outras tarefas criptografados previamente.

> [!NOTE]
> Todos os scripts referem-se à versão mais recente e não AAD do ADE, exceto quando indicado.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Exemplos de scripts do PowerShell para Azure Disk Encryption 


- **Listar todas as VMs criptografadas na assinatura**

  Você pode encontrar todas as VMs com criptografia de ADE e a versão de extensão, em todos os grupos de recursos presentes em uma assinatura, usando [este script do PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1).

  Como alternativa, esses cmdlets mostrarão todas as VMs com criptografia de ADE (mas não a versão de extensão):

    ```azurepowershell-interactive
    $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
    $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
    Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
    ```

- **Listar todas as instâncias VMSS criptografadas em sua assinatura**
    
    Você pode encontrar todas as instâncias de VMSS criptografadas por ADE e a versão de extensão, em todos os grupos de recursos presentes em uma assinatura, usando [este script do PowerShell](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1).
 
- **Listar todos os segredos de criptografia de disco usados para criptografar VMs em um cofre de chaves**

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a> Usando o script do PowerShell de pré-requisitos do Azure Disk Encryption

Se você já estiver familiarizado com os pré-requisitos do Azure Disk Encryption, use o [script do PowerShell de pré-requisitos do Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para obter um exemplo de como usar esse script do PowerShell, confira o [Guia de início rápido para criptografar uma VM](disk-encryption-powershell-quickstart.md). Você pode remover os comentários de uma seção do script, começando na linha 211, para criptografar todos os discos de VMs existentes em um grupo de recursos existente. 

A tabela a seguir mostra quais parâmetros podem ser usados no script do PowerShell: 

|Parâmetro|Descrição|Obrigatório?|
|------|------|------|
|$resourceGroupName| Nome do grupo de recursos ao qual o KeyVault pertence.  Um grupo de recursos com esse nome será criado caso ele ainda não exista.| True|
|$keyVaultName|Nome do KeyVault no qual as chaves de criptografia devem ser colocadas. Um cofre com esse nome será criado caso ele ainda não exista.| True|
|$location|Local do KeyVault. Verifique se o KeyVault e as VMs a serem criptografadas estão no mesmo local. Obtenha uma lista de locais com `Get-AzLocation`.|True|
|$subscriptionId|Identificador da assinatura do Azure a ser usada.  Você pode obter sua ID de assinatura com `Get-AzSubscription`.|True|
|$aadAppName|Nome do aplicativo do Azure AD que será usado para gravar segredos no KeyVault. Será criado um novo aplicativo com esse nome caso ele não exista. Se esse aplicativo já existir, passe o parâmetro aadClientSecret para o script.|Falso|
|$aadClientSecret|Segredo do cliente do aplicativo do Azure AD que já foi criado.|Falso|
|$keyEncryptionKeyName|Nome da chave de criptografia da chave opcional no KeyVault. Uma chave com esse nome será criada caso ela ainda não exista.|Falso|

## <a name="resource-manager-templates"></a>Modelos do Gerenciador de Recursos

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Criptografar ou descriptografar VMs sem um aplicativo do Azure AD

- [Habilitar a criptografia de disco em uma VM do Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)  
- [Desabilitar a criptografia em uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) 

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Criptografar ou descriptografar VMs com um aplicativo do Azure AD (versão anterior) 
 
- [Habilitar a criptografia de disco em uma VM do Windows existente ou em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)    
- [Desabilitar a criptografia em uma VM do Windows em execução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 
- [Criar um novo disco gerenciado criptografado a partir de um blob de armazenamento/VHD previamente criptografado](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Cria um disco gerenciado criptografado fornecido usando um VHD pré-criptografado e as configurações de criptografia correspondentes

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>Preparar um VHD do Windows previamente criptografado
As seções a seguir são necessárias para preparar um VHD do Windows previamente criptografado para implantação como um VHD criptografado no Azure IaaS. Use as informações para preparar e inicializar uma nova VHD (VM do Windows) no Azure Site Recovery ou no Azure. Para obter mais informações sobre como preparar e carregar um VHD, consulte [Carregar um VHD generalizado e usá-lo para criar novas VMs no Azure](upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Atualizar a política de grupo para permitir não TPM na proteção do sistema operacional
Configure a política de grupo do BitLocker **Criptografia de Unidade de Disco BitLocker**, que você encontrará em **Política de Computador Local** > **Configuração do Computador** > **Modelos Administrativos** > **Componentes do Windows**. Alterar essa configuração para **unidades do sistema operacional**  >  **exigir autenticação adicional na inicialização**  >  **permitir BitLocker sem um TPM compatível**, conforme mostrado na figura a seguir:

![Microsoft Antimalware no Azure](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalar componentes de recursos do BitLocker
Para o Windows Server 2012 e versões posteriores, use o seguinte comando:

```console
dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart
```

Para o Windows Server 2008 R2, use o seguinte comando:

```console
ServerManagerCmd -install BitLockers
```

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Preparar o volume do sistema operacional para o BitLocker usando `bdehdcfg`
Para compactar a partição do SO e preparar o computador para BitLocker, execute o [bdehdcfg](/windows/security/information-protection/bitlocker/bitlocker-basic-deployment), se necessário:

```console
bdehdcfg -target c: shrink -quiet 
```

### <a name="protect-the-os-volume-by-using-bitlocker"></a>Proteger o volume do sistema operacional usando o BitLocker
Use o [`manage-bde`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/ff829849(v=ws.11)) comando para habilitar a criptografia no volume de inicialização usando um protetor de chave externa. Também coloque a chave externa (arquivo .bek) na unidade ou no volume. A criptografia é habilitada no volume de inicialização/sistema após a próxima reinicialização.

```console
manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
reboot
```

> [!NOTE]
> Prepare a VM com um VHD de dados/recursos separado para obter a chave externa usando o BitLocker.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Carregue o VHD criptografado para uma conta de armazenamento do Azure
Depois que a criptografia de DM-Crypt estiver habilitada, o VHD criptografado local precisará ser carregado em sua conta de armazenamento.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a> Carregar o segredo da VM previamente criptografada no cofre de chaves
O segredo de criptografia de disco que você obteve anteriormente deve ser carregado como um segredo no cofre de chaves.  Isso requer a concessão da permissão Set Secret e a permissão wrapkey para a conta que carregará os segredos.

```powershell 
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly 
# $upn = "user@domain.com" 
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that 
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Segredo de criptografia de disco não criptografado com uma KEK
Para configurar o segredo em seu cofre de chaves, use [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). A frase secreta é codificada como uma cadeia de caracteres Base64 e, em seguida, carregada no cofre de chaves. Além disso, verifique se as seguintes marcas estão definidas ao criar o segredo no cofre de chaves.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Use o `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional sem usar KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Segredo de criptografia de disco criptografado com uma KEK
Antes de carregar o segredo no cofre de chaves, opcionalmente, você pode criptografá-lo usando uma chave de criptografia de chave. Use a [API](/rest/api/keyvault/wrapkey) de encapsulamento para primeiro criptografar o segredo usando a chave de criptografia de chave. A saída dessa operação de encapsulamento é uma cadeia de caracteres codificada de URL base64, que você pode carregar como um segredo usando o [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Use `$KeyEncryptionKey` e `$secretUrl` na próxima etapa para [anexar o disco do sistema operacional usando KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Especificar uma URL secreta ao anexar um disco do sistema operacional

###  <a name="without-using-a-kek"></a>Sem usar uma KEK
Enquanto você está anexando o disco do sistema operacional, é necessário passar `$secretUrl`. A URL foi gerada na seção "Segredo de criptografia de disco não criptografado com uma KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>Usando uma KEK
Ao anexar o disco do sistema operacional, passe `$KeyEncryptionKey` e `$secretUrl`. A URL foi gerada na seção "Segredo de criptografia de disco criptografado com KEK".
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
