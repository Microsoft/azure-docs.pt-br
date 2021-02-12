---
title: incluir arquivo
description: incluir arquivo
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 08/26/2020
ms.author: rogara
ms.custom: include file
ms.openlocfilehash: 4773446ec0007ffbed99bc01939d1f92f5823d99
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95558533"
---
## <a name="assign-access-permissions-to-an-identity"></a>Atribuir permissões de acesso a uma identidade

Para acessar os recursos de arquivos do Azure com autenticação baseada em identidade, uma identidade (um usuário, grupo ou entidade de serviço) deve ter as permissões necessárias no nível de compartilhamento. Esse processo é semelhante à especificação de permissões de compartilhamento do Windows, em que você especifica o tipo de acesso que um usuário específico tem a um compartilhamento de arquivos. As orientações nesta seção demonstram como atribuir ler, gravar ou excluir as permissões para um compartilhamento de arquivos para uma identidade. 

Introduzimos três funções internas do Azure para conceder permissões de nível de compartilhamento aos usuários:

- O **leitor de compartilhamento SMB de dados de arquivo de armazenamento** permite acesso de leitura em compartilhamentos de arquivos de armazenamento do Azure via SMB
- O **colaborador de compartilhamento SMB de dados de arquivo de armazenamento** permite acesso de leitura, gravação e exclusão em compartilhamentos de arquivos de armazenamento do Azure via SMB.
- O **colaborador elevado de compartilhamento SMB de dados de arquivo de armazenamento** permite a leitura, gravação, exclusão e modificação de permissões NTFS em compartilhamentos de arquivos de armazenamento do Azure via SMB.

> [!IMPORTANT]
> O controle administrativo total de um compartilhamento de arquivos, incluindo a capacidade de apropriar-se de um arquivo, requer o uso da chave da conta de armazenamento. O controle administrativo não é compatível com credenciais do Azure AD.

Você pode usar o portal do Azure, o PowerShell ou o CLI do Azure para atribuir as funções internas à identidade do Azure AD de um usuário para conceder permissões de nível de compartilhamento. Lembre-se de que a atribuição de função do Azure no nível de compartilhamento pode levar algum tempo para estar em vigor. 

> [!NOTE]
> Lembre-se de [sincronizar suas credenciais do AD DS com o AD do Azure](../articles/active-directory/hybrid/how-to-connect-install-roadmap.md) se você planeja usar sua AD DS local para autenticação. A sincronização de hash de senha de AD DS para o Azure AD é opcional. A permissão de nível de compartilhamento será concedida à identidade do Azure AD que é sincronizada a partir de seu AD DS local.

A recomendação geral é usar a permissão de nível de compartilhamento para o gerenciamento de acesso de alto nível para um grupo do AD que representa um grupo de usuários e identidades e, em seguida, aproveitar as permissões de NTFS para o controle de acesso granular no nível de diretório/arquivo. 

### <a name="assign-an-azure-role-to-an-ad-identity"></a>Atribuir uma função do Azure a uma identidade do AD

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para atribuir uma função do Azure a uma identidade do Azure AD, usando o [portal do Azure](https://portal.azure.com), siga estas etapas:

1. No portal do Azure, vá para o compartilhamento de arquivos ou [crie um compartilhamento de arquivos](../articles/storage/files/storage-how-to-create-file-share.md).
2. Selecione **Controle de Acesso (IAM)** .
3. Selecione **Adicionar uma atribuição de função**
4. Na folha **Adicionar atribuição de função** , selecione a função interna apropriada (leitor de compartilhamento SMB de dados de arquivo de armazenamento, colaborador de compartilhamento SMB de dados de arquivo de armazenamento) na lista de **funções** . Deixe **atribuir acesso à** na configuração padrão: **usuário, grupo ou entidade de serviço do Azure ad**. Selecione a identidade do Azure AD de destino por nome ou endereço de email.
5. Selecione **salvar** para concluir a operação de atribuição de função.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

O exemplo do PowerShell a seguir mostra como atribuir uma função do Azure a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções do Azure com o PowerShell, consulte [gerenciar o acesso usando RBAC e Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).

Antes de executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo colchetes, pelos seus próprios valores.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)
  
O comando da CLI 2,0 a seguir mostra como atribuir uma função do Azure a uma identidade do Azure AD, com base no nome de entrada. Para obter mais informações sobre como atribuir funções do Azure com CLI do Azure, consulte [gerenciar o acesso usando RBAC e CLI do Azure](../articles/role-based-access-control/role-assignments-cli.md). 

Antes de executar o script de exemplo a seguir, lembre-se de substituir os valores de espaço reservado, incluindo colchetes, pelos seus próprios valores.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="configure-ntfs-permissions-over-smb"></a>Configurar permissões NTFS em SMB

Depois de atribuir permissões no nível de compartilhamento com o RBAC, você deve atribuir permissões de NTFS corretas no nível raiz, de diretório ou de arquivo. Considere as permissões de nível de compartilhamento como o gatekeeper de alto nível que determina se um usuário pode acessar o compartilhamento. Enquanto as permissões de NTFS atuam em um nível mais granular para determinar quais operações o usuário pode fazer no nível do diretório ou do arquivo.

Os arquivos do Azure dá suporte a todo o conjunto de permissões de NTFS básicos e avançados. Você pode exibir e configurar as permissões NTFS em diretórios e arquivos em um compartilhamento de arquivos do Azure montando o compartilhamento e usando o explorador de arquivos do Windows ou executando o comando [icacls](/windows-server/administration/windows-commands/icacls) do Windows ou [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) . 

Para configurar o NTFS com permissões de superusuário, você deve montar o compartilhamento usando sua chave de conta de armazenamento de sua VM ingressada no domínio. Siga as instruções na próxima seção para montar um compartilhamento de arquivos do Azure no prompt de comando e configurar as permissões NTFS adequadamente.

Conjuntos de permissões a seguir têm suporte para o diretório raiz de um compartilhamento de arquivos:

- BUILTIN\Administrators:(OI)(CI)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(Rx)
- BUILTIN\Users:(OI)(CI)(IO)(GR,GE)
- NT AUTHORITY\Authenticated Users:(OI)(CI)(M)
- NT AUTHORITY\SYSTEM:(F)
- CREATOR OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Montar um compartilhamento de arquivos do Azure no prompt de comando

Usar o Windows **net use** comando para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado no exemplo a seguir pelos seus próprios valores. Para obter mais informações sobre como montar compartilhamentos de arquivos, consulte [usar um compartilhamento de arquivos do Azure com o Windows](../articles/storage/files/storage-how-to-use-files-windows.md). 

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}

```

Se você tiver problemas ao conectar-se aos arquivos do Azure, consulte [a ferramenta de solução de problemas que publicamos para erros de montagem de arquivos do Azure no Windows](https://azure.microsoft.com/blog/new-troubleshooting-diagnostics-for-azure-files-mounting-errors-on-windows/). Também fornecemos [orientações](../articles/storage/files/storage-files-faq.md#on-premises-access) para contornar cenários quando a porta 445 é bloqueada. 


### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Configurar permissões NTFS com o explorador de arquivos do Windows

Use o explorador de arquivos do Windows para conceder permissão total a todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz.

1. Abra o explorador de arquivos do Windows e clique com o botão direito do mouse no arquivo/diretório e selecione **Propriedades**.
2. Selecione a guia **Segurança**.
3. Selecione **Editar...** para alterar permissões.
4. Você pode alterar as permissões de usuários existentes ou selecionar **Adicionar...** para conceder permissões a novos usuários.
5. Na janela de prompt para adicionar novos usuários, insira o nome de usuário de destino ao qual você deseja conceder permissão na caixa **Inserir os nomes de objeto a serem selecionados** e selecione **verificar nomes** para localizar o nome UPN completo do usuário de destino.
7.    Selecione **OK**.
8.    Na guia **segurança** , selecione todas as permissões que você deseja conceder ao novo usuário.
9.    Escolha **Aplicar**.

### <a name="configure-ntfs-permissions-with-icacls"></a>Configurar permissões NTFS com icacls

Use o seguinte comando do Windows para conceder permissões completas para todos os diretórios e arquivos no compartilhamento de arquivos, incluindo o diretório raiz. Lembre-se de substituir os valores de espaço reservado no exemplo pelos seus próprios valores.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Para obter mais informações sobre como usar o icacls para definir permissões NTFS e sobre os diferentes tipos de permissões com suporte, consulte [a referência de linha de comando para icacls](/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Montar um compartilhamento de arquivos de uma VM ingressada no domínio

O processo a seguir verifica se as permissões de acesso e compartilhamento de arquivos foram configuradas corretamente e se você pode acessar um compartilhamento de arquivos do Azure de uma VM ingressada no domínio. Lembre-se de que a atribuição de função do Azure no nível de compartilhamento pode levar algum tempo para estar em vigor. 

Entre na VM usando a identidade do Azure AD à qual você concedeu permissões, conforme mostrado na imagem a seguir. Se você habilitou a autenticação de AD DS local para arquivos do Azure, use suas credenciais de AD DS. Para a autenticação de AD DS do Azure, entre com as credenciais do Azure AD.

![Captura de tela mostrando a tela de entrada do Azure AD para autenticação do usuário](media/storage-files-aad-permissions-and-mounting/azure-active-directory-authentication-dialog.png)

Use o comando a seguir para montar o compartilhamento de arquivos do Azure. Lembre-se de substituir os valores de espaço reservado por seus próprios valores. Como você foi autenticado, não precisa fornecer a chave de conta de armazenamento, as credenciais de AD DS locais ou as credenciais de AD DS do Azure. Há suporte para a experiência de logon único para autenticação com AD DS local ou AD DS do Azure. Se você tiver problemas para montar com credenciais de AD DS, consulte [solucionar problemas de arquivos do Azure no Windows](../articles/storage/files/storage-troubleshoot-windows-file-connection-problems.md) para obter diretrizes.

```
$connectTestResult = Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 445
if ($connectTestResult.TcpTestSucceeded)
{
 net use <desired-drive letter>: \\<storage-account-name>.file.core.windows.net\<fileshare-name>
} 
else 
{
 Write-Error -Message "Unable to reach the Azure storage account via port 445. Check to make sure your organization or ISP is not blocking port 445, or use Azure P2S VPN, Azure S2S VPN, or Express Route to tunnel SMB traffic over a different port."
}
```