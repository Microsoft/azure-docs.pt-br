---
title: Adicionar aprovações personalizadas para fluxos de inscrição de autoatendimento-Azure AD
description: Adicionar conectores de API para fluxos de trabalho de aprovação personalizados em identidades externas inscrição de autoatendimento-Azure Active Directory (Azure AD)
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3165bc28e6d6283bf8578d9c10b11f7b19981002
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355232"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>Adicionar um fluxo de trabalho de aprovação personalizado à inscrição de autoatendimento

Com os [conectores de API](api-connectors-overview.md), você pode integrar com seus próprios fluxos de trabalho de aprovação personalizados com inscrição de autoatendimento para que você possa gerenciar quais contas de usuário convidado são criadas em seu locatário.

Este artigo fornece um exemplo de como integrar o a um sistema de aprovação. Neste exemplo, o fluxo de usuário de inscrição de autoatendimento coleta dados do usuário durante o processo de inscrição e os transmite para o sistema de aprovação. Em seguida, o sistema de aprovação pode:

- Aprove automaticamente o usuário e permita que o Azure AD crie a conta de usuário.
- Dispare uma revisão manual. Se a solicitação for aprovada, o sistema de aprovação usará Microsoft Graph para provisionar a conta de usuário. O sistema de aprovação também pode notificar o usuário de que sua conta foi criada.

> [!IMPORTANT]
>A **partir de 4 de janeiro de 2021**, o Google está [preterindo o suporte de entrada do WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se você estiver usando a inscrição do Google Federation ou autoatendimento com o Gmail, deverá [testar seus aplicativos nativos de linha de negócios para compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="register-an-application-for-your-approval-system"></a>Registrar um aplicativo para seu sistema de aprovação

Você precisa registrar seu sistema de aprovação como um aplicativo em seu locatário do Azure AD para que ele possa ser autenticado com o Azure AD e tenha permissão para criar usuários. Saiba mais sobre [noções básicas de autenticação e autorização para Microsoft Graph](/graph/auth/auth-concepts).

1. Entre no [Portal do Azure](https://portal.azure.com) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **registros de aplicativo** e, em seguida, selecione **novo registro**.
4. Insira um **nome** para o aplicativo, por exemplo, _aprovações de inscrição_.

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. Selecione **Registrar**. Você pode deixar outros campos em seus padrões.

   ![Captura de tela que realça o botão registrar.](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. Em **gerenciar** no menu à esquerda, selecione **permissões de API** e, em seguida, selecione **Adicionar uma permissão**.
7. Na página **solicitar permissões de API** , selecione **Microsoft Graph** e, em seguida, selecione **permissões de aplicativo**.
8. Em **selecionar permissões**, expanda **usuário** e marque a caixa de seleção **User. ReadWrite. All** . Essa permissão permite que o sistema de aprovação crie o usuário após a aprovação. Em seguida, selecione **Adicionar permissões**.

   ![Registrar uma página de aplicativo](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. Na página **permissões de API** , selecione **conceder consentimento de administrador para (seu nome de locatário)** e, em seguida, selecione **Sim**.
10. Em **gerenciar** no menu à esquerda, selecione **certificados & segredos** e, em seguida, selecione **novo segredo do cliente**.
11. Insira uma **Descrição** para o segredo, por exemplo, _aprovações do cliente secreto_ e selecione a duração de quando o segredo do cliente **expira**. Em seguida, selecione **Adicionar**.
12. Copie o valor do segredo do cliente.

    ![Copiar o segredo do cliente para uso no sistema de aprovação](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. Configure seu sistema de aprovação para usar a **ID do aplicativo** como a ID do cliente e o segredo do **cliente** que você gerou para autenticar com o Azure AD.

## <a name="create-the-api-connectors"></a>Criar os conectores de API

Em seguida, você [criará os conectores de API](self-service-sign-up-add-api-connector.md#create-an-api-connector) para seu fluxo de usuário de inscrição de autoatendimento. Sua API do sistema de aprovação precisa de dois conectores e pontos de extremidade correspondentes, como os exemplos mostrados abaixo. Esses conectores de API fazem o seguinte:

- **Verifique o status de aprovação**. Envie uma chamada para o sistema de aprovação imediatamente depois que um usuário entrar com um provedor de identidade para verificar se o usuário tem uma solicitação de aprovação existente ou se já foi negado. Se o seu sistema de aprovação apenas tomar decisões de aprovação automática, esse conector de API poderá não ser necessário. Exemplo de um conector de API "verificar status de aprovação".

  ![Verificar configuração do conector de API de status de aprovação](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **Solicitação de aprovação** – envie uma chamada para o sistema de aprovação depois que um usuário concluir a página de coleção de atributos, mas antes de a conta de usuário ser criada, para solicitar aprovação. A solicitação de aprovação pode ser automaticamente concedida ou revisada manualmente. Exemplo de um conector de API de "solicitação de aprovação". 

  ![Solicitar configuração do conector de API de aprovação](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

Para criar esses conectores, siga as etapas em [criar um conector de API](self-service-sign-up-add-api-connector.md#create-an-api-connector).

## <a name="enable-the-api-connectors-in-a-user-flow"></a>Habilitar os conectores de API em um fluxo de usuário

Agora você adicionará os conectores de API a um fluxo de usuário de inscrição de autoatendimento com estas etapas:

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **fluxos de usuário (versão prévia)** e, em seguida, selecione o fluxo de usuário para o qual você deseja habilitar o conector de API.
5. Selecione **conectores de API** e, em seguida, selecione os pontos de extremidade de API que você deseja invocar nas etapas a seguir no fluxo do usuário:

   - **Depois de entrar com um provedor de identidade**: Selecione seu conector de API de status de aprovação, por exemplo, _Verifique o status de aprovação_.
   - **Antes de criar o usuário**: Selecione seu conector de API de solicitação de aprovação, por exemplo _solicitar aprovação_.

   ![Adicionar APIs ao fluxo do usuário](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. Clique em **Salvar**.

## <a name="control-the-sign-up-flow-with-api-responses"></a>Controlar o fluxo de inscrição com respostas de API

Seu sistema de aprovação pode usar suas respostas quando chamado para controlar o fluxo de inscrição. 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>Solicitação e respostas para o conector de API "verificar status de aprovação"

Exemplo da solicitação recebida pela API do conector de API "verificar status de aprovação":

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

As declarações exatas enviadas para a API dependem de quais informações são fornecidas pelo provedor de identidade. ' email ' é sempre enviado.

#### <a name="continuation-response-for-check-approval-status"></a>Resposta de continuação para "verificar status de aprovação"

O ponto de extremidade da API **verificar status da aprovação** deverá retornar uma resposta de continuação se:

- O usuário não solicitou uma aprovação anteriormente.

Exemplo de resposta de continuação:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>Resposta de bloqueio para "verificar status de aprovação"

O ponto de extremidade da API **verificar status da aprovação** deverá retornar uma resposta de bloqueio se:

- A aprovação do usuário está pendente.
- O usuário foi negado e não deve ter permissão para solicitar aprovação novamente.

Veja a seguir exemplos de respostas de bloqueio:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-PENDING"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-DENIED"
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>Solicitação e respostas para o conector de API "solicitar aprovação"

Exemplo de uma solicitação HTTP recebida pela API do conector de API "solicitar aprovação":

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

As declarações exatas enviadas para a API dependem de quais informações são coletadas do usuário ou são fornecidas pelo provedor de identidade.

#### <a name="continuation-response-for-request-approval"></a>Resposta de continuação para "solicitação de aprovação"

O ponto de extremidade da API de **aprovação de solicitação** deve retornar uma resposta de continuação se:

- O usuário pode ser **_aprovado automaticamente_**.

Exemplo de resposta de continuação:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> Se uma resposta de continuação for recebida, o Azure AD criará uma conta de usuário e direcionará o usuário para o aplicativo.

#### <a name="blocking-response-for-request-approval"></a>Bloqueio de resposta para "solicitação de aprovação"

O ponto de extremidade da API de **aprovação de solicitação** deve retornar uma resposta de bloqueio se:

- Uma solicitação de aprovação de usuário foi criada e agora está pendente.
- Uma solicitação de aprovação de usuário foi negada automaticamente.

Veja a seguir exemplos de respostas de bloqueio:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
    "code": "CONTOSO-APPROVAL-REQUESTED"
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
    "code": "CONTOSO-APPROVAL-AUTO-DENIED"
}
```

O `userMessage` na resposta é exibido para o usuário, por exemplo:

![Exemplo de página de aprovação pendente](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>Criação de conta de usuário após aprovação manual

Depois de obter a aprovação manual, o sistema de aprovação personalizado cria uma conta de [usuário](/graph/azuread-users-concept-overview) usando [Microsoft Graph](/graph/use-the-api). A maneira como seu sistema de aprovação provisiona a conta de usuário depende do provedor de identidade que foi usado pelo usuário.

### <a name="for-a-federated-google-or-facebook-user"></a>Para um usuário federado do Google ou do Facebook

> [!IMPORTANT]
> O sistema de aprovação deve verificar explicitamente `identities` isso `identities[0]` e `identities[0].issuer` estar presente e `identities[0].issuer` igual a ' Facebook ' ou ' Google ' para usar esse método.

Se o usuário tiver entrado com uma conta do Google ou do Facebook, você poderá usar a [API de criação de usuário](/graph/api/user-post-users?tabs=http).

1. O sistema de aprovação usa o recebe a solicitação HTTP do fluxo do usuário.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. O sistema de aprovação usa Microsoft Graph para criar uma conta de usuário.

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| Parâmetro                                           | Obrigatório | Descrição                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | Sim      | Pode ser gerado por meio da `email` declaração enviada para a API, substituindo o `@` caractere por `_` e esperando-o previamente para `#EXT@<tenant-name>.onmicrosoft.com` . |
| accountEnabled                                      | Sim      | Deve ser definido como `true`.                                                                                                                                                 |
| mail                                                | Sim      | Equivalente à `email` declaração enviada para a API.                                                                                                               |
| userType                                            | Sim      | Deve ser `Guest`. Designa esse usuário como um usuário convidado.                                                                                                                 |
| identidades                                          | Sim      | As informações de identidade federada.                                                                                                                                    |
| \<otherBuiltInAttribute>                            | Não       | Outros atributos internos como `displayName` , `city` e outros. Os nomes de parâmetro são os mesmos que os parâmetros enviados pelo conector de API.                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | Não       | Atributos personalizados sobre o usuário. Os nomes de parâmetro são os mesmos que os parâmetros enviados pelo conector de API.                                                            |

### <a name="for-a-federated-azure-active-directory-user"></a>Para um usuário federado Azure Active Directory

Se um usuário entrar com uma conta de Azure Active Directory federada, você deverá usar a [API de convite](/graph/api/invitation-post) para criar o usuário e, opcionalmente, a [API de atualização do usuário](/graph/api/user-update) para atribuir mais atributos ao usuário.

1. O sistema de aprovação recebe a solicitação HTTP do fluxo do usuário.

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. O sistema de aprovação cria o convite usando o `email` fornecido pelo conector da API.

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress":"johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

Exemplo da resposta:

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. O sistema de aprovação usa a ID do usuário convidado para atualizar a conta do usuário com atributos de usuário coletados (opcional).

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>Próximas etapas

- Introdução aos nossos [exemplos de início rápido do Azure function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
- Faça o check [-up de inscrição por autoatendimento para usuários convidados com o exemplo de aprovação manual](code-samples-self-service-sign-up.md#custom-approval-workflows).
