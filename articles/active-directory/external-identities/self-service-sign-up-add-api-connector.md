---
title: Adicionar conectores de API a fluxos de inscrição de autoatendimento-Azure AD
description: Configure uma API Web a ser usada em um fluxo de usuário.
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
ms.openlocfilehash: 26403c20d7f3274e8f3f2dcae479f72e9a7e3354
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807013"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Adicionar um conector de API a um fluxo de usuário

Para usar um [conector de API](api-connectors-overview.md), primeiro crie o conector de API e habilite-o em um fluxo de usuário.

> [!IMPORTANT]
>**A partir de 4 de janeiro de 2021**, o Google está [preterindo o suporte de entrada do WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Se estiver usando a federação do Google ou a inscrição por autoatendimento com o Gmail, você deverá [testar seus aplicativos nativos de linha de negócios para garantir a compatibilidade](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="create-an-api-connector"></a>Criar um conector de API

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **todos os conectores de API (versão prévia)** e, em seguida, selecione **novo conector de API**.

   ![Adicionar um novo conector de API](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Forneça um nome de exibição para a chamada. Por exemplo, **Verifique o status de aprovação**.
6. Forneça a **URL do ponto de extremidade** para a chamada à API.
7. Forneça as informações de autenticação para a API.

   - Somente a autenticação básica tem suporte no momento. Se você quiser usar uma API sem autenticação básica para fins de desenvolvimento, basta inserir um **nome de usuário** e **senha** fictícios que sua API pode ignorar. Para usar com uma função do Azure com uma chave de API, você pode incluir o código como um parâmetro de consulta na **URL do ponto de extremidade** (por exemplo, `https://contoso.azurewebsites.net/api/endpoint?code=0123456789` ).

   ![Configurar um novo conector de API](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. Selecione **Salvar**.

> [!IMPORTANT]
> Anteriormente, era necessário configurar quais atributos de usuário enviar à API (' claims to send ') e quais atributos de usuário aceitar da API (' claims to Receive '). Agora, todos os atributos de usuário são enviados por padrão se eles tiverem um valor e qualquer atributo de usuário puder ser retornado pela API em uma resposta de ' continuação '.

## <a name="the-request-sent-to-your-api"></a>A solicitação enviada à sua API
Um conector de API se materializa como uma solicitação **http post** , enviando atributos de usuário (' declarações ') como pares de chave-valor em um corpo JSON. Os atributos são serializados da mesma forma para [Microsoft Graph](/graph/api/resources/user#properties) Propriedades de usuário. 

**Solicitação de exemplo**
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

Somente as propriedades de usuário e os atributos personalizados listados na **Azure Active Directory**  >  experiência de atributos de usuário personalizados de **identidades externas**  >   estão disponíveis para serem enviados na solicitação.

Existem atributos personalizados no formato **de \<extensions-app-id> _AttributeName extension_**  no diretório. Sua API deve esperar receber declarações nesse mesmo formato serializado. Para obter mais informações sobre atributos personalizados, consulte [definir atributos personalizados para fluxos de inscrição de autoatendimento](user-flow-add-custom-attributes.md).

Além disso, a Declaração **localidades da interface do usuário (' ui_locales ')** é enviada por padrão em todas as solicitações. Ele fornece a localidade de um usuário, conforme configurado em seu dispositivo, que pode ser usado pela API para retornar respostas internacionalizadas.

> [!IMPORTANT]
> Se uma declaração a ser enviada não tiver um valor no momento em que o ponto de extremidade de API for chamado, a declaração não será enviada para a API. Sua API deve ser projetada para verificar explicitamente o valor esperado.

> [!TIP] 
> [**identidades (' identidades ')**](/graph/api/resources/objectidentity) e as declarações de **endereço de email (' email ')** podem ser usadas pela sua API para identificar um usuário antes que eles tenham uma conta em seu locatário. A declaração ' Identities ' é enviada quando um usuário é autenticado com um provedor de identidade, como Google ou Facebook. ' email ' é sempre enviado.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Habilitar o conector de API em um fluxo de usuário

Siga estas etapas para adicionar um conector de API a um fluxo de usuário de inscrição de autoatendimento.

1. Entre no [Portal do Azure](https://portal.azure.com/) como administrador do Microsoft Azure AD.
2. Em **Serviços do Azure**, selecione **Azure Active Directory**.
3. No menu à esquerda, selecione **Identidades Externas**.
4. Selecione **fluxos de usuário (versão prévia)** e, em seguida, selecione o fluxo de usuário ao qual você deseja adicionar o conector de API.
5. Selecione **conectores de API** e, em seguida, selecione os pontos de extremidade de API que você deseja invocar nas etapas a seguir no fluxo do usuário:

   - **Depois de entrar com um provedor de identidade**
   - **Antes de criar o usuário**

   ![Adicionar APIs ao fluxo do usuário](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Selecione **Salvar**.

## <a name="after-signing-in-with-an-identity-provider"></a>Depois de entrar com um provedor de identidade

Um conector de API nesta etapa no processo de inscrição é invocado imediatamente depois que o usuário é autenticado com um provedor de identidade (Google, Facebook, Azure AD). Esta etapa precede a ***página coleção de atributos***, que é o formulário apresentado ao usuário para coletar atributos de usuário. 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exemplo de solicitação enviada para a API nesta etapa
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

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de resposta esperados da API Web nesta etapa

Quando a API Web recebe uma solicitação HTTP do Azure AD durante um fluxo de usuário, ela pode retornar essas respostas:

- Resposta de continuação
- Resposta de bloqueio

#### <a name="continuation-response"></a>Resposta de continuação

Uma resposta de continuação indica que o fluxo do usuário deve continuar para a próxima etapa: a página coleção de atributos.

Em uma resposta de continuação, a API pode retornar declarações. Se uma declaração for retornada pela API, a declaração fará o seguinte:

- Preenche previamente o campo de entrada na página coleção de atributos.

Consulte um exemplo de uma [resposta de continuação](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Resposta de bloqueio

Uma resposta de bloqueio sai do fluxo do usuário. Ele pode ser emitido intencionalmente pela API para interromper a continuação do fluxo do usuário, exibindo uma página de bloco para o usuário. A página bloco exibe o `userMessage` fornecido pela API.

Consulte um exemplo de uma [resposta de bloqueio](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Antes de criar o usuário

Um conector de API nesta etapa no processo de inscrição é invocado após a página de coleção de atributos, se um for incluído. Essa etapa é sempre invocada antes de uma conta de usuário ser criada no Azure AD. 

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exemplo de solicitação enviada para a API nesta etapa

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

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Tipos de resposta esperados da API Web nesta etapa

Quando a API Web recebe uma solicitação HTTP do Azure AD durante um fluxo de usuário, ela pode retornar essas respostas:

- Resposta de continuação
- Resposta de bloqueio
- Resposta de validação

#### <a name="continuation-response"></a>Resposta de continuação

Uma resposta de continuação indica que o fluxo do usuário deve continuar para a próxima etapa: criar o usuário no diretório.

Em uma resposta de continuação, a API pode retornar declarações. Se uma declaração for retornada pela API, a declaração fará o seguinte:

- Substitui qualquer valor que já tenha sido atribuído à declaração da página de coleção de atributos.

Consulte um exemplo de uma [resposta de continuação](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Resposta de bloqueio
Uma resposta de bloqueio sai do fluxo do usuário. Ele pode ser emitido intencionalmente pela API para interromper a continuação do fluxo do usuário, exibindo uma página de bloco para o usuário. A página bloco exibe o `userMessage` fornecido pela API.

Consulte um exemplo de uma [resposta de bloqueio](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Validação-resposta de erro
 Quando a API responde com uma resposta de erro de validação, o fluxo do usuário permanece na página coleção de atributos e um `userMessage` é exibido para o usuário. O usuário pode editar e reenviar o formulário. Esse tipo de resposta pode ser usado para validação de entrada.

Consulte um exemplo de uma [resposta de erro de validação](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Respostas de exemplo

### <a name="example-of-a-continuation-response"></a>Exemplo de uma resposta de continuação

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parâmetro                                          | Tipo              | Obrigatório | Descrição                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | String            | Sim      | A versão da API.                                                                                                                                                                                                                                                                |
| ação                                             | String            | Sim      | O valor precisa ser `Continue`.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Não       | Os valores podem ser armazenados no diretório se forem selecionados como uma **declaração para receber** na configuração do conector de API e nos **atributos de usuário** para um fluxo de usuário. Os valores podem ser retornados no token, se selecionado como uma **declaração de aplicativo**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Não       | A declaração retornada não precisa conter `_<extensions-app-id>_` . Os valores serão armazenados no diretório se forem selecionados como uma **declaração para receber** na configuração do conector de API e no **atributo de usuário** para um fluxo de usuário. Atributos personalizados não podem ser enviados de volta no token. |

### <a name="example-of-a-blocking-response"></a>Exemplo de uma resposta de bloqueio

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parâmetro   | Tipo   | Obrigatório | Descrição                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | Sim      | A versão da API.                                                    |
| ação      | String | Sim      | O valor deve ser `ShowBlockPage`                                              |
| userMessage | String | Sim      | Mensagem a ser exibida ao usuário.                                            |
| code        | String | Não       | Código do erro. Pode ser usado para fins de depuração. Não são exibidos para o usuário. |

**Experiência do usuário final com uma resposta de bloqueio**

![Exemplo de página de bloco](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Exemplo de uma resposta de erro de validação

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parâmetro   | Tipo    | Obrigatório | Descrição                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | Sim      | A versão da API.                                                    |
| ação      | String  | Sim      | O valor precisa ser `ValidationError`.                                           |
| status      | Integer | Sim      | Deve ser `400` um valor para uma resposta do ValidationError.                        |
| userMessage | String  | Sim      | Mensagem a ser exibida ao usuário.                                            |
| code        | String  | Não       | Código do erro. Pode ser usado para fins de depuração. Não são exibidos para o usuário. |

**Experiência do usuário final com uma resposta de erro de validação**

![Página de validação de exemplo](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Práticas recomendadas e como solucionar problemas

### <a name="using-serverless-cloud-functions"></a>Usando funções de nuvem sem servidor
Funções sem servidor, como gatilhos HTTP no Azure Functions, fornecem uma maneira simples de criar pontos de extremidade de API para usar com o conector de API. Você pode usar a função de nuvem sem servidor para, [por exemplo](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts), executar a lógica de validação e limitar as entradas a domínios específicos. A função de nuvem sem servidor também pode chamar e invocar outras APIs da Web, lojas de usuários e outros serviços de nuvem para cenários mais complexos.

### <a name="best-practices"></a>Práticas recomendadas
Verifique se:
* Sua API está seguindo os contratos de solicitação e resposta da API, conforme descrito acima. 
* A **URL do ponto de extremidade** do conector de API aponta para o ponto de extremidade de API correto.
* Sua API verifica explicitamente se há valores nulos de declarações recebidas.
* Sua API responde o mais rápido possível para garantir uma experiência de usuário fluida.
    * Se estiver usando uma função sem servidor ou um serviço Web escalonável, use um plano de hospedagem que mantenha a API "ativo" ou "quente". Por Azure Functions, é recomendável usar o [plano Premium](../../azure-functions/functions-premium-plan.md). 


### <a name="use-logging"></a>Usar registro em log
Em geral, é útil usar as ferramentas de log habilitadas pelo serviço de API Web, como o [Application insights](../../azure-functions/functions-monitoring.md), para monitorar sua API para códigos de erro inesperados, exceções e baixo desempenho.
* Monitore os códigos de status HTTP que não são HTTP 200 ou 400.
* Um código de status HTTP 401 ou 403 normalmente indica que há um problema com sua autenticação. Verifique a camada de autenticação da API e a configuração correspondente no conector da API.
* Use níveis mais agressivos de registro em log (por exemplo, "rastreamento" ou "depuração") no desenvolvimento, se necessário.
* Monitore sua API para tempos de resposta longos.

## <a name="next-steps"></a>Próximas etapas
<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Saiba como [Adicionar um fluxo de trabalho de aprovação personalizado à inscrição de autoatendimento](self-service-sign-up-add-approvals.md)
- Introdução aos nossos [exemplos de início rápido do Azure function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->