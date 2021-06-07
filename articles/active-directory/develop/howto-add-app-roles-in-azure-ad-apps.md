---
title: Adicionar funções de aplicativo e obtê-las de um token | Azure
titleSuffix: Microsoft identity platform
description: Saiba como adicionar funções de aplicativo a um aplicativo registrado no Azure Active Directory, atribuir usuários e grupos a essas funções e recebê-las na declaração ' Roles ' no token.
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: fce963bd9ffdc6f768d7b3de4a9e4870add06136
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104239"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>Como: adicionar funções de aplicativo ao seu aplicativo e recebê-las no token

O RBAC (controle de acesso baseado em função) é um mecanismo comum usado para impor a autorização em aplicativos. Usando o RBAC, o administrador concede permissões a funções, e não a usuários ou grupos individuais. O administrador pode, então, atribuir funções a diferentes usuários e grupos para controlar quem tem acesso a qual conteúdo e funcionalidade.

Usando o RBAC com funções de aplicativo e declarações de função, os desenvolvedores podem impor com segurança a autorização em seus aplicativos com menos esforço.

Outra abordagem é usar grupos do Azure AD e declarações de grupo, conforme mostrado no exemplo de código [Active-Directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) no github. Grupos e funções de aplicativo do Azure AD não são mutuamente exclusivos; Eles podem ser usados em conjunto para fornecer um controle de acesso ainda mais refinado.

## <a name="declare-roles-for-an-application"></a>Declarar funções para um aplicativo

Você define as funções de aplicativo usando o [portal do Azure](https://portal.azure.com). As funções de aplicativo geralmente são definidas em um registro de aplicativo que representa um serviço, aplicativo ou API. Quando um usuário entra no aplicativo, o Azure AD emite uma `roles` declaração para cada função que o usuário ou a entidade de serviço recebeu individualmente para o usuário e da sua associação de grupo. Isso pode ser usado para implementar a autorização baseada em declarações. As funções de aplicativo podem ser atribuídas [a um usuário ou grupo de usuários](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app). As funções de aplicativo também podem ser atribuídas à entidade de serviço para outro aplicativo ou [à entidade de serviço para uma identidade gerenciada](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md).

> [!IMPORTANT]
> No momento, se você adicionar uma entidade de serviço a um grupo e atribuir uma função de aplicativo a esse grupo, o Azure AD não adicionará a `roles` declaração aos tokens emitidos.

Existem duas maneiras de declarar funções de aplicativo usando o portal do Azure:

* [Interface do usuário de funções de aplicativo](#app-roles-ui--preview) | Visualizar
* [Editor de manifesto do aplicativo](#app-manifest-editor)

O número de funções que você adiciona conta para os limites de manifesto do aplicativo imposto pelo Azure Active Directory. Para obter informações sobre esses limites, consulte a seção de  [limites de manifesto](./reference-app-manifest.md#manifest-limits) de [Azure Active Directory referência de manifesto de aplicativo](reference-app-manifest.md).

### <a name="app-roles-ui--preview"></a>Interface do usuário de funções de aplicativo | Visualizar

> [!IMPORTANT]
> O recurso de interface do usuário do portal de funções de aplicativo [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Para criar uma função de aplicativo usando a interface do usuário do portal do Azure:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o locatário Azure Active Directory que contém o registro do aplicativo ao qual você deseja adicionar uma função de aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **gerenciar**, selecione **registros de aplicativo** e, em seguida, selecione o aplicativo no qual você deseja definir funções de aplicativo.
1. Selecionar **Funções de aplicativo | Visualizar** e selecione **Criar função de aplicativo**.

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Um painel de funções de aplicativo do registro de aplicativo no portal do Azure":::
1. No painel **criar função de aplicativo** , insira as configurações para a função. A tabela a seguir descreve cada configuração e seus parâmetros.

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Um painel de contexto criar funções de aplicativo do registro de aplicativo no portal do Azure":::

    | Campo | Descrição | Exemplo |
    |-------|-------------|---------|
    | **Nome de exibição** | Nome de exibição para a função de aplicativo que aparece no consentimento do administrador e experiências de atribuição de aplicativo. Esse valor pode conter espaços. | `Survey Writer` |
    | **Tipos de membro permitidos** | Especifica se essa função de aplicativo pode ser atribuída a usuários, aplicativos ou ambos.<br/><br/>Quando disponível para `applications` o, as funções de aplicativo aparecem como permissões de aplicativo na seção **gerenciar** do registro de aplicativo > **permissões de API > adicionar uma permissão > minhas APIs > escolher uma API > permissões de aplicativo**. | `Users/Groups` |
    | **Valor** | Especifica o valor da declaração de funções que o aplicativo deve esperar no token. O valor deve corresponder exatamente à cadeia de caracteres referenciada no código do aplicativo. O valor não pode conter espaços. | `Survey.Create` |
    | **Descrição** | Uma descrição mais detalhada da função de aplicativo exibida durante as experiências de atribuição e consentimento do aplicativo de administração. | `Writers can create surveys.` |
    | **Deseja habilitar esta função de aplicativo?** | Especifica se a função de aplicativo está habilitada. Para excluir uma função de aplicativo, desmarque essa caixa de seleção e aplique a alteração antes de tentar a operação de exclusão. | *Verificado* |

1. Selecione **Aplicar** para salvar as alterações.

### <a name="app-manifest-editor"></a>Editor de manifesto do aplicativo

Para adicionar funções editando o manifesto diretamente:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Selecione o **diretório +** filtro de assinatura no menu superior e escolha o locatário Azure Active Directory que contém o registro do aplicativo ao qual você deseja adicionar uma função de aplicativo.
1. Pesquise **Azure Active Directory** e selecione-o.
1. Em **gerenciar**, selecione **registros de aplicativo** e, em seguida, selecione o aplicativo no qual você deseja definir funções de aplicativo.
1. Novamente em **gerenciar**, selecione **manifesto**.
1. Edite o manifesto do aplicativo localizando a `appRoles` configuração e adicionando suas funções de aplicativo. Você pode definir funções de aplicativo direcionadas `users` , `applications` ou ambas. Os trechos de código JSON a seguir mostram exemplos de ambos.
1. Salve o manifesto.

Cada definição de função de aplicativo no manifesto deve ter um GUID exclusivo para seu `id` valor.

A propriedade `value` de cada definição de função de aplicativo deve corresponder exatamente às cadeias de caracteres usadas no código do aplicativo. A propriedade `value` não pode conter espaços. Se ela contiver, você receberá um erro ao salvar o manifesto.

#### <a name="example-user-app-role"></a>Exemplo: função de aplicativo de usuário

Este exemplo define uma função de aplicativo chamada `Writer` que você pode atribuir a um `User` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>Exemplo: função de aplicativo de aplicativo

Quando disponível para `applications` o, as funções de aplicativo aparecem como permissões de aplicativo na seção **gerenciar** do registro de aplicativo > **permissões de API > adicionar uma permissão > minhas APIs > escolher uma API > permissões de aplicativo**.

Este exemplo mostra uma função de aplicativo direcionada a um `Application` :

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>Atribuir usuários e grupos a funções

Após adicionar funções de aplicativo em seu aplicativo, você pode atribuir usuários e grupos a essas funções. A atribuição de usuários e grupos a funções pode ser feita por meio da interface do usuário do portal ou de maneira programática usando o [Microsoft Graph](/graph/api/user-post-approleassignments). Quando os usuários atribuídos às várias funções de aplicativo entrarem no aplicativo, seus tokens terão suas funções atribuídas na `roles` declaração.

Para atribuir usuários e grupos às funções usando o portal do Azure:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Em **Azure Active Directory**, selecione **aplicativos empresariais** no menu de navegação à esquerda.
1. Selecione **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos. Se o seu aplicativo não aparecer na lista, use os filtros na parte superior da lista **todos os aplicativos** para restringir a lista ou role para baixo na lista para localizar seu aplicativo.
1. Selecione o aplicativo no qual deseja atribuir funções a usuários ou grupos de segurança.
1. Em **gerenciar**, selecione **usuários e grupos**.
1. Selecione **Adicionar usuário** para abrir o painel **Adicionar atribuição** .
1. Clique no seletor **Usuários e grupos** do painel **Adicionar Atribuição**. É mostrada uma lista de usuários e grupos de segurança. Você pode pesquisar um determinado usuário ou grupo, bem como selecionar vários usuários e grupos que aparecem na lista.
1. Depois de selecionar usuários e grupos, selecione o botão **selecionar** para continuar.
1. Selecione **selecionar uma função** no painel **Adicionar atribuição** . Todas as funções que você definiu para o aplicativo são exibidas.
1. Escolha uma função e selecione o botão **selecionar** .
1. Selecione o botão **atribuir** para concluir a atribuição de usuários e grupos ao aplicativo.

Veja se os usuários e grupos que você adicionou aparecem na lista atualizada de **Usuários e grupos**.

## <a name="assign-app-roles-to-applications"></a>Atribuir funções de aplicativo a aplicativos

Depois de adicionar funções de aplicativo em seu aplicativo, você pode atribuir uma função de aplicativo a um aplicativo cliente usando o portal do Azure ou de forma programática usando [Microsoft Graph](/graph/api/user-post-approleassignments).

Ao atribuir funções de aplicativo a um aplicativo, você cria *permissões de aplicativo*. As permissões de aplicativo normalmente são usadas por aplicativos daemon ou serviços de back-end que precisam autenticar e fazer chamadas de API autorizadas, sem a interação de um usuário.

Para atribuir funções de aplicativo a um aplicativo usando o portal do Azure:

1. Entre no <a href="https://portal.azure.com/" target="_blank">portal do Azure</a>.
1. Em **Azure Active Directory**, selecione **registros de aplicativo** no menu de navegação à esquerda.
1. Selecione **Todos os aplicativos** para exibir uma lista com todos os seus aplicativos. Se o seu aplicativo não aparecer na lista, use os filtros na parte superior da lista **todos os aplicativos** para restringir a lista ou role para baixo na lista para localizar seu aplicativo.
1. Selecione o aplicativo ao qual você deseja atribuir uma função de aplicativo.
1. Selecione **Permissões de API** > **Adicionar uma permissão**.
1. Selecione a guia **minhas APIs** e, em seguida, selecione o aplicativo para o qual você definiu funções de aplicativo.
1. Selecione **Permissões de aplicativo**.
1. Selecione a (s) função (ões) que você deseja atribuir.
1. Selecione o botão **adicionar permissões** adição completa da (s) função (ões).

As funções adicionadas recentemente devem aparecer no painel de **permissões de API** do registro de aplicativo.

#### <a name="grant-admin-consent"></a>Conceder consentimento do administrador

Como essas são *permissões de aplicativo*, permissões não delegadas, um administrador deve conceder consentimento para usar as funções de aplicativo atribuídas ao aplicativo.

1. No painel **permissões de API** do registro de aplicativo, selecione **conceder consentimento de \<tenant name\> administrador para**.
1. Selecione **Sim** quando solicitado a conceder consentimento para as permissões solicitadas.

A coluna **status** deve refletir que o consentimento foi **concedido para \<tenant name\>**.

## <a name="use-app-roles-in-your-web-api"></a>Usar funções de aplicativo em sua API Web

Depois de definir as funções de aplicativo e atribuí-las a um usuário, grupo ou aplicativo, a próxima etapa é adicionar o código à API Web que verifica essas funções quando a API é chamada. Ou seja, quando um aplicativo cliente solicita uma operação de API que você decidiu exigir autorização, o código da API deve verificar se os escopos estão no token de acesso apresentado na chamada do aplicativo cliente.

Para saber como adicionar autorização à API Web, consulte [API Web protegida: verificar escopos e funções de aplicativo](scenario-protected-web-api-verification-scope-app-roles.md).

## <a name="app-roles-vs-groups"></a>Funções de aplicativo versus grupos

Embora você possa usar funções de aplicativo ou grupos para autorização, as principais diferenças entre eles podem influenciar o que você decidir usar para seu cenário.

| Funções de aplicativo                                                                          | Grupos                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| Eles são específicos de um aplicativo e são definidos no registro do aplicativo. Eles se movem com o aplicativo. | Eles não são específicos para um aplicativo, mas para um locatário do Azure AD. |
| As funções de aplicativo são removidas quando o registro do aplicativo é removido.                      | Os grupos permanecem intactos, mesmo que o aplicativo seja removido.            |
| Fornecido na `roles` declaração.                                                     | Fornecido na `groups` declaração.                                 |

Os desenvolvedores podem usar funções de aplicativo para controlar se um usuário pode entrar em um aplicativo ou um aplicativo pode obter um token de acesso para uma API da Web. Para estender esse controle de segurança para grupos, os desenvolvedores e administradores também podem atribuir grupos de segurança a funções de aplicativo.

As funções de aplicativo são preferenciais pelos desenvolvedores quando desejam descrever e controlar os parâmetros de autorização no próprio aplicativo. Por exemplo, um aplicativo que usa grupos para autorização será interrompido no próximo locatário, pois tanto a ID de grupo quanto o nome podem ser diferentes. Um aplicativo que usa funções de aplicativo permanece seguro. Na verdade, a atribuição de grupos a funções de aplicativo é popular com aplicativos SaaS pelos mesmos motivos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as funções de aplicativo com os recursos a seguir.

* Exemplo de código no GitHub
  * [Adicionar autorização usando grupos e declarações de grupo para um aplicativo Web ASP.NET Core](https://aka.ms/groupssample)
  * [SPA (aplicativo de página única) angular chamando uma API Web do .NET Core e usando funções de aplicativo e grupos de segurança](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* Documentação de referência
  * [Manifesto de aplicativo do Azure AD](./reference-app-manifest.md)
  * [Tokens de acesso do AD do Azure](access-tokens.md)
  * [Tokens de ID do Azure AD](id-tokens.md)
  * [Fornecer declarações opcionais para seu aplicativo](active-directory-optional-claims.md)
* Vídeo: [implementar a autorização em seus aplicativos com a plataforma de identidade da Microsoft](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
