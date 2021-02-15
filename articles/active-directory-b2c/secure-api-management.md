---
title: Proteger uma API de Gerenciamento de API do Azure usando Azure Active Directory B2C
description: Saiba como usar tokens de acesso emitidos pelo Azure Active Directory B2C para proteger um ponto de extremidade da API de Gerenciamento de API do Azure.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 60b7bb33dfbf29b7e448887ce992d03009133b2e
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953478"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Proteger uma API de Gerenciamento de API do Azure com Azure AD B2C

Saiba como restringir o acesso à sua API do APIM (Gerenciamento de API do Azure) para clientes que se autenticaram com o Azure AD B2C (Azure Active Directory B2C). Siga as etapas neste artigo para criar e testar uma política de entrada no APIM que restringe o acesso apenas às solicitações que incluem um token de acesso válido emitido por Azure AD B2C.

## <a name="prerequisites"></a>Pré-requisitos

Você precisará dos seguintes recursos em vigor antes de continuar com as etapas deste artigo:

* [Locatário do Azure AD B2C](tutorial-create-tenant.md)
* [Aplicativo registrado](tutorial-register-applications.md) em seu locatário
* [Fluxos dos usuários criados](tutorial-create-user-flows.md) em seu locatário
* [API publicada](../api-management/import-and-publish.md) no Gerenciamento de API do Azure
* [Postman](https://www.getpostman.com/) para testar o acesso protegido (opcional)

## <a name="get-azure-ad-b2c-application-id"></a>Obter a ID do aplicativo Azure AD B2C

Ao proteger uma API no Gerenciamento de API do Azure com Azure AD B2C, você precisa de vários valores para a [política de entrada](../api-management/api-management-howto-policies.md) que você cria no APIM. Primeiro, registre a ID de um aplicativo que você criou anteriormente em seu locatário do Azure AD B2C. Se você estiver usando o aplicativo criado nos pré-requisitos, use a ID do aplicativo para *webbapp1*.

Para registrar um aplicativo no locatário do Azure AD B2C, você pode usar a nossa nova experiência unificada de **Registros de aplicativos** ou a nossa experiência herdada **Aplicativos (Herdado)** . [Saiba mais sobre a nova experiência](./app-registrations-training-guide.md).

#### <a name="app-registrations"></a>[Registros de aplicativo](#tab/app-reg-ga/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Selecione **registros de aplicativo** e, em seguida, selecione a guia **aplicativos pertencentes** .
1. Registre o valor na coluna **ID do aplicativo (cliente)** para *webapp1* ou outro aplicativo que você criou anteriormente.

#### <a name="applications-legacy"></a>[Aplicativos (Herdado)](#tab/applications-legacy/)

1. Entre no [portal do Azure](https://portal.azure.com).
1. Selecione o filtro **Diretório + assinatura** no menu superior e, em seguida, selecione o diretório que contém o locatário do Azure AD B2C.
1. No menu à esquerda, selecione **Azure AD B2C**. Ou selecione **Todos os serviços** e pesquise e selecione **Azure AD B2C**.
1. Em **gerenciar**, selecione **aplicativos (herdados)**.
1. Registre o valor na coluna **ID DO APLICATIVO** para *webapp1* ou outro aplicativo que você criou anteriormente.

* * *

## <a name="get-token-issuer-endpoint"></a>Obter ponto de extremidade do emissor do token

Em seguida, obtenha a URL de configuração conhecida para um dos seus fluxos de usuário do Azure AD B2C. Você também precisa do URI do ponto de extremidade do emissor do token para o qual você deseja dar suporte no Gerenciamento de API do Azure.

1. Navegue até seu locatário do Azure AD B2C no [portal do Azure](https://portal.azure.com).
1. Em **Políticas**, selecione **Fluxos dos usuários**.
1. Selecione uma política existente, por exemplo *B2C_1_signupsignin1* e, em seguida, selecione **Executar fluxo de usuário**.
1. Registre a URL no hiperlink exibido no título **Executar fluxo de usuário** próximo à parte superior da página. Essa URL é o ponto de extremidade de descoberta conhecido do OpenID Connect para o fluxo do usuário, e você o usará na próxima seção ao configurar a política de entrada no Gerenciamento de API do Azure.

    ![Hiperlink de URI conhecido na página Executar agora do portal do Azure](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. Selecione o hiperlink para navegar até a página de configuração conhecida do OpenID Connect.
1. Na página que é aberta no navegador, registre o valor `issuer`, por exemplo:

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    Você usará esse valor na próxima seção quando configurar sua API no Gerenciamento de API do Azure.

Agora você deve ter duas URLs registradas para uso na próxima seção: a URL do ponto de extremidade de configuração conhecida do OpenID Connect e o URI do emissor. Por exemplo:

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Configurar a política de entrada no Gerenciamento de API do Azure

Agora você está pronto para adicionar a política de entrada no Gerenciamento de API do Azure que valida chamadas de API. Ao adicionar uma política [Validação JWT](../api-management/api-management-access-restriction-policies.md#ValidateJWT) que verifica o público e o emissor em um token de acesso, você poderá garantir que somente chamadas de API com um token válido sejam aceitas.

1. Navegue até sua instância de Gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com).
1. Selecione **APIs**.
1. Selecione a API que você deseja proteger com o Azure AD B2C.
1. Selecione a guia **Design**.
1. Em **Processamento de entrada**, selecione **\</\>** para abrir o editor de código de política.
1. Coloque a marca `<validate-jwt>` a seguir dentro da política de `<inbound>`.

    1. Atualize o valor `url` no elemento `<openid-config>` com a URL de configuração conhecida de sua política.
    1. Atualize o elemento `<audience>` com a ID do aplicativo que você criou anteriormente em seu locatário B2C (por exemplo, *webapp1*).
    1. Atualize o elemento `<issuer>` com o ponto de extremidade do emissor do token que você registrou anteriormente.

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>Validar o acesso à API segura

Para garantir que somente chamadores autenticados possam acessar sua API, valide a configuração do Gerenciamento de API do Azure chamando a API com [Postman](https://www.getpostman.com/).

Para chamar a API, você precisa de um token de acesso emitido por Azure AD B2C e uma chave de assinatura do APIM.

### <a name="get-an-access-token"></a>Obter um token de acesso

Primeiro, você precisa de um token emitido pelo Azure AD B2C para usar no cabeçalho `Authorization` no Postman. Você pode obter um usando o recurso **Executar agora** do fluxo de usuários de inscrição/entrada que você deve ter criado como um dos pré-requisitos.

1. Navegue até seu locatário do Azure AD B2C no [portal do Azure](https://portal.azure.com).
1. Em **Políticas**, selecione **Fluxos dos usuários**.
1. Selecione um fluxo de usuários de inscrição/entrada existente, por exemplo, *B2C_1_signupsignin1*.
1. Para **Aplicativo**, selecione *webapp1*.
1. Para **URL de resposta**, escolha `https://jwt.ms`.
1. Selecione **Executar fluxo de usuário**.

    ![Página Executar fluxo de usuários para fluxo de inscrição e entrada no portal do Azure](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. Conclua o processo de conexão. Você deve ser redirecionado para `https://jwt.ms`.
1. Registre o valor de token codificado exibido no navegador. Você usa esse valor de token para o cabeçalho de autorização no Postman.

    ![Valor de token codificado exibido em jwt.ms](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>Obter chave de assinatura de API

Um aplicativo cliente (neste caso, o Postman) que chama uma API publicada deve incluir uma chave de assinatura de Gerenciamento de API válida em suas solicitações HTTP para a API. Para obter uma chave de assinatura a fim de incluir em sua solicitação HTTP do Postman:

1. Navegue até a instância de serviço do Gerenciamento de API do Azure no [portal do Azure](https://portal.azure.com).
1. Selecione **Assinaturas**.
1. Selecione as reticências de **Produto: Ilimitado**, em seguida, selecione **Mostrar/ocultar chaves**.
1. Registre a **CHAVE PRIMÁRIA** para o produto. Use essa chave para o cabeçalho de `Ocp-Apim-Subscription-Key` em sua solicitação HTTP no Postman.

![Página de chave de assinatura com Mostrar/ocultar chaves selecionado no portal do Azure](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>Testar uma chamada à API segura

Com o token de acesso e a chave de assinatura APIM registrada, agora você está pronto para testar se configurou corretamente o acesso seguro à API.

1. Crie uma solicitação `GET` no [Postman](https://www.getpostman.com/). Para a URL de solicitação, especifique o ponto de extremidade da lista de palestrantes da API que você publicou como um dos pré-requisitos. Por exemplo:

    `https://contosoapim.azure-api.net/conference/speakers`

1. Em seguida, adicione os seguintes cabeçalhos:

    | Chave | Valor |
    | --- | ----- |
    | `Authorization` | Valor de token codificado que você registrou anteriormente, prefixado com `Bearer ` (inclua o espaço após "portador") |
    | `Ocp-Apim-Subscription-Key` | Chave de assinatura do APIM que você registrou anteriormente |

    A URL de solicitação **GET** e os **Cabeçalhos** devem ser semelhantes a:

    ![Interface do usuário do Postman mostrando a URL de solicitação GET e os cabeçalhos](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Selecione o botão **Enviar** no Postman para executar a solicitação. Se você tiver configurado tudo corretamente, deverá receber uma resposta JSON com uma coleção de palestrantes (mostrados aqui truncados):

    ```json
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>Testar uma chamada à API não segura

Agora que você fez uma solicitação bem-sucedida, teste o caso de falha para garantir que as chamadas para sua API com um token *inválido* sejam rejeitadas conforme o esperado. Uma maneira de executar o teste é adicionar ou alterar alguns caracteres no valor do token e, em seguida, executar a mesma solicitação `GET` de antes.

1. Adicione vários caracteres ao valor do token para simular um token inválido. Por exemplo, adicione "INVALID" ao valor do token:

    ![Seção de cabeçalhos da interface do usuário do Postman mostrando INVÁLIDO adicionado ao token](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. Selecione o botão **Enviar** para executar a solicitação. Com um token inválido, o resultado esperado é um código de status não-autorizado `401`:

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

Caso você veja o código de status `401`, isso significa que verificou que somente os chamadores com um token de acesso válido emitido pelo Azure AD B2C podem fazer solicitações bem-sucedidas para sua API de Gerenciamento de API do Azure.

## <a name="support-multiple-applications-and-issuers"></a>Suporte a vários aplicativos e emissores

Normalmente, vários aplicativos interagem com uma única API REST. Para habilitar sua API para aceitar tokens destinados a vários aplicativos, adicione as IDs de aplicativo ao elemento `<audiences>` na política de entrada do APIM.

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

Da mesma forma, para dar suporte a vários emissores de token, adicione os URIs de ponto de extremidade ao elemento `<issuers>` na política de entrada do APIM.

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>Migrar para b2clogin.com

Se você tiver uma API de APIM que valida tokens emitidos pelo ponto de extremidade herdado `login.microsoftonline.com`, deverá migrar a API e os aplicativos que o chamam para usar tokens emitidos pelo [b2clogin.com](b2clogin.md).

Você pode seguir este processo geral para executar uma migração em etapas:

1. Adicione suporte a sua política de entrada do APIM para tokens emitidos por b2clogin.com e login.microsoftonline.com.
1. Atualize seus aplicativos um de cada vez para obter tokens do ponto de extremidade b2clogin.com.
1. Depois que todos os aplicativos estiverem obtendo corretamente tokens de b2clogin.com, remova o suporte para tokens emitidos por login.microsoftonline.com da API.

O exemplo de política de entrada do APIM a seguir ilustra como aceitar tokens emitidos por b2clogin.com e login.microsoftonline.com. Além disso, ele dá suporte a solicitações de API de dois aplicativos.

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>Próximas etapas

Para obter detalhes adicionais sobre as políticas de Gerenciamento de API do Azure, confira o [Índice de referência de política do APIM](../api-management/api-management-policies.md).

Você pode encontrar informações sobre como migrar APIs da Web baseadas em OWIN e seus aplicativos para b2clogin.com em [Migrar uma API da Web baseada em OWIN para b2clogin.com](multiple-token-endpoints.md).