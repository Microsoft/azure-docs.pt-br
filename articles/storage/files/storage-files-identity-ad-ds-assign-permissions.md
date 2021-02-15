---
title: Controlar o acesso aos compartilhamentos de arquivos do Azure-autenticação de AD DS local
description: Saiba como atribuir permissões a uma identidade de Active Directory Domain Services que representa sua conta de armazenamento. Isso permite que você controle o acesso com a autenticação baseada em identidade.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 06/22/2020
ms.author: rogarana
ms.openlocfilehash: af88f0b3403fb80acbb7dacebe293ac583e35799
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91716030"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Parte dois: atribuir permissões de nível de compartilhamento a uma identidade

Antes de começar este artigo, verifique se você concluiu o artigo anterior, [habilite a autenticação AD DS para sua conta](storage-files-identity-ad-ds-enable.md).

Depois de habilitar a autenticação Active Directory Domain Services (AD DS) em sua conta de armazenamento, você deve configurar permissões de nível de compartilhamento para obter acesso aos compartilhamentos de arquivos. A identidade com a qual você deseja acessar recursos de compartilhamento de arquivos do Azure deve ser uma identidade híbrida que existe tanto no AD DS quanto no Azure AD. Por exemplo, digamos que você tenha um usuário em seu AD DS que é user1@onprem.contoso.com e sincronizado com o Azure ad user1@contoso.com usando Azure ad Connect sincronização. Para permitir que esse usuário acesse os arquivos do Azure, você deve atribuir as permissões de nível de compartilhamento ao user1@contoso.com . O mesmo conceito se aplica a grupos ou entidades de serviço. Por isso, você deve sincronizar os usuários e grupos do seu AD DS com o AD do Azure usando a sincronização Azure AD Connect. 

As permissões de nível de compartilhamento devem ser atribuídas à identidade do Azure AD que representa o mesmo usuário ou grupo em seu AD DS para dar suporte à autenticação AD DS para o compartilhamento de arquivos do Azure. A autenticação e a autorização em identidades que só existem no Azure AD, como MSIs (identidades gerenciadas do Azure), não têm suporte com a autenticação AD DS. Este artigo demonstra como atribuir permissões de nível de compartilhamento para um compartilhamento de arquivos a uma identidade.


## <a name="share-level-permissions"></a>Permissões de nível de compartilhamento

Em geral, é recomendável usar permissões de nível de compartilhamento para gerenciamento de acesso de alto nível a um grupo do Azure AD que representa um grupo de usuários e identidades e, em seguida, aproveitar ACLs do Windows para o controle de acesso granular para o nível de diretório/arquivo. 

Há três funções internas do Azure para conceder permissões de nível de compartilhamento aos usuários:

- O **leitor de compartilhamento SMB de dados de arquivo de armazenamento** permite acesso de leitura em compartilhamentos de arquivos de armazenamento do Azure via SMB
- O **colaborador de compartilhamento SMB de dados de arquivo de armazenamento** permite acesso de leitura, gravação e exclusão em compartilhamentos de arquivos de armazenamento do Azure via SMB.
- O **colaborador elevado do compartilhamento SMB de dados de arquivo de armazenamento** permite ler, gravar, excluir e modificar ACLs do Windows em compartilhamentos de arquivos de armazenamento do Azure via SMB.

> [!IMPORTANT]
> O controle administrativo total de um compartilhamento de arquivos, incluindo a capacidade de apropriar-se de um arquivo, requer o uso da chave da conta de armazenamento. O controle administrativo não é compatível com credenciais do Azure AD.

Você pode usar o portal do Azure, Azure PowerShell ou CLI do Azure para atribuir as funções internas à identidade do Azure AD de um usuário para conceder permissões de nível de compartilhamento.

## <a name="assign-an-azure-role"></a>Atribuir uma função do Azure

### <a name="azure-portal"></a>Portal do Azure

Para atribuir uma função do Azure a uma identidade do Azure AD, usando o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. No portal do Azure, vá para o compartilhamento de arquivos ou [crie um compartilhamento de arquivos](storage-how-to-create-file-share.md).
1. Selecione **Controle de Acesso (IAM)** .
1. Selecione **Adicionar uma atribuição de função**
1. Na folha **Adicionar atribuição de função** , selecione a função interna apropriada (leitor de compartilhamento SMB de dados de arquivo de armazenamento, colaborador de compartilhamento SMB de dados de arquivo de armazenamento) na lista de **funções** . Deixe **atribuir acesso à** na configuração padrão: **usuário, grupo ou entidade de serviço do Azure ad**. Selecione a identidade do Azure AD de destino por nome ou endereço de email. **A identidade do Azure AD selecionada deve ser uma identidade híbrida e não pode ser uma identidade somente na nuvem.** Isso significa que a mesma identidade também é representada em AD DS.
1. Selecione **salvar** para concluir a operação de atribuição de função.

### <a name="powershell"></a>PowerShell

O exemplo do PowerShell a seguir mostra como atribuir uma função do Azure a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções do Azure com o PowerShell, consulte [Adicionar ou remover atribuições de função do Azure usando o módulo Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Antes de executar o script de exemplo a seguir, substitua os valores de espaço reservado, incluindo colchetes, pelos valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

### <a name="cli"></a>CLI
  
O comando da CLI 2,0 a seguir atribui uma função do Azure a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções do Azure com CLI do Azure, consulte [Adicionar ou remover atribuições de função do Azure usando o CLI do Azure](../../role-based-access-control/role-assignments-cli.md). 

Antes de executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo colchetes, pelos seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="next-steps"></a>Próximas etapas

Agora que você atribuiu permissões de nível de compartilhamento, deve configurar o diretório e as permissões de nível de arquivo. Vá para o próximo artigo.

[Parte três: configurar permissões de diretório e de nível de arquivo sobre SMB](storage-files-identity-ad-ds-configure-permissions.md)
