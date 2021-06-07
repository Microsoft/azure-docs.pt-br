---
title: Restringir permissões de acesso de usuário convidado-Azure Active Directory | Microsoft Docs
description: Restringir as permissões de acesso de usuário convidado usando o portal do Azure, o PowerShell ou o Microsoft Graph no Azure Active Directory
services: active-directory
author: curtand
ms.author: curtand
manager: daveba
ms.date: 01/14/2020
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf2d0d3335468147575eb53a99940866baa18375
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222514"
---
# <a name="restrict-guest-access-permissions-preview-in-azure-active-directory"></a>Restringir as permissões de acesso de convidado (versão prévia) no Azure Active Directory

O Azure Active Directory (AD do Azure) permite restringir o que os usuários convidados externos podem ver em sua organização no Azure AD. Os usuários convidados são definidos como um nível de permissão limitado por padrão no Azure AD, enquanto o padrão para usuários Membros é o conjunto completo de permissões de usuário padrão. Esta é uma visualização de um novo nível de permissão de usuário convidado nas configurações de colaboração externa da sua organização do Azure AD para obter um acesso ainda mais restrito, de modo que suas opções de acesso de convidado agora são:

Nível de permissão         | Nível de acesso
----------------         | ------------
Igual aos usuários Membros     | Os convidados têm o mesmo acesso aos recursos do Azure AD como usuários Membros
Acesso limitado (padrão) | Os convidados podem ver a associação de todos os grupos não ocultos
**Acesso restrito (novo)**  | **Convidados não podem ver a associação de nenhum grupo**

Quando o acesso de convidado é restrito, os convidados podem exibir apenas seu próprio perfil de usuário. A permissão para exibir outros usuários não é permitida mesmo se o convidado estiver pesquisando por nome UPN ou objectId. O acesso restrito também restringe os usuários convidados de ver a Associação dos grupos em que estão. Para obter mais informações sobre as permissões gerais de usuário padrão, incluindo permissões de usuário convidado, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../fundamentals/users-default-permissions.md).

## <a name="permissions-and-licenses"></a>Permissões e licenças

Você deve estar na função de administrador global para definir as configurações de colaboração externa. Não há requisitos de licenciamento adicionais para restringir o acesso de convidado.

## <a name="update-in-the-azure-portal"></a>Atualizar no portal do Azure

Fizemos alterações nos controles de portal do Azure existentes para permissões de usuário convidado.

1. Entre no centro de [Administração do Azure ad](https://aad.portal.azure.com) com permissões de administrador global.
1. Na página Visão geral do **Azure Active Directory** da sua organização, selecione **configurações do usuário**.
1. Em **usuários externos**, selecione **gerenciar configurações de colaboração externas**.
1. Na página **configurações de colaboração externas** , selecione **acesso de usuário convidado é restrito a propriedades e associações de sua própria opção de objetos de diretório** .

    ![Página de configurações de colaboração externa do Azure AD](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. Clique em **Salvar**. As alterações podem levar até 15 minutos para entrar em vigor para os usuários convidados.

## <a name="update-with-the-microsoft-graph-api"></a>Atualizar com a API de Microsoft Graph

Adicionamos uma nova API de Microsoft Graph para configurar permissões de convidado em sua organização do Azure AD. As seguintes chamadas à API podem ser feitas para atribuir qualquer nível de permissão. O valor de guestUserRoleId usado aqui é ilustrar a configuração de usuário convidado mais restrita. Para obter mais informações sobre como usar o Microsoft Graph para definir permissões de convidado, consulte [tipo de recurso authorizationPolicy](/graph/api/resources/authorizationpolicy).

### <a name="configuring-for-the-first-time"></a>Configurando pela primeira vez

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A resposta deve ser bem-sucedida 204.

### <a name="updating-the-existing-value"></a>Atualizando o valor existente

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

A resposta deve ser bem-sucedida 204.

### <a name="view-the-current-value"></a>Exibir o valor atual

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

Resposta de exemplo:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>Atualizar com cmdlets do PowerShell

Com esse recurso, adicionamos a capacidade de configurar as permissões restritas por meio de cmdlets do PowerShell v2. O cmdlet Get e set do PowerShell foram publicados na versão 2.0.2.85.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Comando Get: Get-AzureADMSAuthorizationPolicy

Exemplo:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Comando Set: Set-AzureADMSAuthorizationPolicy

Exemplo:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> Você deve inserir authorizationPolicy como a ID quando solicitado.

## <a name="supported-microsoft-365-services"></a>Serviços Microsoft 365 com suporte

### <a name="supported-services"></a>Serviços com suporte

Por suporte, queremos dizer que a experiência é conforme o esperado; especificamente, essa é a mesma experiência de convidado atual.

- Teams
- Outlook (OWA)
- SharePoint
- Planejador nas equipes
- Aplicativo Web do Planner

### <a name="services-currently-not-supported"></a>Serviços atualmente sem suporte

O serviço sem suporte atual pode ter problemas de compatibilidade com a nova configuração de restrição de convidado.

- Formulários
- Aplicativo móvel do Planner
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>Perguntas frequentes

Pergunta | Resposta
-------- | ------
Onde se aplicam essas permissões? | Essas permissões de nível de diretório são aplicadas nos serviços e portais do Azure AD, incluindo o Microsoft Graph, o PowerShell v2, o portal do Azure e o portal de meus aplicativos. Os serviços Microsoft 365 aproveitando grupos de Microsoft 365 para cenários de colaboração também são afetados, especificamente o Outlook, o Microsoft Teams e o SharePoint.
Como as permissões restritas afetam quais grupos convidados podem ver? | Independentemente das permissões de convidado padrão ou restritas, os convidados não podem enumerar a lista de grupos ou usuários. Os convidados podem ver os grupos dos quais são membros no portal do Azure e no portal meus aplicativos, dependendo das permissões:<li>**Permissões padrão**: para localizar os grupos dos quais eles são membros na portal do Azure, o convidado deve pesquisar sua ID de objeto na lista **todos os usuários** e, em seguida, selecionar **grupos**. Aqui, eles podem ver a lista de grupos dos quais eles são membros, incluindo todos os detalhes do grupo, incluindo nome, email e assim por diante. No portal meus aplicativos, eles podem ver uma lista de grupos dos quais eles possuem e grupos dos quais eles são membros.</li><li>**Permissões de convidado restritas**: no portal do Azure, eles ainda podem encontrar a lista de grupos dos quais eles são membros, pesquisando sua ID de objeto na lista todos os usuários e, em seguida, selecionar grupos. Eles só podem ver detalhes muito limitados sobre o grupo, notavelmente a ID do objeto. Por design, as colunas nome e email estão em branco e o tipo de grupo não é reconhecido. No portal meus aplicativos, eles não podem acessar a lista de grupos dos quais eles possuem ou grupos dos quais eles são membros.</li><br>Para obter uma comparação mais detalhada das permissões de diretório provenientes da API do Graph, consulte [permissões de usuário padrão](../fundamentals/users-default-permissions.md#member-and-guest-users).
Quais partes do portal meus aplicativos serão afetadas por esse recurso? | A funcionalidade grupos no portal meus aplicativos respeitará essas novas permissões. Isso inclui todos os caminhos para exibir a lista de grupos e as associações de grupo em meus aplicativos. Nenhuma alteração foi feita na disponibilidade do bloco do grupo. A disponibilidade de bloco do grupo ainda é controlada pela configuração de grupo existente no portal do Azure.
Essas permissões substituem as configurações de convidado do SharePoint ou do Microsoft Teams? | Não. Essas configurações existentes ainda controlam a experiência e o acesso nesses aplicativos. Por exemplo, se você vir problemas no SharePoint, verifique as configurações de compartilhamento externas.
Quais são os problemas de compatibilidade conhecidos no Planner e no Yammer? | <li>Com as permissões definidas como ' Restricted ', os convidados conectados ao aplicativo móvel do planejador não poderão acessar seus planos ou tarefas.<li>Com as permissões definidas como ' Restricted ', os convidados conectados ao Yammer não poderão sair do grupo.
Minhas permissões de convidado existentes serão alteradas no meu locatário? | Nenhuma alteração foi feita nas configurações atuais. Mantemos compatibilidade com versões anteriores com suas configurações existentes. Você decide quando deseja fazer alterações.
Essas permissões serão definidas por padrão? | Não. As permissões padrão existentes permanecem inalteradas. Opcionalmente, você pode definir as permissões para serem mais restritivas.
Há algum requisito de licença para esse recurso? | Não, não há nenhum novo requisito de licenciamento com esse recurso.

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre as permissões de convidado existentes no Azure AD, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../fundamentals/users-default-permissions.md)
- Para ver os métodos de API de Microsoft Graph para restringir o acesso de convidado, consulte [tipo de recurso authorizationPolicy](/graph/api/resources/authorizationpolicy)
- Para revogar todo o acesso de um usuário, consulte [revogar o acesso do usuário no Azure ad](users-revoke-access.md)