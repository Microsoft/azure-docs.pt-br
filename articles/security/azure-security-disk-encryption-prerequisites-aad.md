---
title: Azure Disk Encryption com pré-requisitos de Aplicativo Azure AD (versão anterior)
description: Este artigo fornece os pré-requisitos para usar o Microsoft Azure Disk Encryption para VMs IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5fa8e54a6a665b1bad91a87ca8e58f873df1ae8a
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672311"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Pré-requisitos do Azure Disk Encryption (versão anterior)

**A nova versão do Azure Disk Encryption elimina a necessidade de fornecer um parâmetro de aplicativo do Azure AD para habilitar a criptografia de disco de VM. Com a nova versão, você não precisa fornecer credenciais do Azure AD durante a etapa de habilitação de criptografia. Todas as novas VMs devem ser criptografadas sem os parâmetros do aplicativo do Azure AD, usando a nova versão. Para exibir instruções para habilitar a criptografia de disco de VM usando a nova versão, veja [Pré-requisitos do Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md). As VMs que já foram criptografadas com os parâmetros de aplicativo do Azure AD ainda têm suporte e devem continuar a ser mantidas com a sintaxe do AAD.**

 Neste artigo, pré-requisitos de criptografia de disco do Azure, explica os itens que precisam estar em vigor antes de usar o Azure Disk Encryption. Junto com os pré-requisitos gerais, o Azure Disk Encryption é integrado com [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) e usa um aplicativo Azure AD para fornecer autenticação para gerenciar chaves de criptografia no cofre de chaves. Você também poderá usar [Azure PowerShell](/powershell/azure/overview) ou o [CLI do Azure](/cli/azure/) para configurar o Cofre de chaves e o aplicativo do Azure AD.

Antes de habilitar o Azure Disk Encryption em VMs IaaS do Azure para os cenários com suporte que foram discutidos na [visão geral de criptografia de disco do Azure](azure-security-disk-encryption-overview.md) do artigo, certifique-se de ter os pré-requisitos em vigor. 

> [!WARNING]
> - Determinadas recomendações podem aumentar o uso de recursos de dados, rede ou computação, resultando em custos adicionais de licença ou inscrição. Você deve ter uma assinatura ativa válida do Azure para criar recursos no Azure nas regiões com suporte.
> - Se você usou o [Azure Disk Encryption com aplicativo do Microsoft Azure Active Directory](azure-security-disk-encryption-prerequisites-aad.md) anteriormente para criptografar essa VM, você deverá continuar usando essa opção para criptografar a VM. Não é possível usar o [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) nessa VM criptografada pois esse cenário não tem suporte, o que significa que ainda não há suporte para alternar o aplicativo AAD por essa VM criptografada. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

### <a name="windows"></a>Windows

- Cliente do Windows: Windows 8 e posterior.
- Windows Server: Windows Server 2008 R2 e versões posteriores.  
 
> [!NOTE] 
> Windows Server 2008 R2 requer o .NET Framework 4.5 ser instalado para a criptografia; Instale-o a partir do Windows Update com a atualização opcional Microsoft .NET Framework 4.5.2 para sistemas baseados em x64 do Windows Server 2008 R2 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Núcleo do Windows Server 2012 R2 e Windows Server 2016 Core requer o componente bdehdcfg para ser instalado na VM para criptografia.

### <a name="linux"></a>Linux 

Azure Disk Encryption tem suporte em um subconjunto do [distribuições do Linux endossadas pelo Azure](../virtual-machines/linux/endorsed-distros.md), que é um subconjunto de todas as distribuições Linux server possíveis, em si.

![Diagrama de Venn de distribuições do Linux server que dá suporte à criptografia de disco do Azure](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Distribuições do Linux e de servidor que não são endossadas pelo Azure não dão suporte a Azure Disk Encryption e, desses endossadas, somente os seguintes distribuições e versões de suportam Azure Disk Encryption:

| Distribuição Linux | Versão | Tipo de volume suportado para criptografia|
| --- | --- |--- |
| Ubuntu | 18.04| SO e disco de dados |
| Ubuntu | 16.04| SO e disco de dados |
| Ubuntu | 14.04.5</br>[com kernel ajustado para Azure atualizado para 4.15 ou posterior](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | SO e disco de dados |
| RHEL | 7.6 | Disco do sistema operacional e dados (consulte a observação abaixo) |
| RHEL | 7.5 | Disco do sistema operacional e dados (consulte a observação abaixo) |
| RHEL | 7.4 | Disco do sistema operacional e dados (consulte a observação abaixo) |
| RHEL | 7.3 | Disco do sistema operacional e dados (consulte a observação abaixo) |
| RHEL | 7,2 | Disco do sistema operacional e dados (consulte a observação abaixo) |
| RHEL | 6,8 | Disco de dados (consulte a observação abaixo) |
| RHEL | 6.7 | Disco de dados (consulte a observação abaixo) |
| CentOS | 7.6 | SO e disco de dados |
| CentOS | 7.5 | SO e disco de dados |
| CentOS | 7.4 | SO e disco de dados |
| CentOS | 7.3 | SO e disco de dados |
| CentOS | 7.2n | SO e disco de dados |
| CentOS | 6,8 | Disco de dados |
| openSUSE | 42.3 | Disco de dados |
| SLES | 12-SP4 | Disco de dados |
| SLES | 12-SP3 | Disco de dados |

> [!NOTE]
> A nova implementação ADE tem suporte para RHEL SO e disco de dados para imagens RHEL7 pay-as. Atualmente, o ADE não dá suporte para imagens RHEL BYOS (Traga sua própria assinatura). Ver [Azure Disk Encryption para Linux](azure-security-disk-encryption-linux.md) para obter mais informações.

- O Azure Disk Encryption exige que seu cofre de chaves e as VMs residam na mesma região e assinatura do Azure. Configurá os recursos em regiões separadas causa uma falha na habilitação do recurso de Azure Disk Encryption.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Pré-requisitos adicionais para VMs IaaS Linux 

- Azure Disk Encryption exige o dm-crypt e vfat módulos para serem presentes no sistema. Remover ou desabilitar vfat a partir da imagem padrão impedirá que o sistema de ler o volume de chave e obter a chave necessária para desbloquear os discos em reinicializações subsequentes. Etapas de proteção do sistema que remova o módulo vfat do sistema não são compatíveis com o Azure Disk Encryption. 
- Antes de habilitar a criptografia, os discos de dados a serem criptografados precisam ser listados corretamente em /etc/fstab. Use um nome de dispositivo de bloco persistente para essa entrada, pois nomes de dispositivo no formato "/dev/sdX" não podem ser considerados para serem associados com o mesmo disco entre as reinicializações, principalmente depois que a criptografia é aplicada. Para obter mais detalhes sobre esse comportamento, consulte: [Solucionar problemas de alterações do nome do dispositivo da VM do Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Verifique se as configurações de /etc/fstab estão definidas corretamente para a montagem. Para definir essas configurações, execute o comando mount - a ou reinicie a VM e disparar a remontagem dessa forma. Quando terminar, verifique a saída do comando lsblk para verificar se a unidade ainda está montada. 
  - Se o arquivo /etc/fstab não montar a unidade corretamente antes de habilitar a criptografia, o Azure Disk Encryption não será capaz de montá-la corretamente.
  - O processo de criptografia de disco do Azure se moverá as informações de montagem fora /etc/fstab e em seu próprio arquivo de configuração como parte do processo de criptografia. Não se assuste para ver a entrada ausente do /etc/fstab após a criptografia de unidade de dados é concluída.
  - Antes de iniciar a criptografia, não se esqueça de parar todos os serviços e processos que poderia estar escrevendo para os discos de dados montados e desabilitá-los, para que eles não reiniciam automaticamente após uma reinicialização. Eles poderiam manter arquivos abertos nessas partições, impedindo que o procedimento de criptografia para remontá-los, causando a falha da criptografia. 
  - Após a reinicialização, ela levará tempo para o processo de criptografia de disco do Azure montar os discos criptografados recentemente. Eles não estarão disponíveis imediatamente após uma reinicialização. O processo precisa de tempo para iniciar, desbloquear e, em seguida, montar as unidades criptografadas antes de estar disponível para ser acessado por outros processos. Esse processo pode levar mais de um minuto após a reinicialização, dependendo das características do sistema.

Um exemplo de comandos que podem ser usados para montar os discos de dados e criar as entradas/ etc/fstab necessárias pode ser encontrado nas [linhas 244-248 desse arquivo de script ](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Sistema de rede e a diretiva de grupo

**Para habilitar o recurso de Azure Disk Encryption usando a sintaxe do parâmetro AAD, as VMs IaaS devem atender os requisitos de configuração do ponto de extremidade da rede a seguir:** 
  - Para obter um token para se conectar ao seu cofre de chaves, a VM IaaS deve ser capaz de se conectar a um ponto de extremidade do Azure Active Directory, \[login.microsoftonline.com\].
  - Para gravar as chaves de criptografia no cofre de chaves, a VM IaaS deve ser capaz de se conectar ao ponto de extremidade do cofre de chaves.
  - A VM IaaS deve ser capaz de se conectar a um ponto de extremidade do armazenamento do Azure que hospeda o repositório de extensão do Azure e uma conta de armazenamento do Azure que hospeda os arquivos VHD.
  -  Se a política de segurança limita o acesso de VMs do Azure à Internet, você pode resolver o URI anterior e configurar uma regra específica para permitir a conectividade de saída para os IPs. Para obter mais informações, consulte [Azure Key Vault por trás de um firewall](../key-vault/key-vault-access-behind-firewall.md).
  - No Windows, se o TLS 1.0 foi desabilitado explicitamente e a versão do .NET não foi atualizada para 4.6 ou superior, a seguinte alteração do registro permitirá ADE selecionar a versão mais recente do TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**Política de grupo:**
 - A solução de Azure Disk Encryption usa o protetor de chave externa BitLocker para VMs IaaS do Windows. Para VMs ingressado no domínio, não envie por push todas as políticas de grupo que imponham protetores TPM. Para obter informações sobre a política de grupo "Permitir BitLocker sem um TPM compatível", confira [Referência de política de grupo do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  A política do BitLocker em máquinas virtuais ingressadas no domínio com política de grupo personalizada deve incluir a seguinte configuração: [Configurar o armazenamento de usuário do BitLocker as informações de recuperação -> permitir 256 bits chave de recuperação](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). O Azure Disk Encryption falha quando as configurações da política de grupo personalizada para o BitLocker são incompatíveis. Em computadores que não tinham a configuração de política correta, aplique a nova política, force a atualização da nova política (gpupdate.exe /force) e, em seguida, pode ser necessário reiniciar.  


## <a name="bkmk_PSH"></a>PowerShell do Azure
O [Azure PowerShell](/powershell/azure/overview) fornece um conjunto de cmdlets que usa o modelo do [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) para gerenciar os recursos do Azure. Você pode usá-lo em seu navegador com [Azure Cloud Shell](../cloud-shell/overview.md), ou você pode instalá-lo em seu computador local usando as instruções abaixo para usá-lo em qualquer sessão do PowerShell. Se você já tiver instalado localmente, verifique se que você usar a versão mais recente do Azure PowerShell para configurar o Azure Disk Encryption.

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Instale o Azure PowerShell para uso em seu computador local (opcional):  
1. [Instale e configure o Azure PowerShell](/powershell/azure/install-az-ps). 

2. Instalar o [módulo do Azure Active Directory PowerShell](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. Verifique se as versões instaladas dos módulos.
      ```powershell
      Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Entrar no Azure usando o [AzAccount Connect](/powershell/module/az.accounts/connect-azaccount) cmdlet.
     
     ```powershell
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Conectar ao Azure AD [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. Revise [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps) e [AzureAD](/powershell/module/azuread), se necessário.

## <a name="bkmk_CLI"></a>Azure CLI

O [CLI 2.0 do Azure](/cli/azure) é uma ferramenta de linha de comando para gerenciar recursos do Azure. A CLI é projetada para consultar dados com flexibilidade, dar suporte a operações de longa execução como processos desbloqueados e facilitar o script. Você pode usá-lo em seu navegador com o [Azure Cloud Shell](../cloud-shell/overview.md), ou você pode instalá-lo em seu computador local e usá-lo em qualquer sessão do PowerShell.

1. [Instalar CLI do Azure](/cli/azure/install-azure-cli) para uso em seu computador local (opcional):

2. Verifique se a versão instalada.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Entrar no Azure usando [login az](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Revisão [começar com a CLI do Azure 2.0](/cli/azure/get-started-with-azure-cli) se necessário. 


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Fluxo de trabalho pré-requisito para o Cofre de chaves e o aplicativo do Azure AD

Se você já estiver familiarizado com os pré-requisitos do Key Vault e do Azure AD para o Azure Disk Encryption, você pode usar o [script do PowerShell de pré-requisitos do Azure Disk Encryption](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Para obter mais informações sobre como usar o script de pré-requisitos, veja o [Início rápido para criptografar uma VM](quick-encrypt-vm-powershell.md) e o [Apêndice do Azure Disk Encryption](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Crie um cofre da chave. 
2. Configure um aplicativo do Azure AD e entidade de serviço.
3. Defina a política de acesso do Cofre de chaves para o aplicativo do Azure AD.
4. Defina as políticas de acesso avançado da chave de segurança.
 
## <a name="bkmk_KeyVault"></a> Criar um cofre de chaves 
O Azure Disk Encryption se integra [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) para ajudá-lo a controlar e gerenciar as chaves de criptografia de disco e segredos em sua assinatura do Cofre de chaves. Você pode criar um cofre de chaves ou usar um existente para o Azure Disk Encryption. Para obter mais informações sobre cofres-chave, consulte [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md) e [Proteja seu cofre de chaves](../key-vault/key-vault-secure-your-key-vault.md). Você pode usar um modelo do Resource Manager, o Azure PowerShell ou a CLI do Azure para criar um cofre de chaves. 


>[!WARNING]
>Para garantir que os segredos de criptografia não ultrapassem os limites regionais, o Azure Disk Encryption precisa que o Key Vault e as VMs sejam colocadas na mesma região. Crie e use um cofre de chaves que esteja na mesma região da VM a ser criptografada. 


### <a name="bkmk_KVPSH"></a> Criar um cofre de chaves com o PowerShell

Você pode criar um cofre de chaves com o Azure PowerShell usando o [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) cmdlet. Para outros cmdlets para o Cofre de chaves, consulte [Az.KeyVault](/powershell/module/az.keyvault/). 

1. Se necessário, [conectar-se à sua assinatura do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. Criar um novo grupo de recursos, se necessário, com [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Para listar os locais de centro de dados, use [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Criar um novo cofre de chaves usando [AzKeyVault novo](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Observe a **nome do cofre**, **nome do grupo de recursos**, **ID do recurso**, **URI do cofre**e o **ID de objeto** que são retornados para uso posterior ao criptografar os discos. 


### <a name="bkmk_KVCLI"></a> Criar um cofre de chaves com CLI do Azure
Você pode gerenciar o Cofre de chaves com CLI do Azure usando os comandos [keyvault az](/cli/azure/keyvault#commands). Para criar um cofre de chaves, use [az creata](/cli/azure/keyvault#az-keyvault-create).

1. Se necessário, [conectar-se à sua assinatura do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Criar um novo grupo de recursos, se necessário, com [criar grupo de az](/cli/azure/group#az-group-create). Para listar os locais, use [locais de lista az conta](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Crie um novo cofre de chave usando [az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Observação o **nome do cofre** (nome), **nome do grupo de recursos**, **ID do recurso** (ID), **URI do cofre**e o **deIDdeobjeto** que são retornados para uso posterior. 

### <a name="bkmk_KVRM"></a> Criar um cofre de chaves com um modelo do Resource Manager

Você pode criar um cofre de chaves usando o modelo [Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. No modelo de início rápido do Azure, clique em **Implantar no Azure**.
2. Selecione a assinatura, o grupo de recursos, o local do grupo de recursos, o nome do Key Vault, o ID do Objeto, os termos legais e o contrato e clique em **Compra**. 


## <a name="bkmk_ADapp"></a> Configurar um aplicativo do Azure AD e o serviço de entidade 
Quando você precisa habilitar a criptografia em uma VM em execução no Azure, o Azure Disk Encryption gera e grava as chaves de criptografia no cofre de chaves. O gerenciamento de chaves de criptografia no cofre de chaves requer a autenticação do Azure AD. Crie um aplicativo do Azure AD para essa finalidade. Para fins de autenticação, você pode usar a autenticação baseada em segredo do cliente ou a [autenticação do Azure AD baseada em certificado de cliente](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Configure um aplicativo e uma entidade de serviço do Azure AD com o Azure PowerShell 
Para executar os seguintes comandos, obtenha e use o [módulo do PowerShell do Azure AD](/powershell/azure/active-directory/install-adv2). 

1. Se necessário, [conectar-se à sua assinatura do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Use o [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) cmdlet do PowerShell para criar um aplicativo do Azure AD. MyApplicationHomePage e MyApplicationUri podem ser quaisquer valores que você desejar.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. O $ azureAdApplication.ApplicationId é o ClientID do Azure AD e o $ aadClientSecret é o segredo do cliente que você usará posteriormente para ativar a Criptografia de Disco do Azure. Proteja adequadamente o segredo do cliente no Azure AD. A execução `$azureAdApplication.ApplicationId` mostrará o ApplicationID.


### <a name="bkmk_ADappCLI"></a> Configure um aplicativo e uma entidade de serviço do Azure AD com a CLI do Azure

Você pode gerenciar seus principais de serviço com a CLI do Azure usando os comandos [az ad sp](/cli/azure/ad/sp). Para obter mais informações, consulte [criar uma entidade de serviço](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Se necessário, [conectar-se à sua assinatura do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Crie uma nova entidade de serviço.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  O appId retornado é o ClientID do Azure AD usado em outros comandos. Ele é também o SPN que você usará para az keyvault set-policy. A senha é o segredo do cliente que você deve usar posteriormente para ativar a criptografia de disco do Azure. Proteja adequadamente o segredo do cliente no Azure AD.
 
### <a name="bkmk_ADappRM"></a> Configure um aplicativo e uma entidade de serviço do Azure AD através do portal do Azure
Use as etapas do artigo [usar o portal para criar aplicativo e entidade de serviço que pode acessar recursos de um Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md) para criar um aplicativo do Azure AD. Cada etapa listada abaixo levará você diretamente para a seção de artigos para concluir. 

1. [Verifique se as permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Criar um aplicativo do Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Você pode usar qualquer nome e URL de logon que desejar ao criar o aplicativo.
3. [Obtenha o ID do aplicativo e a chave de autenticação](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - A chave de autenticação é o segredo do cliente e é usada como o AadClientSecret para Set-AzVMDiskEncryptionExtension. 
        - A chave de autenticação é usada pelo aplicativo como uma credencial para entrar no Azure AD. No portal do Azure, esse segredo é chamado de chaves, mas não tem relação com os cofres da chave. Proteja esse segredo adequadamente. 
     - A ID do aplicativo será usada posteriormente como o AadClientId para Set-AzVMDiskEncryptionExtension e ServicePrincipalName para Set-AzKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a> Defina a política de acesso ao cofre de chaves para o aplicativo Azure AD
Para gravar segredos de criptografia em um Cofre de Chaves especificado, o Azure Disk Encryption precisa do ID do Cliente e do Segredo do Cliente do aplicativo Azure Active Directory que tenha permissões para gravar segredos no Cofre de Chaves. 

> [!NOTE]
> O Azure Disk Encryption exige que você configure as seguintes políticas de acesso ao aplicativo de cliente do Azure AD: permissões _WrapKey_ e _Set_.

### <a name="bkmk_KVAPPSH"></a> Defina a política de acesso ao cofre principal para o aplicativo Azure AD com o Azure PowerShell
Seu aplicativo Azure AD precisa de direitos para acessar as chaves ou os segredos no cofre. Use o [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) cmdlet para conceder permissões para o aplicativo usando a ID do cliente (que foi gerada quando o aplicativo foi registrado) como o _– ServicePrincipalName_ valor do parâmetro. Para saber mais, confira a postagem de blog [Azure Key Vault - passo a passo](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Se necessário, [conectar-se à sua assinatura do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Defina a política de acesso ao vault principal para o aplicativo AD com o PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a> Defina a política de acesso ao cofre de chaves do aplicativo Azure AD com a CLI do Azure
Use [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) para definir a política de acesso. Para obter mais informações, consulte [Gerenciar cofre de chaves usando o CLI 2.0](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. Se necessário, [conectar-se à sua assinatura do Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Dê à entidade de serviço que você criou por meio do acesso da CLI do Azure para obter segredos e agrupar chaves com o seguinte comando:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> Defina a política de acesso ao cofre de chaves do aplicativo Azure AD com o portal

1. Abra o grupo de recursos com o Cofre de chaves.
2. Selecione seu cofre de chaves, vá para **Acessar Políticas** e clique em **Adicionar novo**.
3. Em **Selecionar principal**, procure o aplicativo do Azure AD criado e selecione-o. 
4. Para **permissões de chave**, verifique **Wrap Key** sob **operações criptográficas**.
5. Para **permissões do segredo**, verifique **definir** sob **operações de gerenciamento de segredo**.
6. Clique em **Ok** para salvar a política de acesso. 

![Azure Key Vault operações criptográficas - Wrap Key](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Permissões secretas do Cofre de Chaves do Azure - Definir](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Definir as políticas de acesso avançado do cofre de chaves
A plataforma Azure precisa acessar as chaves de criptografia ou os segredos no cofre de chaves para disponibilizá-los para a máquina virtual para inicialização e descriptografar os volumes. Habilite a criptografia de disco no cofre da chave ou as implantações falharão.  

### <a name="bkmk_KVperPSH"></a> Definir políticas de acesso avançado ao cofre de chaves com o Azure PowerShell
 Use o cmdlet de PowerShell do Cofre de chaves [AzKeyVaultAccessPolicy conjunto](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para habilitar a criptografia de disco para o Cofre de chaves.

  - **Habilitar o Key Vault para criptografia de disco:** EnabledForDiskEncryption é necessário para o Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Habilitar o Key Vault para a implantação, se necessário:** Permite que o provedor de recursos Microsoft.Compute recupere segredos desse cofre de chaves quando esse cofre de chaves é referenciado na criação de recursos, por exemplo, ao criar uma máquina virtual.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Habilitar o Key Vault para implantação de modelo, se necessário:** Permite que o Azure Resource Manager obtenha segredos desse cofre de chaves quando esse cofre de chaves é referenciado em uma implantação de modelo.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Definir Cofre de chaves avançadas de políticas de acesso usando a CLI do Azure
Use [atualização de keyvault az](/cli/azure/keyvault#az-keyvault-update) para habilitar a criptografia de disco para o Cofre de chaves. 

 - **Habilitar o Key Vault para criptografia de disco:** Enabled-for-disk-encryption é necessário. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Habilitar o Key Vault para a implantação, se necessário:** Permitir que Máquinas Virtuais recuperem certificados armazenados como segredos do cofre.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Habilitar o Key Vault para implantação de modelo, se necessário:** Permite que o Resource Manager recupere segredos do cofre.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Defina as políticas de acesso avançado ao cofre de chaves por meio do portal do Azure

1. Selecione sua keyvault, vá para **Access Policies** e **Clique para mostrar as políticas de acesso avançadas**.
2. Selecione a caixa rotulada **habilitar o acesso ao Azure Disk Encryption para criptografia de volume**.
3. Selecione **habilitar o acesso às máquinas virtuais do Azure para implantação** e/ou **habilitar acesso ao Azure Resource Manager para implantação de modelo**, se necessário. 
4. Clique em **Salvar**.

![Cofre de chaves do Azure, políticas de acesso avançadas](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Configurar uma chave de criptografia de chave (opcional)
Se você quiser usar uma chave de criptografia (KEK) para uma camada adicional de segurança para chaves de criptografia, adicione uma KEK ao seu cofre de chaves. Use o [AzKeyVaultKey adicionar](/powershell/module/az.keyvault/add-azkeyvaultkey) cmdlet para criar uma chave de criptografia de chave no cofre de chaves. Você também pode importar a KEK do HSM do gerenciamento de chaves local. Para obter mais informações, consulte [documentação do cofre de chaves](../key-vault/key-vault-hsm-protected-keys.md). Quando uma chave de criptografia de chave é especificada, o Azure Disk Encryption usa essa chave para agrupar os segredos de criptografia antes de gravar no Key Vault. 

* Ao gerar as chaves, use um tipo de chave RSA. O Azure Disk Encryption ainda não dá suporte usando as chaves de curva elíptica.

* O segredo do cofre de chaves e as URLs KEK devem ter controle de versão. O Azure impõe essa restrição de controle de versão. Para um segredo válido e URLs KEK, confira os seguintes exemplos:

  * Exemplo de uma URL secreta válida: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Exemplo de uma URL KEK válida: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* o Azure Disk Encryption não dá suporte à especificação de números de portas como parte de segredos do cofre de chaves e URLs KEK. Para exemplos de URLs do cofre de chaves não suportados e suportados, consulte os seguintes exemplos:

  * URL do cofre de chaves inaceitável  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * URL do cofre de chaves aceitável:  *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="bkmk_KEKPSH"></a> Configurar uma chave de criptografia chave com o Azure PowerShell 
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

## <a name="bkmk_Cert"></a> Autenticação baseada em certificado (opcional)
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

## <a name="bkmk_CertKEK"></a> Autenticação baseada em certificado e uma KEK (opcional)

Se você quiser usar a autenticação de certificado e encapsular a chave de criptografia com uma KEK, use o script abaixo como exemplo. Antes de usar o script do PowerShell, você deve estar familiarizado com todos os pré-requisitos anteriores da Criptografia de Disco do Azure para entender as etapas do script. O script de amostra pode precisar de mudanças para seu ambiente.

> [!IMPORTANT]
> No momento, não há suporte para a autenticação baseada em certificado do Azure AD em VMs do Linux.



     
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
> [!div class="nextstepaction"]
> [Habilitar o Azure Disk Encryption para Windows](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Habilitar o Azure Disk Encryption para Linux](azure-security-disk-encryption-linux-aad.md)
