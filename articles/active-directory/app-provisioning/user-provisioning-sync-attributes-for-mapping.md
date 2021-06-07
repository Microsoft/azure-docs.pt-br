---
title: Sincronizar atributos com o Azure AD para mapeamento
description: Ao configurar o provisionamento de usuário para aplicativos SaaS, use o recurso de extensão de diretório para adicionar atributos de origem que não são sincronizados por padrão.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/17/2021
ms.author: kenwith
ms.openlocfilehash: 52f34cdafac76a9bca2b4ff0b00e0b3efaa63f5d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579426"
---
# <a name="syncing-extension-attributes-attributes"></a>Sincronizando atributos de atributos de extensão

Ao personalizar mapeamentos de atributo para provisionamento de usuário, você pode descobrir que o atributo que você deseja mapear não aparece na lista de **atributos de origem** . Este artigo mostra como adicionar o atributo ausente sincronizando-o do seu Active Directory local (AD) para Azure Active Directory (Azure AD) ou criando os atributos de extensão no Azure AD para um usuário somente na nuvem. 

O Azure AD deve conter todos os dados necessários para criar um perfil de usuário ao provisionar contas de usuário do Azure AD para um aplicativo SaaS. Em alguns casos, para disponibilizar os dados, talvez seja necessário sincronizar os atributos do seu AD local com o Azure AD. Azure AD Connect sincroniza automaticamente determinados atributos com o Azure AD, mas não todos os atributos. Além disso, alguns atributos (como SAMAccountName) que são sincronizados por padrão podem não ser expostos usando o API do Graph do Azure AD. Nesses casos, você pode usar o recurso de extensão de diretório Azure AD Connect para sincronizar o atributo com o Azure AD. Dessa forma, o atributo ficará visível para o Azure AD API do Graph e o serviço de provisionamento do Azure AD. Se os dados necessários para o provisionamento estiverem em Active Directory, mas não estiverem disponíveis para provisionamento devido aos motivos descritos acima, você poderá usar Azure AD Connect para criar atributos de extensão. 

Embora a maioria dos usuários sejam, provavelmente, usuários híbridos que estão sincronizados do Active Directory, você também pode criar extensões em usuários somente de nuvem sem usar Azure AD Connect. Usando o PowerShell ou Microsoft Graph você pode estender o esquema de um usuário somente na nuvem. 

## <a name="create-an-extension-attribute-using-azure-ad-connect"></a>Criar um atributo de extensão usando Azure AD Connect

1. Abra o assistente de Azure AD Connect, escolha tarefas e, em seguida, escolha **Personalizar opções de sincronização**.

   ![Página tarefas adicionais do assistente de Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-customize.png)
 
2. Entre como um administrador global do Azure AD. 

3. Na página **recursos opcionais** , selecione a **extensão de diretório sincronização de atributos**.
 
   ![Página de recursos opcionais do assistente de Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extension-attribute-sync.png)

4. Selecione os atributos que você deseja estender para o Azure AD.
   > [!NOTE]
   > A pesquisa em **atributos disponíveis** diferencia maiúsculas de minúsculas.

   ![Captura de tela que mostra a página de seleção "extensões de diretório"](./media/user-provisioning-sync-attributes-for-mapping/active-directory-connect-directory-extensions.png)

5. Conclua o assistente de Azure AD Connect e permita a execução de um ciclo de sincronização completo. Quando o ciclo for concluído, o esquema será estendido e os novos valores serão sincronizados entre o AD local e o Azure AD.
 
6. No portal do Azure, enquanto você estiver [editando mapeamentos de atributo de usuário](customize-application-attributes.md), a lista de **atributos de origem** agora conterá o atributo adicionado no formato `<attributename> (extension_<appID>_<attributename>)` . Selecione o atributo e mapeie-o para o aplicativo de destino para provisionamento.

   ![Página de seleção de extensões de diretório do assistente de Azure Active Directory Connect](./media/user-provisioning-sync-attributes-for-mapping/attribute-mapping-extensions.png)

> [!NOTE]
> Atualmente, não há suporte para a capacidade de provisionar atributos de referência do AD local, como **ManagedBy** ou **DN/distinguishedName**. Você pode solicitar esse recurso no [User Voice](https://feedback.azure.com/forums/169401-azure-active-directory). 

## <a name="create-an-extension-attribute-on-a-cloud-only-user"></a>Criar um atributo de extensão em um usuário somente na nuvem
Os clientes podem usar o Microsoft Graph e o PowerShell para estender o esquema do usuário. Esses atributos de extensão são automaticamente descobertos na maioria dos casos, mas os clientes com mais de 1000 entidades de serviço podem encontrar extensões ausentes na lista atributo de origem. Se um atributo que você criar usando as etapas abaixo não aparecer automaticamente na lista de atributos de origem, verifique usando o grafo que o atributo de extensão foi criado com êxito e, em seguida, adicione-o ao seu esquema [manualmente](https://docs.microsoft.com/azure/active-directory/app-provisioning/customize-application-attributes#editing-the-list-of-supported-attributes). Ao fazer as solicitações do grafo abaixo, clique em saiba mais para verificar as permissões necessárias para fazer as solicitações. Você pode usar o [Gerenciador de gráficos](https://docs.microsoft.com/graph/graph-explorer/graph-explorer-overview) para fazer as solicitações. 

### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-microsoft-graph"></a>Criar um atributo de extensão em um usuário somente na nuvem usando Microsoft Graph
Você precisará usar um aplicativo para estender o esquema de seus usuários. Liste os aplicativos em seu locatário para identificar a ID do aplicativo que você deseja usar para estender o esquema do usuário. [Saiba mais.](https://docs.microsoft.com/graph/api/application-list?view=graph-rest-1.0&tabs=http)

```json
GET https://graph.microsoft.com/v1.0/applications
```

Crie o atributo de extensão. Substitua a propriedade **ID** abaixo pela **ID** recuperada na etapa anterior. Você precisará usar o atributo **"ID"** e não o "AppID". [Saiba mais.](https://docs.microsoft.com/graph/api/application-post-extensionproperty?view=graph-rest-1.0&tabs=http)
```json
POST https://graph.microsoft.com/v1.0/applications/{id}/extensionProperties
Content-type: application/json

{
    "name": "extensionName",
    "dataType": "string",
    "targetObjects": [
        "User"
    ]
}
```

A solicitação anterior criou um atributo de extensão com o formato "extension_appID_extensionName". Atualize um usuário com o atributo de extensão. [Saiba mais.](https://docs.microsoft.com/graph/api/user-update?view=graph-rest-1.0&tabs=http)
```json
PATCH https://graph.microsoft.com/v1.0/users/{id}
Content-type: application/json

{
  "extension_inputAppId_extensionName": "extensionValue"
}
```
Verifique o usuário para garantir que o atributo foi atualizado com êxito. [Saiba mais.](https://docs.microsoft.com/graph/api/user-get?view=graph-rest-1.0&tabs=http#example-3-users-request-using-select)

```json
GET https://graph.microsoft.com/v1.0/users/{id}?$select=displayName,extension_inputAppId_extensionName
```


### <a name="create-an-extension-attribute-on-a-cloud-only-user-using-powershell"></a>Criar um atributo de extensão em um usuário somente na nuvem usando o PowerShell
Crie uma extensão personalizada usando o PowerShell e atribua um valor a um usuário. 

```
#Connect to your Azure AD tenant   
Connect-AzureAD

#Create an application (you can instead use an existing application if you would like)
$App = New-AzureADApplication -DisplayName “test app name” -IdentifierUris https://testapp

#Create a service principal
New-AzureADServicePrincipal -AppId $App.AppId

#Create an extension property
New-AzureADApplicationExtensionProperty -ObjectId $App.ObjectId -Name “TestAttributeName” -DataType “String” -TargetObjects “User”

#List users in your tenant to determine the objectid for your user
Get-AzureADUser

#Set a value for the extension property on the user. Replace the objectid with the id of the user and the extension name with the value from the previous step
Set-AzureADUserExtension -objectid 0ccf8df6-62f1-4175-9e55-73da9e742690 -ExtensionName “extension_6552753978624005a48638a778921fan3_TestAttributeName”

#Verify that the attribute was added correctly.
Get-AzureADUser -ObjectId 0ccf8df6-62f1-4175-9e55-73da9e742690 | Select -ExpandProperty ExtensionProperty

```

## <a name="next-steps"></a>Próximas etapas

* [Definir quem está no escopo para provisionamento](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
