---
title: Ativar a autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização) - Armazenamento do Azure
description: Saiba como habilitar a autenticação baseada em identidade em SMB (Bloqueio de Mensagens do Servidor) (visualização) para os Arquivos do Azure por meio dos Serviços de Domínio do Azure AD (Azure Active Directory). As máquinas virtuais Windows (VMs) associadas ao domínio podem acessar os compartilhamentos de arquivos do Azure usando as credenciais do Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 01/02/2019
ms.author: rogarana
ms.openlocfilehash: 974a4341bd140da60c5e229a644657fe7ab02535
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766411"
---
# <a name="enable-azure-active-directory-authentication-over-smb-for-azure-files-preview"></a>Ativar a autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Para obter uma visão geral da autenticação do AD do Azure em SMB para arquivos do Azure, consulte [Visão geral da autenticação do Active Directory do Azure em SMB para arquivos do Azure (visualização)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Visão geral do fluxo de trabalho
Antes de habilitar o Azure AD em SMB para arquivos do Azure, verifique se os ambientes do Azure AD e do Armazenamento do Azure estão configurados corretamente. É recomendável que você analise os [pré-requisitos](#prerequisites) para ter certeza de que realizou todas as etapas necessárias. 

Em seguida, conceda acesso aos recursos do Azure Files com credenciais do Azure AD seguindo estas etapas: 

1. Ative a autenticação do Azure AD em SMB para que sua conta de armazenamento registre a conta de armazenamento com a implantação associada dos Serviços de Domínio do Azure AD.
2. Atribuir permissões de acesso para um compartilhamento a uma identidade do Azure AD (um usuário, grupo ou responsável pelo serviço).
3. Configurar permissões NTFS em SMB para diretórios e arquivos.
4. Montar um compartilhamento de arquivos do Azure de uma VM associada ao domínio.

O diagrama abaixo ilustra o fluxo de trabalho de ponta a ponta para habilitar a autenticação do Azure AD em SMB para arquivos do Azure. 

![Diagrama mostrando o fluxo de trabalho do Azure AD sobre SMB para arquivos do Azure](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Pré-requisitos 

Antes de habilitar o Azure Active Directory no SMB para arquivos do Azure, verifique se que você concluiu os pré-requisitos a seguir:

1.  **Selecione ou crie um locatário do Azure AD.**

    Você pode usar um inquilino novo ou existente para a autenticação do Azure AD em SMB. O locatário e o compartilhamento de arquivos que você deseja acessar devem estar associados à mesma assinatura.

    Para criar um novo locatário do Azure AD, você pode [adicionar um locatário do Azure AD e uma assinatura do Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Se você tiver um inquilino do Azure AD existente, mas quiser criar um novo inquilino para uso com os Arquivos do Azure, consulte [Criar um inquilino do Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Ative os Serviços de Domínio do Azure AD no locatário do Azure AD.**

    Para oferecer suporte à autenticação com credenciais do Azure AD, você deve habilitar os Serviços de Domínio do Azure AD para o seu locatário do Azure AD. Se você não for o administrador do locatário do Azure AD, entre em contato com o administrador e siga as orientações passo a passo para [Habilitar os Serviços de Domínio do Active Directory do Azure usando o portal do Azure](../../active-directory-domain-services/active-directory-ds-getting-started.md).

    Normalmente leva cerca de 15 minutos para uma implantação do Azure AD Domain Services concluir. Verifique se o status de integridade dos Serviços de Domínio do AD do Azure exibe **Em Execução**, com a sincronização de senha ativada, antes de prosseguir para a próxima etapa.

3.  **Domine e associe uma VM do Azure aos Serviços de Domínio do Azure AD.**

    Para acessar um compartilhamento de arquivo usando as credenciais do Azure AD de uma VM, sua VM deve estar associada ao domínio aos Serviços de Domínio do Azure AD. Para obter mais informações sobre como ingressar no domínio de uma VM, consulte [Ingressar uma máquina virtual do Windows Server em um domínio gerenciado](../../active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal.md).

    > [!NOTE]
    > A autenticação do Azure AD em SMB com arquivos do Azure é suportada somente em VMs do Azure em execução nas versões do sistema operacional acima do Windows 7 ou do Windows Server 2008 R2.

4.  **Selecione ou crie um compartilhamento de arquivos do Azure.**

    Selecione um compartilhamento de arquivos novo ou existente associado à mesma assinatura que o seu locatário do Azure AD. Para obter informações sobre como criar um novo compartilhamento de arquivos, consulte [Criar um compartilhamento de arquivos no Azure Files](storage-how-to-create-file-share.md). 
    Para um desempenho ideal, a Microsoft recomenda que o compartilhamento de arquivos esteja na mesma região da VM da qual você planeja acessar o compartilhamento.

5.  **Verifique a conectividade de arquivos do Azure montando compartilhamentos de arquivos do Azure usando a chave da sua conta de armazenamento.**

    Para verificar se sua VM e seu compartilhamento de arquivos estão configurados corretamente, tente montar o compartilhamento de arquivos usando sua chave de conta de armazenamento. Para obter mais informações, consulte [Montar um compartilhamento de arquivos do Azure e acessar o compartilhamento no Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-authentication-for-your-account"></a>Habilitar a autenticação do Microsoft Azure Active Directory para sua conta

Para habilitar a autenticação do Azure Active Directory no SMB para arquivos do Azure, você pode definir uma propriedade em contas de armazenamento criadas após 24 de setembro de 2018, usando o portal do Azure, Azure PowerShell ou CLI do Azure. A configuração dessa propriedade registra a conta de armazenamento com a implantação dos Serviços de Domínio do Azure AD associados. A autenticação do AD do Azure em SMB é habilitada para todos os compartilhamentos de arquivos novos e existentes na conta de armazenamento. 

Lembre-se de que você pode habilitar a autenticação do Azure AD somente em SMB após ter implantado com êxito os Serviços de Domínio do Azure AD em seu locatário do Azure AD. Para obter mais informações, consulte o [pré-requisitos](#prerequisites).

### <a name="azure-portal"></a>Portal do Azure

Para habilitar a autenticação do Azure Active Directory via SMB usando o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. No portal do Azure, navegue até sua conta de armazenamento existente, ou [criar uma conta de armazenamento](../common/storage-quickstart-create-account.md).
2. Na seção **Configurações**, selecione **Configuração**.
3. Habilitar **autenticação do Microsoft Azure Active Directory para arquivos do Azure (visualização)**.

A imagem a seguir mostra como habilitar a autenticação do Azure Active Directory no SMB para sua conta de armazenamento.

![Habilitar a autenticação do Azure Active Directory no SMB no portal do Azure](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Para habilitar a autenticação do Azure AD no SMB do Azure PowerShell, primeiro instale um build de versão prévia do módulo `Az.Storage` com suporte do Azure AD. Para obter mais informações sobre como instalar o PowerShell, consulte [Instalar o Azure PowerShell no Windows com o PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Em seguida, crie uma nova conta de armazenamento, chame [Set-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) e defina o parâmetro **EnableAzureFilesAadIntegrationForSMB** como **true**. No exemplo abaixo, lembre-se de substituir os valores de espaço reservado por seus próprios valores.

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureFilesAadIntegrationForSMB $true

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureFilesAadIntegrationForSMB $true```
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

# Update an existing storage account
# Supported for storage accounts created after September 24, 2018 only
az storage account update -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade 

Para acessar os recursos do Azure Files usando credenciais do Azure AD, uma identidade (um usuário, grupo ou serviço principal) deve ter as permissões necessárias no nível do compartilhamento. As orientações nesta seção demonstram como atribuir ler, gravar ou excluir as permissões para um compartilhamento de arquivos para uma identidade.

> [!IMPORTANT]
> O controle administrativo total de um compartilhamento de arquivos, incluindo a capacidade de atribuir uma função a uma identidade, requer o uso da chave da conta de armazenamento. O controle administrativo não é compatível com credenciais do Azure AD. 

### <a name="define-a-custom-role"></a>Definir uma função personalizada

Para conceder permissões no nível do compartilhamento, defina uma função RBAC personalizada e atribua-a a uma identidade, definindo-a para um compartilhamento de arquivo específico. Esse processo é semelhante à especificação de permissões do Windows Share, em que você especifica o tipo de acesso que um determinado usuário tem a um compartilhamento de arquivos.  

Os modelos mostrados nas seções a seguir fornecem as permissões Ler ou Alterar para um compartilhamento de arquivos. Para definir uma função personalizada, crie um arquivo JSON e copie o modelo apropriado para esse arquivo. Para obter mais informações sobre como definir funções personalizadas do RBAC, consulte [Funções personalizadas no Azure](../../role-based-access-control/custom-roles.md).

**Definição de função para permissões de alteração em nível de compartilhamento**  
O modelo de função personalizado a seguir fornece permissões de alteração no nível do compartilhamento, concedendo acesso de leitura, gravação e exclusão de identidade ao compartilhamento.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read, write and delete access to Azure File Share over SMB",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
        "Microsoft.Authorization/*/Write",
        "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [
    "*"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

**Definição de função para permissões de leitura em nível de compartilhamento**  
O modelo de função personalizado a seguir fornece permissões de leitura no nível do compartilhamento, concedendo um acesso de leitura de identidade ao compartilhamento.

```json
{
  "Name": "<Custom-Role-Name>",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure File Share over SMB",
  "Actions": [
    "*/read"
  ],
  "DataActions": [
    "*/read"
  ],
  "AssignableScopes": [
        "/subscriptions/<Subscription-ID>"
  ]
}
```

### <a name="create-the-custom-role"></a>Criar a função personalizada

Para criar a função personalizada, use o PowerShell ou a CLI do Azure. 

#### <a name="powershell"></a>PowerShell

O seguinte comando do PowerShell cria uma função personalizada com base em um dos modelos de exemplo.

```powershell
#Create a custom role based on the sample template above
New-AzRoleDefinition -InputFile "<custom-role-def-json-path>"
```

#### <a name="cli"></a>CLI 

O seguinte comando da CLI do Azure cria uma função personalizada com base em um dos modelos de exemplo.

```azurecli-interactive
#Create a custom role based on the sample templates above
az role definition create --role-definition "<Custom-role-def-JSON-path>"
```

### <a name="assign-the-custom-role-to-the-target-identity"></a>Atribuir a função personalizada à entidade de serviço

Em seguida, use o PowerShell ou a CLI do Azure para atribuir a função personalizada a uma identidade do Azure Active Directory. 

#### <a name="powershell"></a>PowerShell

O comando PowerShell a seguir mostra como listar as funções personalizadas disponíveis e, em seguida, atribuir uma função personalizada para uma identidade do Azure Active Directory, com base no nome de usuário. Para obter mais informações sobre como atribuir funções do RBAC ao PowerShell, consulte [Gerenciar acesso usando o RBAC e o Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Ao executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo os colchetes, com seus próprios valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>"
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>CLI
  
O comando da CLI 2.0 a seguir mostra como listar as funções personalizadas disponíveis e, em seguida, atribuir uma função personalizada para uma identidade do Azure Active Directory, com base no nome de usuário. Para obter mais informações sobre como atribuir funções do RBAC à CLI do Azure, consulte [Gerenciar acesso usando o RBAC e o Azure CLI](../../role-based-access-control/role-assignments-cli.md). 

Ao executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo os colchetes, com seus próprios valores.

```azurecli-interactive
#List the custom roles
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.roleName, "description":.description, "roleType":.roleType}'
#Assign the custom role to the target identity
az role assignment create --role "<custom-role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
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
