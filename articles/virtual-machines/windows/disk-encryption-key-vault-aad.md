---
title: Criar e configurar um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)
description: Neste artigo, saiba como criar e configurar um cofre de chaves para Azure Disk Encryption com o Azure AD.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: fd239b52e647d7a0dfa3b60ee1dc31ea7005311b
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99805948"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Criando e configurando um cofre de chaves para Azure Disk Encryption com o Azure AD (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo do Azure AD para habilitar a criptografia de disco de VM. Com a nova versão, você não precisa mais fornecer as credenciais do Azure AD durante a etapa habilitar criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros de aplicativo do Azure AD usando a nova versão. Para exibir instruções para habilitar a criptografia de disco de VM usando a nova versão, consulte [Azure Disk Encryption](disk-encryption-overview.md). As VMs que já foram criptografadas com parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.**

O Azure Disk Encryption usa o Azure Key Vault para ajudar você a controlar e gerenciar os segredos e chaves de criptografia de disco.  Para obter mais informações sobre cofres-chave, consulte [Introdução ao Cofre de Chaves do Azure](../../key-vault/general/overview.md) e [Proteja seu cofre de chaves](../../key-vault/general/secure-your-key-vault.md). 

Criar e configurar um cofre de chaves para uso com o Azure Disk Encryption com o Azure AD (versão anterior) envolve três etapas:

1. Crie um cofre da chave. 
2. Configure um aplicativo do Azure AD e entidade de serviço.
3. Defina a política de acesso do Cofre de chaves para o aplicativo do Azure AD.
4. Defina as políticas de acesso avançado da chave de segurança.
 
Caso queira, também é possível gerar ou importar uma KEK (chave de criptografia de chave).

Consulte o artigo principal [criando e configurando um cofre de chaves para Azure Disk Encryption](disk-encryption-key-vault.md) para obter as etapas de como [instalar as ferramentas e conectar-se ao Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure).

> [!Note]
> As etapas neste artigo são automatizadas no [Script de CLI de pré-requisitos do Azure Disk Encryption](https://github.com/ejarvi/ade-cli-getting-started) e no [Script do PowerShell de pré-requisitos do Azure Disk Encryption](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Criar um cofre de chaves 
O Azure Disk Encryption se integra [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e segredos em sua assinatura do Cofre de chaves. Você pode criar um cofre de chaves ou usar um existente para o Azure Disk Encryption. Para obter mais informações sobre cofres-chave, consulte [Introdução ao Cofre de Chaves do Azure](../../key-vault/general/overview.md) e [Proteja seu cofre de chaves](../../key-vault/general/secure-your-key-vault.md). Você pode usar um modelo do Resource Manager, o Azure PowerShell ou a CLI do Azure para criar um cofre de chaves. 


>[!WARNING]
>Para garantir que os segredos de criptografia não ultrapassem os limites regionais, o Azure Disk Encryption precisa que o Key Vault e as VMs sejam colocados na mesma região. Crie e use um cofre de chaves que esteja na mesma região da VM a ser criptografada. 


### <a name="create-a-key-vault-with-powershell"></a> Criar um cofre de chaves com o PowerShell

Você pode criar um cofre de chaves com Azure PowerShell usando o cmdlet [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Para obter cmdlets adicionais para Key Vault, consulte [AZ. keyvault](/powershell/module/az.keyvault/). 

1. Crie um novo grupo de recursos, se necessário, com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Para listar locais de data center, use [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Criar um novo cofre de chaves usando [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Observe a **nome do cofre**, **nome do grupo de recursos**, **ID do recurso**, **URI do cofre** e o **ID de objeto** que são retornados para uso posterior ao criptografar os discos. 


### <a name="create-a-key-vault-with-azure-cli"></a> Criar um cofre de chaves com CLI do Azure
Você pode gerenciar o Cofre de chaves com CLI do Azure usando os comandos [keyvault az](/cli/azure/keyvault#commands). Para criar um cofre de chaves, use [az creata](/cli/azure/keyvault#az-keyvault-create).

1. Criar um novo grupo de recursos, se necessário, com [criar grupo de az](/cli/azure/group#az-group-create). Para listar os locais, use [locais de lista az conta](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Crie um novo cofre de chave usando [az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Observação o **nome do cofre** (nome), **nome do grupo de recursos**, **ID do recurso** (ID), **URI do cofre** e o **deIDdeobjeto** que são retornados para uso posterior. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a> Criar um cofre de chaves com um modelo do Resource Manager

Você pode criar um cofre de chaves usando o modelo [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.
2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, o nome do Key Vault, a ID do objeto, os termos legais e o contrato e clique em **comprar**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a> Configurar um aplicativo do Azure AD e o serviço de entidade 
Quando você precisa habilitar a criptografia em uma VM em execução no Azure, o Azure Disk Encryption gera e grava as chaves de criptografia no cofre de chaves. O gerenciamento de chaves de criptografia no cofre de chaves requer a autenticação do Azure AD. Crie um aplicativo do Azure AD para essa finalidade. Para fins de autenticação, você pode usar a autenticação baseada em segredo do cliente ou a [autenticação do Azure AD baseada em certificado de cliente](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a> Configure um aplicativo e uma entidade de serviço do Azure AD com o Azure PowerShell 
Para executar os seguintes comandos, obtenha e use o [módulo do PowerShell do Azure AD](/powershell/azure/active-directory/install-adv2). 

1. Use o cmdlet do PowerShell [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) para criar um aplicativo do Azure AD. MyApplicationHomePage e MyApplicationUri podem ser quaisquer valores que você desejar.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. O $ azureAdApplication.ApplicationId é o ClientID do Azure AD e o $ aadClientSecret é o segredo do cliente que você usará posteriormente para ativar a Criptografia de Disco do Azure. Proteja adequadamente o segredo do cliente no Azure AD. A execução `$azureAdApplication.ApplicationId` mostrará o ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a> Configure um aplicativo e uma entidade de serviço do Azure AD com a CLI do Azure

Você pode gerenciar seus principais de serviço com a CLI do Azure usando os comandos [az ad sp](/cli/azure/ad/sp). Para obter mais informações, consulte [criar uma entidade de serviço do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Crie uma nova entidade de serviço.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  O appId retornado é o ClientID do Azure AD usado em outros comandos. Ele é também o SPN que você usará para az keyvault set-policy. A senha é o segredo do cliente que você deve usar posteriormente para ativar a criptografia de disco do Azure. Proteja adequadamente o segredo do cliente no Azure AD.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a> Configure um aplicativo e uma entidade de serviço do Azure AD através do portal do Azure
Use as etapas do artigo [usar o portal para criar aplicativo e entidade de serviço que pode acessar recursos de um Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md) para criar um aplicativo do Azure AD. Cada etapa listada abaixo levará você diretamente para a seção de artigos para concluir. 

1. [Verifique se as permissões necessárias](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Criar um aplicativo do Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - Você pode usar qualquer nome e URL de logon que desejar ao criar o aplicativo.
3. [Obtenha o ID do aplicativo e a chave de autenticação](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). 
     - A chave de autenticação é o segredo do cliente e é usada como AadClientSecret para Set-AzVMDiskEncryptionExtension. 
        - A chave de autenticação é usada pelo aplicativo como uma credencial para entrar no Azure AD. No portal do Azure, esse segredo é chamado de chaves, mas não tem relação com os cofres da chave. Proteja esse segredo adequadamente. 
     - A ID do aplicativo será usada posteriormente como AadClientId para Set-AzVMDiskEncryptionExtension e como o servicePrincipalName para Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Definir a política de acesso ao cofre de chaves para o aplicativo Azure AD
Para gravar segredos de criptografia em um Cofre de Chaves especificado, o Azure Disk Encryption precisa do ID do Cliente e do Segredo do Cliente do aplicativo Azure Active Directory que tenha permissões para gravar segredos no Cofre de Chaves. 

> [!NOTE]
> o Azure Disk Encryption exige que você configure as seguintes políticas de acesso ao aplicativo de cliente do Azure AD: permissões _WrapKey_ e _Set_.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a> Defina a política de acesso ao cofre principal para o aplicativo Azure AD com o Azure PowerShell
Seu aplicativo Azure AD precisa de direitos para acessar as chaves ou os segredos no cofre. Use o cmdlet [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para conceder permissões ao aplicativo, usando a ID do cliente (que foi gerada quando o aplicativo foi registrado) como o valor do parâmetro _– servicePrincipalName_ . Para saber mais, confira a postagem de blog [Azure Key Vault - passo a passo](/archive/blogs/kv/azure-key-vault-step-by-step). 

1. Defina a política de acesso ao vault principal para o aplicativo AD com o PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a> Defina a política de acesso ao cofre de chaves do aplicativo Azure AD com a CLI do Azure
Use [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) para definir a política de acesso. Para obter mais informações, consulte [Gerenciar cofre de chaves usando o CLI 2.0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Dê à entidade de serviço que você criou por meio do acesso da CLI do Azure para obter segredos e agrupar chaves com o seguinte comando:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a> Defina a política de acesso ao cofre de chaves do aplicativo Azure AD com o portal

1. Abra o grupo de recursos com o Cofre de chaves.
2. Selecione seu cofre de chaves, vá para **Acessar Políticas** e clique em **Adicionar novo**.
3. Em **Selecionar principal**, procure o aplicativo do Azure AD criado e selecione-o. 
4. Para **permissões de chave**, verifique **Wrap Key** sob **operações criptográficas**.
5. Para **permissões do segredo**, verifique **definir** sob **operações de gerenciamento de segredo**.
6. Clique em **Ok** para salvar a política de acesso. 

![Azure Key Vault operações criptográficas - Wrap Key](../media/disk-encryption/keyvault-portal-fig3.png)

![Permissões secretas do Cofre de Chaves do Azure - Definir](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Definir as políticas de acesso avançado do cofre de chaves
A plataforma Azure precisa acessar as chaves de criptografia ou os segredos no cofre de chaves para disponibilizá-los para a máquina virtual para inicialização e descriptografar os volumes. Habilite a criptografia de disco no cofre da chave ou as implantações falharão.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a> Definir políticas de acesso avançado ao cofre de chaves com o Azure PowerShell
 Use o cmdlet de cofre de chaves [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) do PowerShell para habilitar a criptografia de disco para o cofre da chaves.

  - **Ative o Key Vault para criptografia de disco:** EnabledForDiskEncryption é necessário para a criptografia do Azure Disk.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Ative o cofre de chaves para implantação, se necessário:** Permite que o provedor de recursos Microsoft.Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é mencionado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Ative o cofre de chaves para implantação de modelos, se necessário:** Permite que o Azure Resource Manager obtenha segredos desse cofre de chaves quando esse cofre de chaves for mencionado em uma implantação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a> Definir Cofre de chaves avançadas de políticas de acesso usando a CLI do Azure
Use [atualização de keyvault az](/cli/azure/keyvault#az-keyvault-update) para habilitar a criptografia de disco para o Cofre de chaves. 

 - **Habilite o cofre de chaves para criptografia de disco:** habilitado para criptografia de disco é necessária. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Habilite o cofre de chaves para a implantação, se necessário:** máquinas de virtuais permitem recuperar certificados armazenados como segredos do cofre.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Habilite o cofre de chaves para implantação de modelo, se necessário:** permitem que o Gerenciador de recursos para recuperar segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a> Defina as políticas de acesso avançado ao cofre de chaves por meio do portal do Azure

1. Selecione sua keyvault, vá para **Access Policies** e **Clique para mostrar as políticas de acesso avançadas**.
2. Selecione a caixa rotulada **habilitar o acesso ao Azure Disk Encryption para criptografia de volume**.
3. Selecione **habilitar o acesso às máquinas virtuais do Azure para implantação** e/ou **habilitar acesso ao Azure Resource Manager para implantação de modelo**, se necessário. 
4. Clique em **Salvar**.

![Cofre de chaves do Azure, políticas de acesso avançadas](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Configurar uma chave de criptografia de chave (opcional)
Se você quiser usar uma chave de criptografia (KEK) para uma camada adicional de segurança para chaves de criptografia, adicione uma KEK ao seu cofre de chaves. Use o cmdlet [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para criar uma chave de criptografia de chave no cofre de chaves. Você também pode importar a KEK do HSM do gerenciamento de chaves local. Para obter mais informações, consulte [documentação do cofre de chaves](../../key-vault/keys/hsm-protected-keys.md). Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault. 

* Ao gerar chaves, use um tipo de chave RSA. Azure Disk Encryption ainda não dá suporte ao uso de chaves de curva elíptica.

* O segredo do cofre de chaves e as URLs KEK devem ter controle de versão. O Azure impõe essa restrição de controle de versão. Para um segredo válido e URLs KEK, confira os seguintes exemplos:

  * Exemplo de uma URL secreta válida:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de uma URL KEK válida:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* o Azure Disk Encryption não dá suporte à especificação de números de portas como parte de segredos do cofre de chaves e URLs KEK. Para exemplos de URLs do cofre de chaves não suportados e suportados, consulte os seguintes exemplos:

  * URL do cofre de chaves inaceitável  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do cofre de chaves aceitável:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a> Configurar uma chave de criptografia chave com o Azure PowerShell 
Antes de usar o script do PowerShell, você deve estar familiarizado com os pré-requisitos do Azure Disk Encryption para entender as etapas no script. O script de amostra pode precisar de mudanças para seu ambiente. Esse script cria todos os pré-requisitos da criptografia de disco do Azure e criptografa uma VM IaaS existente, envolvendo a chave de criptografia de disco usando uma chave de criptografia de chave. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="certificate-based-authentication-optional"></a> Autenticação baseada em certificado (opcional)
Se você quiser usar a autenticação de certificado, poderá fazer o upload de um para o seu cofre de chaves e implantá-lo no cliente. Antes de usar o script do PowerShell, você deve estar familiarizado com os pré-requisitos do Azure Disk Encryption para entender as etapas no script. O script de amostra pode precisar de mudanças para seu ambiente.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="certificate-based-authentication-and-a-kek-optional"></a> Autenticação baseada em certificado e uma KEK (opcional)

Se você quiser usar a autenticação de certificado e encapsular a chave de criptografia com uma KEK, use o script abaixo como exemplo. Antes de usar o script do PowerShell, você deve estar familiarizado com todos os pré-requisitos anteriores da Criptografia de Disco do Azure para entender as etapas do script. O script de amostra pode precisar de mudanças para seu ambiente.

     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Próximas etapas

[Habilitar Azure Disk Encryption com o Azure AD em VMs do Windows (versão anterior)](disk-encryption-windows-aad.md)
