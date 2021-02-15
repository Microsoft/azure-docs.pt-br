---
title: Permissões de função personalizada para registro de aplicativo-Azure AD | Microsoft Docs
description: Delegar permissões de função de administrador personalizado para gerenciar registros de aplicativo.
services: active-directory
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6eb854c8b9ac3b4ee527c9a117edde13479bfeb4
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2021
ms.locfileid: "98740186"
---
# <a name="application-registration-permissions-for-custom-roles-in-azure-active-directory"></a>Permissões de registro de aplicativo para funções personalizadas no Azure Active Directory

Este artigo contém as permissões de registro de aplicativo disponíveis no momento para definições de função personalizadas no Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-tenant-applications"></a>Permissões para gerenciar aplicativos de locatário único

Ao escolher as permissões para sua função personalizada, você tem a opção de conceder acesso para gerenciar somente aplicativos de locatário único. os aplicativos de locatário único estão disponíveis somente para usuários na organização do Azure AD em que o aplicativo está registrado. os aplicativos de locatário único são definidos como tendo os **tipos de conta com suporte** definidos como "contas neste diretório organizacional somente". Na API do Graph, os aplicativos de locatário único têm a propriedade signInAudience definida como "AzureADMyOrg".

Para conceder acesso para gerenciar somente aplicativos de locatário único, use as permissões abaixo com o subtipo **Applications. MyOrganization**. Por exemplo, Microsoft. Directory/Applications. MyOrganization/Basic/Update.

Consulte a [visão geral das funções personalizadas](custom-overview.md) para obter uma explicação do que significa o subtipo de termos gerais, a permissão e o conjunto de propriedades. As informações a seguir são específicas para os registros do aplicativo.

### <a name="create-and-delete"></a>Criar e excluir

Há duas permissões disponíveis para conceder a capacidade de criar registros de aplicativo, cada um com comportamento diferente:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

A atribuição dessa permissão faz com que o criador seja adicionado como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado será contabilizado na cota de objetos criados do 250 do criador.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

A atribuição dessa permissão faz com que o criador não seja adicionado como o primeiro proprietário do registro do aplicativo criado, e o registro do aplicativo criado não será contabilizado na cota de objetos criados do 250 do criador. Use essa permissão com cuidado, pois não há nada impedindo que o destinatário crie registros de aplicativo até que a cota de nível de diretório seja atingida. Se ambas as permissões forem atribuídas, essa permissão terá precedência.

Se ambas as permissões forem atribuídas, a permissão/CREATE terá precedência. Embora a permissão/createAsOwner não adicione automaticamente o criador como o primeiro proprietário, os proprietários podem ser especificados durante a criação do registro do aplicativo ao usar as APIs do Graph ou os cmdlets do PowerShell.

Criar permissões concedem acesso ao **novo** comando de registro.

[Essas permissões concedem acesso ao novo comando do portal de registro](./media/custom-available-permissions/new-custom-role.png)

Há duas permissões disponíveis para conceder a capacidade de excluir registros de aplicativo:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Concede a capacidade de excluir registros de aplicativo, independentemente do subtipo; ou seja, aplicativos de locatário único e multilocatário.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>Microsoft. Directory/Applications. myorganizacionai/Delete

Concede a capacidade de excluir registros de aplicativo restritos àqueles que são acessíveis somente para contas em sua organização ou aplicativos de locatário único (subtipo MyOrganization).

![Essas permissões concedem acesso ao comando excluir aplicativo de registro](./media/custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Ao atribuir uma função que contém permissões de criação, a atribuição de função deve ser feita no escopo do diretório. Uma permissão de criação atribuída a um escopo de recurso não concede a capacidade de criar registros de aplicativo.

### <a name="read"></a>Ler

Todos os usuários Membros na organização podem ler informações de registro do aplicativo por padrão. No entanto, os usuários convidados e as entidades de serviço do aplicativo não podem. Se você planeja atribuir uma função a um usuário ou aplicativo convidado, deve incluir as permissões de leitura apropriadas.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>Microsoft. Directory/Applications/myproperties/Read

Capacidade de ler todas as propriedades de aplicativos de locatário único e multilocatário fora das propriedades que não podem ser lidas em nenhuma situação, como credenciais.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>Microsoft. Directory/Applications. myorganizacionai/myproperties/Read

Concede as mesmas permissões que Microsoft. Directory/Applications/myproperties/Read, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Concede a capacidade de ler a propriedade Owners em aplicativos de locatário único e multilocatário. Concede acesso a todos os campos na página proprietários de registro do aplicativo:

![Essas permissões concedem acesso à página de proprietários de registro do aplicativo](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>Microsoft. Directory/Applications/Standard/Read

Concede acesso para ler as propriedades de registro de aplicativo padrão. Isso inclui propriedades nas páginas de registro do aplicativo.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>Microsoft. Directory/Applications. myorganizacional/Standard/Read

Concede as mesmas permissões que Microsoft. Directory/Applications/Standard/Read, mas somente para aplicativos de locatário único.

### <a name="update"></a>Atualizar

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>Microsoft. Directory/Applications/myproperties/Update

Capacidade de atualizar todas as propriedades em aplicativos de locatário único e multilocatário.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>Microsoft. Directory/Applications. MyOrganization/Propriedade/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/myproperties/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Capacidade de atualizar a propriedade de tipo de conta com suporte (signInAudience) em aplicativos de locatário único e multilocatário.

![Essa permissão concede acesso à propriedade tipo de conta com suporte de registro de aplicativo na página autenticação](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>Microsoft. Directory/Applications. MyOrganization/audiência/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/Audience/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Capacidade de atualizar a URL de resposta, a URL de saída, o fluxo implícito e as propriedades de domínio do Publicador em aplicativos de locatário único e multilocatário. Concede acesso a todos os campos na página de autenticação de registro do aplicativo, exceto tipos de conta com suporte:

![Concede acesso à autenticação de registro de aplicativo, mas aos tipos de conta sem suporte](./media/custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Concede as mesmas permissões que Microsoft. Directory/Applications/Authentication/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Capacidade de atualizar o nome, o logotipo, a URL da Home Page, a URL dos termos de serviço e as propriedades de URL da política de privacidade em aplicativos de locatário único e multilocatário. Concede acesso a todos os campos na página de identidade visual do registro do aplicativo:

![Essa permissão concede acesso à página de identidade visual do registro do aplicativo](./media/custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>Microsoft. Directory/Applications. MyOrganization/Basic/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/Basic/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Capacidade de atualizar as propriedades de certificados e segredos de cliente em aplicativos de locatário único e multilocatário. Concede acesso a todos os campos na página certificados de registro de aplicativo & segredos:

![Essa permissão concede acesso à página de certificados de registro de aplicativo & segredos](./media/custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>Microsoft. Directory/Applications. MyOrganization/Credentials/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/Credentials/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Capacidade de atualizar a propriedade de proprietário em um locatário único e multilocatário. Concede acesso a todos os campos na página proprietários de registro do aplicativo:

![Essas permissões concedem acesso à página de proprietários de registro do aplicativo](./media/custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>Microsoft. Directory/Applications. MyOrganization/Owners/Update

Concede as mesmas permissões que Microsoft. Directory/Applications/Owners/Update, mas somente para aplicativos de locatário único.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Capacidade de atualizar as permissões delegadas, permissões de aplicativo, aplicativos cliente autorizados, permissões necessárias e conceder Propriedades de consentimento em aplicativos de locatário único e multilocatário. Não concede a capacidade de executar o consentimento. Concede acesso a todos os campos nas permissões da API de registro de aplicativo e expõe uma página de API:

![Essas permissões concedem acesso à página de permissões da API de registro do aplicativo](./media/custom-available-permissions/app-registration-api-permissions.png)

![Essa permissão concede acesso ao registro do aplicativo expor uma página de API](./media/custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

Concede as mesmas permissões que Microsoft. Directory/Applications/Permissions/Update, mas somente para aplicativos de locatário único.

## <a name="required-license-plan"></a>Plano de licença necessária

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Próximas etapas

- Criar funções personalizadas usando [o portal do Azure, o PowerShell do Azure AD e o API do Graph](custom-create.md)
- [Exibir as atribuições de uma função personalizada](custom-view-assignments.md)
