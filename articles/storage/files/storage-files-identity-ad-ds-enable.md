---
title: Habilitar a autenticação de AD DS para compartilhamentos de arquivos do Azure
description: Saiba como habilitar a autenticação de Active Directory Domain Services sobre o SMB para compartilhamentos de arquivos do Azure. Suas máquinas virtuais do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando credenciais de AD DS.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 09/13/2020
ms.author: rogarana
ms.openlocfilehash: 948b30cbf37ae5f4f357860569579d8591412414
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630389"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>Parte um: habilitar a autenticação de AD DS para seus compartilhamentos de arquivos do Azure 

Antes de habilitar a autenticação do Active Directory Domain Services (AD DS), verifique se você leu o [artigo de visão geral](storage-files-identity-auth-active-directory-enable.md) para entender os requisitos e cenários com suporte.

Este artigo descreve o processo necessário para habilitar a autenticação de Active Directory Domain Services (AD DS) em sua conta de armazenamento. Depois de habilitar o recurso, você deve configurar sua conta de armazenamento e sua AD DS para usar AD DS credenciais para autenticação no compartilhamento de arquivos do Azure. Para habilitar a autenticação de AD DS sobre o SMB para compartilhamentos de arquivos do Azure, você precisa registrar sua conta de armazenamento com AD DS e, em seguida, definir as propriedades de domínio necessárias na conta de armazenamento.

Para registrar sua conta de armazenamento com o AD DS, crie uma conta que a represente em seu AD DS. Você pode considerar esse processo como se fosse como criar uma conta que represente um servidor de arquivos do Windows local em seu AD DS. Quando o recurso é habilitado na conta de armazenamento, ele se aplica a todos os compartilhamentos de arquivos novos e existentes na conta.

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>Opção um (recomendado): usar o módulo do PowerShell do AzFilesHybrid

Os cmdlets no módulo AzFilesHybrid do PowerShell fazem as modificações necessárias e habilitam o recurso para você. Como algumas partes dos cmdlets interagem com sua AD DS local, explicamos o que os cmdlets fazem, para que você possa determinar se as alterações estão alinhadas com suas políticas de conformidade e segurança e se você tem as permissões adequadas para executar os cmdlets. Embora seja recomendável usar o módulo AzFilesHybrid, se não for possível fazer isso, forneceremos as etapas para que você possa executá-las manualmente.

### <a name="download-azfileshybrid-module"></a>Baixar o módulo AzFilesHybrid

- [Baixar e descompactar o módulo AzFilesHybrid (módulo ga: v 0.2.0 +)](https://github.com/Azure-Samples/azure-files-samples/releases) Observe que a criptografia Kerberos AES 256 tem suporte em v 0.2.2 ou superior. Se você tiver habilitado o recurso com uma versão AzFilesHybrid inferior a v 0.2.2 e quiser atualizar para dar suporte à criptografia Kerberos AES 256, consulte [Este artigo](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption). 
- Instale e execute o módulo em um dispositivo que esteja ingressado no domínio no local AD DS com credenciais de AD DS que tenham permissões para criar uma conta de logon de serviço ou uma conta de computador no AD de destino.
-  Execute o script usando uma credencial de AD DS local que é sincronizada com o Azure AD. A credencial de AD DS local deve ter o proprietário da conta de armazenamento ou as permissões de função do Azure do colaborador.

### <a name="run-join-azstorageaccountforauth"></a>Executar Join-AzStorageAccountForAuth

O `Join-AzStorageAccountForAuth` cmdlet executa o equivalente a um ingresso no domínio offline em nome da conta de armazenamento especificada. O script usa o cmdlet para criar uma [conta de computador](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) em seu domínio do AD. Se, por qualquer motivo, você não puder usar uma conta de computador, poderá alterar o script para criar uma [conta de logon de serviço](/windows/win32/ad/about-service-logon-accounts) . Se você optar por executar o comando manualmente, deverá selecionar a conta mais adequada para o seu ambiente.

A conta de AD DS criada pelo cmdlet representa a conta de armazenamento. Se a conta de AD DS for criada em uma UO (unidade organizacional) que impõe a expiração da senha, você deverá atualizar a senha antes da duração máxima da senha. A falha ao atualizar a senha da conta antes dessa data resulta em falhas de autenticação ao acessar compartilhamentos de arquivos do Azure. Para saber como atualizar a senha, consulte [atualizar a senha da conta do AD DS](storage-files-identity-ad-ds-update-password.md).

Substitua os valores de espaço reservado pelos seus próprios nos parâmetros abaixo antes de executá-lo no PowerShell.
> [!IMPORTANT]
> O cmdlet ingresso no domínio criará uma conta do AD para representar a conta de armazenamento (compartilhamento de arquivos) no AD. Você pode optar por se registrar como uma conta de computador ou conta de logon de serviço, consulte [perguntas frequentes](./storage-files-faq.md#security-authentication-and-access-control) para obter detalhes. Para contas de computador, há uma duração de expiração de senha padrão definida no AD em 30 dias. Da mesma forma, a conta de logon do serviço pode ter uma duração de expiração de senha padrão definida no domínio do AD ou na UO (unidade organizacional).
> Para ambos os tipos de conta, recomendamos que você verifique a duração da validade da senha configurada em seu ambiente do AD e planeje a [atualização da senha de sua identidade de conta de armazenamento](storage-files-identity-ad-ds-update-password.md) da conta do AD antes da duração máxima da senha. Você pode considerar [a criação de uma nova ou (unidade organizacional) do AD no AD](/powershell/module/addsadministration/new-adorganizationalunit?view=win10-ps) e a desabilitação da política de expiração de senha em [contas de computador](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) ou contas de logon de serviço de acordo. 

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
Connect-AzAccount

#Define parameters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType "<ComputerAccount|ServiceLogonAccount>" <# Default is set as ComputerAccount #> `
        -OrganizationalUnitDistinguishedName "<ou-distinguishedname-here>" <# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory. #> `
        -EncryptionType "<AES256/RC4/AES256,RC4>" <# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption. #>

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-2-manually-perform-the-enablement-actions"></a>Opção 2: executar manualmente as ações de habilitação

Se você já executou o `Join-AzStorageAccountForAuth` script acima com êxito, vá para a seção [confirmar se o recurso está habilitado](#confirm-the-feature-is-enabled) . Você não precisa executar as etapas manuais a seguir.

### <a name="checking-environment"></a>Verificando o ambiente

Primeiro, você deve verificar o estado do seu ambiente. Especificamente, você deve verificar se [Active Directory o PowerShell](/powershell/module/addsadministration/?view=win10-ps) está instalado e se o Shell está sendo executado com privilégios de administrador. Em seguida, verifique se o [módulo Az.Storage 2.0](https://www.powershellgallery.com/packages/Az.Storage/2.0.0) está instalado e instale-o se não estiver. Depois de concluir essas verificações, verifique sua AD DS para ver se há uma [conta de computador](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (padrão) ou [conta de logon de serviço](/windows/win32/ad/about-service-logon-accounts) que já foi criada com SPN/UPN como "CIFS/Your-Storage-Account-Name-aqui. File. Core. Windows. net". Se a conta não existir, crie uma conforme descrito na seção a seguir.

### <a name="creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>Criando uma identidade que representa a conta de armazenamento em seu AD manualmente

Para criar essa conta manualmente, crie uma nova chave Kerberos para sua conta de armazenamento. Em seguida, use essa chave Kerberos como a senha para sua conta com os cmdlets do PowerShell abaixo. Essa chave só é usada durante a instalação e não pode ser usada para nenhuma operação de controle ou plano de dados na conta de armazenamento. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

Depois de ter essa chave, crie uma conta de serviço ou de computador em sua UO. Use a seguinte especificação (Lembre-se de substituir o texto de exemplo pelo nome da sua conta de armazenamento):

SPN: "CIFS/Your-Storage-Account-Name-aqui. File. Core. Windows. net" Password: chave Kerberos para sua conta de armazenamento.

Se sua UO impõe a expiração de senha, você deve atualizar a senha antes da duração máxima da senha para evitar falhas de autenticação ao acessar compartilhamentos de arquivos do Azure. Consulte [atualizar a senha de sua identidade de conta de armazenamento no AD](storage-files-identity-ad-ds-update-password.md) para obter detalhes.

Mantenha o SID da identidade recém-criada, você precisará dela para a próxima etapa. A identidade que você criou que representa a conta de armazenamento não precisa ser sincronizada com o Azure AD.

### <a name="enable-the-feature-on-your-storage-account"></a>Habilitar o recurso em sua conta de armazenamento

Agora você pode habilitar o recurso em sua conta de armazenamento. Forneça alguns detalhes de configuração para as propriedades de domínio no comando a seguir e execute-o. O SID da conta de armazenamento necessário no comando a seguir é o SID da identidade que você criou em seu AD DS na [seção anterior](#creating-an-identity-representing-the-storage-account-in-your-ad-manually).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>Depuração

Você pode executar o cmdlet Debug-AzStorageAccountAuth para conduzir um conjunto de verificações básicas em sua configuração do AD com o usuário conectado do AD. Esse cmdlet é compatível com a versão AzFilesHybrid v0.1.2+. Para obter mais informações sobre as verificações executadas nesse cmdlet, consulte [não é possível montar os arquivos do Azure com credenciais do AD](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials) no guia de solução de problemas do Windows.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>Confirmar se o recurso está habilitado

Você pode verificar para confirmar se o recurso está habilitado em sua conta de armazenamento com o script a seguir:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Se for bem-sucedido, a saída deverá ter a seguinte aparência:

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>Próximas etapas

Agora você ativou com êxito o recurso em sua conta de armazenamento. Para usar o recurso, você deve atribuir permissões de nível de compartilhamento. Continue na próxima seção.

[Parte dois: atribuir permissões de nível de compartilhamento a uma identidade](storage-files-identity-ad-ds-assign-permissions.md)