---
title: Solucionar problemas de erros de inicialização do BitLocker em uma VM do Azure| Microsoft Docs
description: Aprenda a solucionar problemas de erros de inicialização do BitLocker em uma VM do Azure
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: 87bf311b5199ec187c24c28a42314d9dc6787998
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633020"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Erros de inicialização do BitLocker em uma VM do Azure

 Este artigo descreve os erros do BitLocker que você pode enfrentar ao iniciar uma VM (máquina virtual) do Windows no Microsoft Azure.

## <a name="symptom"></a>Sintoma

 Uma VM do Windows não inicia. Ao verificar as capturas de tela na janela [Diagnóstico de inicialização ](./boot-diagnostics.md), você verá uma das seguintes mensagens de erro:

- Conectar driver USB que tem a chave do BitLocker

- Você está bloqueado! Insira a chave de recuperação para começar novamente (Layout do teclado: EUA) As informações de entrada incorretas foram inseridas muitas vezes, portanto, o computador foi bloqueado para proteger sua privacidade. Para recuperar a chave de recuperação, vá para https://windows.microsoft.com/recoverykeyfaq em outro PC ou dispositivo móvel. Caso seja necessária, a ID da chave é XXXXXXX. Ou, você pode reiniciar o computador.

- Insira a senha para desbloquear esta unidade [ ] Pressione a tecla Insert para ver a senha enquanto você digita.
- Insira a chave de recuperação para Carregar a chave de recuperação de um dispositivo USB.

## <a name="cause"></a>Causa

Esse problema poderá ocorrer se a VM não puder localizar o arquivo BEK (Chave de Recuperação do BitLocker) para descriptografar o disco criptografado.

## <a name="solution"></a>Solução

> [!TIP]
> Se você tiver um backup recente da VM, poderá tentar [restaurar a VM do backup](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema de inicialização.

Para resolver esse problema, pare e desaloque a VM e, em seguida, inicie-a. Essa operação força a VM a recuperar o arquivo BEK do Azure Key Vault e, em seguida, coloca-o no disco criptografado. 

Se esse método não resolver o problema, siga estas etapas para restaurar o arquivo BEK manualmente:

1. Tire um instantâneo do disco do sistema da VM afetada como um backup. Para obter mais informações, consulte [Instantâneo de um disco](../windows/snapshot-copy-managed-disk.md).
2. [Anexar o disco do sistema para uma VM de recuperação](troubleshoot-recovery-disks-portal-windows.md). Para executar o comando [Manage-bde](/windows-server/administration/windows-commands/manage-bde) na etapa 7, o recurso **criptografia de unidade de disco BitLocker** deve ser habilitado na VM de recuperação.

    Ao anexar um disco gerenciado, você poderá receber uma mensagem de erro "contém configurações de criptografia e, portanto, não pode ser usado como um disco de dados". Nessa situação, execute o script a seguir para tentar anexar novamente o disco:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"
    # Set the EncryptionSettingsEnabled property to false, so you can attach the disk to the recovery VM.
    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Não é possível anexar um disco gerenciado a uma VM que foi restaurada a partir de uma imagem de blob.

3. Depois que o disco estiver anexado, faça uma conexão de área de trabalho remota com a VM de recuperação para poder executar alguns scripts do Azure PowerShell. Certifique-se de que a [última versão do Azure PowerShell](/powershell/azure/) está instalada na VM de recuperação.

4. Abra uma sessão privilegiada do Azure PowerShell (Executar como administrador). Execute os comandos a seguir para entrar na assinatura do Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Execute o script a seguir para verificar o nome do arquivo BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="MachineName"; Expression = {$_.Tags.MachineName}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    A seguir, um exemplo da saída. Nesse caso, presumimos que o nome do arquivo seja EF7B2F5A-50C6-4637-0001-7F599C12F85C. Bek.

    ```
    Created               Content Type Volume MachineName DiskEncryptionKeyFileName
    -------               ------------ ------ ----------- -------------------------
    11/20/2020 7:41:56 AM BEK          C:\    myVM   EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Se você visualizar dois volumes duplicados, o volume que tiver o carimbo de data/hora mais recente será o arquivo BEK atual usado pela VM de recuperação.

    Se o valor do **Tipo de Conteúdo** for **BEK Encapsulado**, vá para os [cenários de KEK (Chave de Criptografia de Chave)](#key-encryption-key-scenario).

    Agora que você tem o nome do arquivo BEK para a unidade, é necessário criar o arquivo secret-file-name.BEK para desbloquear a unidade.

6.  Baixe o arquivo BEK para o disco de recuperação. O exemplo a seguir salva o arquivo BEK na pasta C:\BEK. Antes de executar os scripts, certifique-se de que o caminho `C:\BEK\` existe.

    ```powershell
    $vault = "myKeyVault"
    $bek = "EF7B2F5A-50C6-4637-0001-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $bekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Para desbloquear o disco anexado usando o arquivo BEK, execute o comando a seguir.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Neste exemplo, o disco do SO anexado é a unidade F. Certifique-se de usar a letra da unidade correta. 

8. Depois que o disco for desbloqueado com êxito usando a chave BEK, desanexe o disco da VM de recuperação e recrie a VM usando esse novo disco do sistema operacional.

    > [!NOTE]
    > Não há suporte para alternar o disco do sistema operacional para VMs que usam criptografia de disco.

9. Se a nova VM ainda não puder ser inicializada normalmente, tente uma das etapas a seguir depois de desbloquear a unidade:

    - Suspenda a proteção para desativar temporariamente o BitLocker executando o seguinte:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Descriptografar completamente a unidade. Para fazer isso, execute o seguinte comando:

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Cenário de Chave de Criptografia de Chave

Para um cenário de Chave de Criptografia de Chave, siga estas etapas:

1. Certifique-se de que a conta do usuário que efetuou logon exija a permissão "decodificado" nas políticas de Acesso ao Cofre de Chaves **USUÁRIO|Permissões de chaves|Operações de Criptografia|Decodificar Chave**.
2. Salve o script a seguir em um. Arquivo PS1:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    If ((Test-Path -Path $adal) -and (Test-Path -Path $adalforms)) { 

    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }
     else
     {
    $adal="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms ="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }  

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $wrappedBekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)

    #Delete the key from the memory
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($bstr)
    clear-variable -name wrappedBekSecretBase64
    ```
3. Defina os parâmetros. O script processará o segredo KEK para criar a chave BEK e, em seguida, salvará em uma pasta local na VM de recuperação. Se você receber erros ao executar o script, consulte a seção [solução de problemas de script](#script-troubleshooting) .

4. Quando o script começar, você verá a seguinte saída:

    Local da versão do GAC                                                                              
    ---    -------        --------                                                                              
    False v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..  False v 4.0.30319 C:\Program Files\WindowsPowerShell\Modules\Az.Accounts \. ..

    Quando o script terminar, você verá a seguinte saída:

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. Para desbloquear o disco anexado usando o arquivo BEK, execute o comando a seguir:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Neste exemplo, o disco do SO anexado é a unidade F. Certifique-se de usar a letra da unidade correta. 

6. Depois que o disco for desbloqueado com êxito usando a chave BEK, desanexe o disco da VM de recuperação e recrie a VM usando esse novo disco do sistema operacional. 

    > [!NOTE]
    > Não há suporte para alternar o disco do sistema operacional para VMs que usam criptografia de disco.

7. Se a nova VM ainda não puder ser inicializada normalmente, tente uma das etapas a seguir depois de desbloquear a unidade:

    - Suspenda a proteção para desativar temporariamente o BitLocker executando o seguinte comando:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Descriptografar completamente a unidade. Para fazer isso, execute o seguinte comando:

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>Solução de problemas de script

**Erro: não foi possível carregar o arquivo ou o assembly**

Esse erro ocorre porque os caminhos dos assemblies da ADAL estão errados. Você pode procurar `Az.Accounts` pasta para localizar o caminho correto.

**Erro: Get-AzKeyVaultSecret ou Get-AzKeyVaultSecret não é reconhecido como o nome de um cmdlet**

Se você estiver usando o módulo atual AZ PowerShell, deverá alterar os dois comandos para `Get-AzureKeyVaultSecret` e `Get-AzureKeyVaultSecret` .

**Exemplos de parâmetros**

| Parâmetros  | Exemplo de valor  |Comentários   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | O nome do cofre de chaves que armazena a chave |
|$kekName   |MyKey   | O nome da chave usada para criptografar a VM|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | O nome do segredo da chave da VM|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |O caminho para gravar o arquivo BEK.|
|$adTenant  |contoso.onmicrosoft.com   | FQDN ou GUID do seu Azure Active Directory que hospeda o cofre de chaves |
