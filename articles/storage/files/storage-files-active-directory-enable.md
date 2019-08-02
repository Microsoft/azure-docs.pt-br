---
title: Ativar a autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização) - Armazenamento do Azure
description: Saiba como habilitar a autenticação baseada em identidade em SMB (Bloqueio de Mensagens do Servidor) (visualização) para os Arquivos do Azure por meio dos Serviços de Domínio do Azure AD (Azure Active Directory). As máquinas virtuais Windows (VMs) associadas ao domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696119"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>Habilitar a autenticação do serviço de domínio do Active Directory do Azure no SMB para arquivos do Azure (visualização)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para obter uma visão geral da autenticação do AD do Azure em SMB para arquivos do Azure, consulte [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Visão geral do fluxo de trabalho
Antes de habilitar a autenticação do Azure AD DS via SMB para arquivos do Azure, verifique se que o Azure AD e ambientes de armazenamento do Azure estão configurados corretamente. É recomendável que você analise os [pré-requisitos](#prerequisites) para ter certeza de que realizou todas as etapas necessárias. 

Em seguida, conceda acesso aos recursos do Azure Files com credenciais do Azure AD seguindo estas etapas: 

1. Habilite a autenticação do Azure AD DS via SMB para sua conta de armazenamento para registrar a conta de armazenamento com a implantação do Azure AD Domain Services associada.
2. Atribuir permissões de acesso para um compartilhamento a uma identidade do Azure AD (um usuário, grupo ou responsável pelo serviço).
3. Configurar permissões NTFS em SMB para diretórios e arquivos.
4. Montar um compartilhamento de arquivos do Azure de uma VM associada ao domínio.

O diagrama a seguir ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação do Azure AD DS via SMB para arquivos do Azure. 

![Diagrama mostrando o fluxo de trabalho do Azure AD sobre SMB para arquivos do Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Pré-requisitos 

Antes de habilitar o Azure Active Directory no SMB para arquivos do Azure, verifique se que você concluiu os pré-requisitos a seguir:

1.  **Selecione ou crie um locatário do Azure AD.**

    Você pode usar um inquilino novo ou existente para a autenticação do Azure AD em SMB. O locatário e o compartilhamento de arquivos que você deseja acessar devem estar associados à mesma assinatura.

    Para criar um novo locatário do Azure AD, você pode [adicionar um locatário do Azure AD e uma assinatura do Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se você tiver um inquilino do Azure AD existente, mas quiser criar um novo inquilino para uso com os Arquivos do Azure, consulte [Criar um inquilino do Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Ative os Serviços de Domínio do Azure AD no locatário do Azure AD.**

    Para oferecer suporte à autenticação com credenciais do Azure AD, você deve habilitar os Serviços de Domínio do Azure AD para o seu locatário do Azure AD. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [Habilitar os Serviços de Domínio do Active Directory do Azure usando o portal do Azure](../../active-directory-domain-services/create-instance.md).

    Normalmente leva cerca de 15 minutos para uma implantação do Azure AD Domain Services concluir. Verifique se o status de integridade dos Serviços de Domínio do AD do Azure exibe **Em Execução**, com a sincronização de senha ativada, antes de prosseguir para a próxima etapa.

3.  **Domine e associe uma VM do Azure aos Serviços de Domínio do Azure AD.**

    Para acessar um compartilhamento de arquivo usando as credenciais do Azure AD de uma VM, sua VM deve estar associada ao domínio aos Serviços de Domínio do Azure AD. Para obter mais informações sobre como ingressar no domínio de uma VM, consulte [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Autenticação do AD DS do Azure no SMB com arquivos do Azure tem suporte apenas em VMs do Azure em execução em versões do sistema operacional acima do Windows 7 ou Windows Server 2008 R2.

4.  **Selecione ou crie um compartilhamento de arquivos do Azure.**

    Selecione um compartilhamento de arquivos novo ou existente associado à mesma assinatura que o seu locatário do Azure AD. Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [Criar um compartilhamento de arquivos no Azure Files](storage-how-to-create-file-share.md). 
    Para um desempenho ideal, a Microsoft recomenda que o compartilhamento de arquivos esteja na mesma região da VM da qual você planeja acessar o compartilhamento.

5.  **Verifique a conectividade de arquivos do Azure montando compartilhamentos de arquivos do Azure usando a chave da sua conta de armazenamento.**

    Para verificar se sua VM e seu compartilhamento de arquivos estão configurados corretamente, tente montar o compartilhamento de arquivos usando sua chave de conta de armazenamento. Para obter mais informações, consulte [Montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Habilitar a autenticação do Azure AD DS para sua conta

Para habilitar a autenticação do Azure AD DS via SMB para arquivos do Azure, você pode definir uma propriedade em contas de armazenamento criadas após 24 de setembro de 2018, usando o portal do Azure, Azure PowerShell ou CLI do Azure. A configuração dessa propriedade registra a conta de armazenamento com a implantação dos Serviços de Domínio do Azure AD associados. Autenticação do AD DS do Azure no SMB é habilitada para todos os compartilhamentos de arquivos novos e existentes na conta de armazenamento. 

Tenha em mente que você pode habilitar a autenticação do Azure AD DS no SMB somente depois que você implantou com êxito do Azure AD Domain Services para seu locatário do AD do Azure. Para obter mais informações, consulte o [pré-requisitos](#prerequisites).

### <a name="azure-portal"></a>Portal do Azure

Para habilitar a autenticação do Azure AD DS via SMB usando o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. No portal do Azure, navegue até sua conta de armazenamento existente, ou [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).
2. Na seção **Configurações**, selecione **Configuração**.
3. Habilitar **autenticação do Microsoft Azure Active Directory para arquivos do Azure (visualização)** .

A imagem a seguir mostra como habilitar a autenticação do Azure Active Directory no SMB para sua conta de armazenamento.

![Habilitar a autenticação do Azure Active Directory no SMB no portal do Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Para habilitar a autenticação do Azure AD DS no SMB usando o Azure PowerShell: Primeiro, instale o módulo mais recente do Az (2,4 ou mais recente) ou o módulo Az.Storage (1.5 ou mais recente). Para obter mais informações sobre como instalar o PowerShell, consulte [instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Em seguida, crie um novo armazenamento de conta e, em seguida, chame [AzStorageAccount conjunto](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) e defina o **EnableAzureActiveDirectoryDomainServicesForFile** parâmetro **true**. No exemplo abaixo, lembre-se de substituir os valores de espaço reservado por seus próprios valores. (Se você estivesse usando o módulo de visualização anterior, o parâmetro para a habilitação do recurso é **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
Para habilitar esse recurso em contas de armazenamento existente, use o seguinte comando:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>CLI do Azure

Para habilitar a autenticação do Azure Active Directory no SMB da CLI 2.0 do Azure, primeiro instale o `storage-preview` extensão:

```cli-interactive
az extension add --name storage-preview
```
  
Em seguida, crie um novo armazenamento de conta e, em seguida, chame [Atualização de conta de armazenamento az](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) e defina a `--file-aad` propriedade para **verdadeiro**. No exemplo abaixo, lembre-se de substituir os valores de espaço reservado por seus próprios valores.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade 

Para acessar os recursos do Azure Files usando credenciais do Azure AD, uma identidade (um usuário, grupo ou serviço principal) deve ter as permissões necessárias no nível do compartilhamento. Esse processo é semelhante à especificação de permissões do Windows Share, em que você especifica o tipo de acesso que um determinado usuário tem a um compartilhamento de arquivos. As orientações nesta seção demonstram como atribuir ler, gravar ou excluir as permissões para um compartilhamento de arquivos para uma identidade.

Apresentamos duas funções internas do Azure para conceder permissões de nível de compartilhamento para os usuários: Leitor de compartilhamento SMB do dados de arquivo de armazenamento e colaborador de compartilhamento SMB do dados de arquivo de armazenamento. 

- **Leitor de compartilhamento SMB do dados de arquivo de armazenamento** permite acesso de leitura em compartilhamentos de arquivos do armazenamento do Azure no SMB
- **Colaborador de compartilhamento SMB do dados de arquivo de armazenamento** permite a leitura, gravação e acesso de exclusão em compartilhamentos de arquivos do armazenamento do Azure no SMB

> [!IMPORTANT]
> O controle administrativo total de um compartilhamento de arquivos, incluindo a capacidade de atribuir uma função a uma identidade, requer o uso da chave da conta de armazenamento. O controle administrativo não é compatível com credenciais do Azure AD. 

Você pode usar o portal do Azure, PowerShell ou CLI do Azure para atribuir as funções internas a identidade do Azure AD de um usuário para conceder permissões de nível de compartilhamento. 

#### <a name="azure-portal"></a>Portal do Azure
Para atribuir uma função RBAC para uma identidade do AD do Azure, usando o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. No portal do Azure, navegue até o compartilhamento de arquivos, ou [criar um compartilhamento de arquivos no arquivos do Azure](storage-how-to-create-file-share.md).
2. Selecione **Controle de Acesso (IAM)** .
3. Selecione **adicionar uma atribuição de função**
4. No **Adicionar atribuição de função** folha, selecione a função interna apropriada (armazenamento de arquivo de dados SMB compartilhamento leitor, colaborador de compartilhamento SMB do dados de arquivo de armazenamento) a **função** lista suspensa. Manter o **atribuir acesso a** como padrão: "Usuário do AD do azure, grupo ou entidade de serviço" e selecione o identidade do Azure AD de destino por nome ou endereço de email. 
5. Por fim, selecione **salvar** para concluir a operação de atribuição de função.

#### <a name="powershell"></a>PowerShell

O comando PowerShell a seguir mostra como atribuir uma função de RBAC a uma identidade do AD do Azure, com base no nome de usuário. Para obter mais informações sobre como atribuir funções do RBAC ao PowerShell, consulte [Gerenciar acesso usando o RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Ao executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo os colchetes, com seus próprios valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
O seguinte comando da CLI 2.0 mostra como atribuir uma função de RBAC a uma identidade do AD do Azure, com base no nome de usuário. Para obter mais informações sobre como atribuir funções do RBAC à CLI do Azure, consulte [Gerenciar acesso usando o RBAC e o Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Ao executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo os colchetes, com seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Configurar permissões NTFS em SMB 
Depois de atribuir permissões no nível de compartilhamento com o RBAC, você deve atribuir permissões de NTFS corretas no nível raiz, de diretório ou de arquivo. Pense nas permissões de nível de compartilhamento como o gatekeeper de alto nível que determina se um usuário pode acessar o compartilhamento, enquanto as permissões de NTFS agem em um nível mais granular para determinar quais operações o usuário pode executar no nível de diretório ou arquivo. 

Os arquivos do Azure dá suporte a todo o conjunto de permissões de NTFS básicos e avançados. Você pode exibir e configurar permissões NTFS em diretórios e arquivos em um compartilhamento de arquivos do Azure, montando o compartilhamento e, em seguida, executando o comando [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) ou [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) do Windows. 

> [!NOTE]
> A versão de visualização dá suporte a permissões de exibição somente com o Explorador de arquivos do Windows. Ainda não há suporte para a edição de permissões.

Para configurar permissões NTFS com privilégios de superusuário, você deve montar o compartilhamento com sua chave de conta de armazenamento de sua VM ingressado no domínio. Siga as instruções na próxima seção para montar um compartilhamento de arquivos do Azure do prompt de comando e configurar permissões NTFS apropriadas.

Conjuntos de permissões a seguir têm suporte para o diretório raiz de um compartilhamento de arquivos:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(Rx)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montar um compartilhamento de arquivos do Azure no prompt de comando

Usar o Windows **net use** comando para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores. Para obter mais informações sobre a montagem de compartilhamentos de arquivos, consulte [Montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurar permissões NTFS com icacls
Use o seguinte comando do Windows para conceder permissões completas para todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz. Lembre-se de substituir os valores de marcadores mostrados entre parênteses no exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como usar icacls para configurar permissões NTFS e sobre o tipo diferente de permissões suportadas, consulte a [referência de linha de comando para icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montar um compartilhamento de arquivos de uma VM ingressada no domínio 

Agora você está pronto para verificar se concluiu as etapas acima usando as credenciais do Azure AD para acessar um compartilhamento de arquivos do Azure a partir de uma VM associada ao domínio. Primeiro, faça login na VM usando a identidade do Azure AD para a qual você concedeu permissões, conforme mostrado na imagem a seguir.

![Captura de tela mostrando a tela de entrada do Azure AD para autenticação do usuário](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Em seguida, use o seguinte comando para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado por seus próprios valores. Como você já foi autenticado, não é necessário fornecer a chave da conta de armazenamento ou o nome de usuário e a senha do Azure AD. O Azure AD sobre SMB oferece suporte a uma experiência de logon único usando as credenciais do Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Agora você habilitou com êxito a autenticação do Azure AD em SMB e atribuiu uma função personalizada que fornece acesso a um compartilhamento de arquivos para uma identidade do Azure AD. Para conceder acesso ao seu compartilhamento de arquivos a outros usuários, siga as instruções fornecidas nas etapas 2 e 3.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre os arquivos do Azure e usar o Azure AD sobre o SMB, consulte estes recursos:

- [Introdução aos Arquivos do Azure](storage-files-introduction.md)
- [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](storage-files-active-directory-overview.md)
- [Perguntas frequentes](storage-files-faq.md)
