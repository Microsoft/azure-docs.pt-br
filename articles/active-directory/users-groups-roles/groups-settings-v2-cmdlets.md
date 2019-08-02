---
title: Exemplos do PowerShell para gerenciar grupos e Visualizar write-back de grupo no local-Azure Active Directory | Microsoft Docs
description: Esta página fornece exemplos do PowerShell para ajudar no gerenciamento de seus grupos no Azure Active Directory
keywords: Azure AD, Azure Active Directory, PowerShell, Grupos, Gerenciamento de grupos
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 06/14/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2e22baabda901a34f624cf27c25037ff3ba94e90
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381850"
---
# <a name="azure-active-directory-version-2-cmdlets-for-group-management"></a>Cmdlets da versão 2 do Azure Active Directory para gerenciamento de grupos

> [!div class="op_single_selector"]
> * [Portal do Azure](../fundamentals/active-directory-groups-create-azure-portal.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
> * [PowerShell](groups-settings-v2-cmdlets.md)
>
>

Este artigo contém exemplos de como usar o PowerShell para gerenciar grupos no Azure AD (Azure Active Directory).  Ele também explica como configurar usando o módulo PowerShell do Azure AD. Primeiramente, você deve [baixar o módulo PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="install-the-azure-ad-powershell-module"></a>Instalar o módulo do Powershell do Azure AD

Para instalar o módulo PowerShell do Azure AD, use os seguintes comandos:

```powershell
    PS C:\Windows\system32> install-module azuread
    PS C:\Windows\system32> import-module azuread
```

Para verificar se o módulo está pronto para ser usado, use o seguinte comando:

```powershell
    PS C:\Windows\system32> get-module azuread

    ModuleType Version      Name                                ExportedCommands
    ---------- ---------    ----                                ----------------
    Binary     2.0.0.115    azuread                      {Add-AzureADAdministrati...}
```

Agora você pode começar a usar os cmdlets do módulo. Para obter uma descrição completa dos cmdlets no módulo do Azure AD, veja a documentação de referência online do [PowerShell versão 2 do Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

## <a name="connect-to-the-directory"></a>Conectar ao diretório

Antes de começar a gerenciar grupos usando cmdlets do PowerShell do Azure AD, você deve conectar a sessão do PowerShell ao diretório que deseja gerenciar. Use o seguinte comando:

```powershell
    PS C:\Windows\system32> Connect-AzureAD
```

O cmdlet solicita as credenciais que você deseja usar para acessar o diretório. Neste exemplo, estamos usando karen@drumkit.onmicrosoft.com para acessar o diretório de demonstração. O cmdlet retorna uma confirmação para mostrar que a conexão da sessão com o diretório foi bem-sucedida:

```powershell
    Account                       Environment Tenant
    -------                       ----------- ------
    Karen@drumkit.onmicrosoft.com AzureCloud  85b5ff1e-0402-400c-9e3c-0f…
```

Agora você pode começar a usar os cmdlets do AzureAD para gerenciar grupos no diretório.

## <a name="retrieve-groups"></a>Recuperar grupos

Para recuperar grupos existentes do seu diretório, use o cmdlet Get-AzureADGroups. 

Para recuperar todos os grupos no diretório, use o cmdlet sem parâmetros:

```powershell
    PS C:\Windows\system32> get-azureadgroup
```

O cmdlet retorna todos os grupos no diretório conectado.

Você pode usar o parâmetro -objectID para recuperar um grupo específico, para o qual especifica o objectID do grupo:

```powershell
    PS C:\Windows\system32> get-azureadgroup -ObjectId e29bae11-4ac0-450c-bc37-6dae8f3da61b
```

Agora, o cmdlet retorna o grupo cujo objectID corresponde ao valor do parâmetro inserido:

```powershell
    DeletionTimeStamp            :
    ObjectId                     : e29bae11-4ac0-450c-bc37-6dae8f3da61b
    ObjectType                   : Group
    Description                  :
    DirSyncEnabled               :
    DisplayName                  : Pacific NW Support
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 9bb4139b-60a1-434a-8c0d-7c1f8eee2df9
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Você pode procurar um grupo específico usando o parâmetro -filter. Esse parâmetro usa uma cláusula de filtro ODATA e retorna todos os grupos que correspondem ao filtro, como no exemplo a seguir:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

> [!NOTE]
> Os cmdlets do PowerShell do Azure AD implementam o padrão de consulta OData. Para saber mais, confira **$filter** em [Opções de consulta do sistema OData usando o ponto de extremidade do OData](https://msdn.microsoft.com/library/gg309461.aspx#BKMK_filter).

## <a name="create-groups"></a>Criar grupos

Para criar um novo grupo no seu diretório, use o cmdlet New-AzureADGroup. Esse cmdlet cria um novo grupo de segurança chamado "Marketing":

```powershell
    PS C:\Windows\system32> New-AzureADGroup -Description "Marketing" -DisplayName "Marketing" -MailEnabled $false -SecurityEnabled $true -MailNickName "Marketing"
```

## <a name="update-groups"></a>Atualizar grupos

Para atualizar um grupo existente, use o cmdlet Set-AzureADGroup. Neste exemplo, estamos alterando a propriedade DisplayName do grupo "Intune Administrators". Primeiramente, vamos localizar o grupo usando o cmdlet Get-AzureADGroup e filtrar usando o atributo DisplayName:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"


    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

Em seguida, vamos mudar a propriedade Description para o novo valor "Intune Device Administrators":

```powershell
    PS C:\Windows\system32> Set-AzureADGroup -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -Description "Intune Device Administrators"
```

Agora, se encontrarmos o grupo novamente, veremos que a propriedade Description é atualizada para refletir o novo valor:

```powershell
    PS C:\Windows\system32> Get-AzureADGroup -Filter "DisplayName eq 'Intune Administrators'"

    DeletionTimeStamp            :
    ObjectId                     : 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
    ObjectType                   : Group
    Description                  : Intune Device Administrators
    DirSyncEnabled               :
    DisplayName                  : Intune Administrators
    LastDirSyncTime              :
    Mail                         :
    MailEnabled                  : False
    MailNickName                 : 4dd067a0-6515-4f23-968a-cc2ffc2eff5c
    OnPremisesSecurityIdentifier :
    ProvisioningErrors           : {}
    ProxyAddresses               : {}
    SecurityEnabled              : True
```

## <a name="delete-groups"></a>Excluir grupos

Para excluir grupos do diretório, use o cmdlet Remove-AzureADGroup da seguinte maneira:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroup -ObjectId b11ca53e-07cc-455d-9a89-1fe3ab24566b
```

## <a name="manage-group-membership"></a>Gerenciar associação a um grupo

### <a name="add-members"></a>Adicionar membros

Para adicionar novos membros a um grupo, use o cmdlet Add-AzureADGroupMember. Esse comando adiciona um membro ao grupo Intune Administrators que usamos no exemplo anterior:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

O parâmetro -ObjectId é o ObjectID do grupo ao qual queremos adicionar um membro, e -RefObjectId é o ObjectID do usuário que queremos adicionar como um membro ao grupo.

### <a name="get-members"></a>Obter membros

Para obter os membros existentes de um grupo, use o cmdlet Get-AzureADGroupMember, como neste exemplo:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df

    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          72cd4bbd-2594-40a2-935c-016f3cfeeeea User
                          8120cc36-64b4-4080-a9e8-23aa98e8b34f User
```

### <a name="remove-members"></a>Remover membros

Para remover o membro que adicionamos anteriormente ao grupo, use o cmdlet Remove-AzureADGroupMember, conforme mostrado aqui:

```powershell
    PS C:\Windows\system32> Remove-AzureADGroupMember -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -MemberId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

### <a name="verify-members"></a>Verificar membros

Para verificar as associações de grupo de um usuário, use o cmdlet Select-AzureADGroupIdsUserIsMemberOf. Esse cmdlet utiliza como seus parâmetros o ObjectId do usuário do qual verifica as associações de grupo e uma lista de grupos da qual verifica as associações. A lista de grupos deve ser fornecida na forma de uma variável complexa do tipo "Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck". Desse modo, devemos criar primeiro uma variável com esse tipo:

```powershell
    PS C:\Windows\system32> $g = new-object Microsoft.Open.AzureAD.Model.GroupIdsForMembershipCheck
```

Em seguida, fornecemos valores de groupIds para verificação no atributo "GroupIds" dessa variável complexa:

```powershell
    PS C:\Windows\system32> $g.GroupIds = "b11ca53e-07cc-455d-9a89-1fe3ab24566b", "31f1ff6c-d48c-4f8a-b2e1-abca7fd399df"
```

Agora, se quisermos verificar as associações de grupo de um usuário com o ObjectID 72cd4bbd-2594-40a2-935c-016f3cfeeeea nos grupos em $g, devemos usar:

```powershell
    PS C:\Windows\system32> Select-AzureADGroupIdsUserIsMemberOf -ObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea -GroupIdsForMembershipCheck $g

    OdataMetadata                                                                                                 Value
    -------------                                                                                                  -----
    https://graph.windows.net/85b5ff1e-0402-400c-9e3c-0f9e965325d1/$metadata#Collection(Edm.String)             {31f1ff6c-d48c-4f8a-b2e1-abca7fd399df}
```

O valor retornado é uma lista de grupos dos quais esse usuário é membro. Você também pode aplicar esse método para verificar a associação de Contatos, Grupos ou Entidades de Serviço para uma determinada lista de grupos, usando Select-AzureADGroupIdsContactIsMemberOf, Select-AzureADGroupIdsGroupIsMemberOf ou Select-AzureADGroupIdsServicePrincipalIsMemberOf

## <a name="disable-group-creation-by-your-users"></a>Desativar a criação de grupo por seus usuários

É possível impedir que usuários não administradores criem grupos de segurança. O comportamento padrão no Microsoft Online Directory Services (MSODS) é permitir que usuários não administradores criem grupos, quer o gerenciamento de grupo de autoatendimento (SSGM) também esteja ou não habilitado. A configuração do SSGM controla o comportamento apenas no painel de acesso Meus Aplicativos.

Para desabilitar a criação de grupos por usuários não administradores:

1. Verifique se os usuários não administradores têm permissão para criar grupos:
   
   ```powershell
   PS C:\> Get-MsolCompanyInformation | fl UsersPermissionToCreateGroupsEnabled
   ```
  
2. Se retornar `UsersPermissionToCreateGroupsEnabled : True`, os usuários não administradores podem criar grupos. Para desabilitar esse recurso:
  
   ```powershell 
   Set-MsolCompanySettings -UsersPermissionToCreateGroupsEnabled $False
   ```
  
## <a name="manage-owners-of-groups"></a>Gerenciar proprietários de grupos

Para adicionar proprietários a um grupo, use o cmdlet Add-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Add-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -RefObjectId 72cd4bbd-2594-40a2-935c-016f3cfeeeea
```

O parâmetro-ObjectId é o ObjectID do grupo ao qual queremos adicionar um proprietário, e-RefObjectId é o ObjectID do usuário ou da entidade de serviço que desejamos adicionar como um proprietário do grupo.

Para recuperar os proprietários de um grupo, use o cmdlet Get-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> Get-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df
```

O cmdlet retorna a lista de proprietários (usuários e entidades de serviço) para o grupo especificado:

```powershell
    DeletionTimeStamp ObjectId                             ObjectType
    ----------------- --------                             ----------
                          e831b3fd-77c9-49c7-9fca-de43e109ef67 User
```

Se quiser remover um proprietário de um grupo, use o cmdlet Remove-AzureADGroupOwner:

```powershell
    PS C:\Windows\system32> remove-AzureADGroupOwner -ObjectId 31f1ff6c-d48c-4f8a-b2e1-abca7fd399df -OwnerId e831b3fd-77c9-49c7-9fca-de43e109ef67
```

## <a name="reserved-aliases"></a>Aliases reservados

Quando um grupo é criado, certos pontos de extremidade permitem que o usuário final especifique um mailNickname ou o alias a ser usado como parte do endereço de email do grupo. Os grupos com os seguintes aliases de email altamente privilegiados só podem ser criados por um administrador global do Azure AD. 
  
* abuso
* administrador
* administrator
* hostmaster
* majordomo
* postmaster
* root
* seguro
* segurança
* ssl-admin
* webmaster

## <a name="group-writeback-to-on-premises-preview"></a>Write-back de grupo para local (versão prévia)

Hoje, muitos grupos ainda são gerenciados no local Active Directory. Para responder a solicitações de sincronização de grupos de nuvem de volta para o local, o recurso de write-back de grupos do Office 365 para o Azure AD agora está disponível para visualização.

Os grupos do Office 365 são criados e gerenciados na nuvem. A capacidade de write-back permite que você escreva grupos do Office 365 como grupos de distribuição para uma floresta Active Directory com o Exchange instalado. Os usuários com caixas de correio locais do Exchange podem enviar e receber emails desses grupos. O recurso de write-back de grupo não dá suporte a grupos de segurança ou grupos de distribuição do Azure AD.

Para obter mais detalhes, consulte a documentação para o [serviço de sincronização de Azure ad Connect](../hybrid/how-to-connect-syncservice-features.md).

O Write-back do grupo do Office 365 é um recurso de visualização pública do Azure Active Directory (Azure AD) e está disponível com qualquer plano de licença pago do Azure AD. Para obter informações legais sobre visualizações, consulte [termos de uso suplementares para Microsoft Azure visualizações](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="next-steps"></a>Próximas etapas

Você pode encontrar mais documentação do PowerShell do Azure Active Directory em [Cmdlets do Azure Active Directory](/powershell/azure/install-adv2?view=azureadps-2.0).

* [Gerenciamento de acesso a recursos com grupos do Active Directory do Azure](../fundamentals/active-directory-manage-groups.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
* [Integração de suas identidades locais com o Active Directory do Azure](../hybrid/whatis-hybrid-identity.md?context=azure/active-directory/users-groups-roles/context/ugr-context)
