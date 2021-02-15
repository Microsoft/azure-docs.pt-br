---
title: Trocas de declarações da API REST em políticas personalizadas B2C
titleSuffix: Azure AD B2C
description: Uma introdução à criação de uma jornada de usuário do Azure AD B2C que interage com os serviços RESTful.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89594179"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>Integrar trocas de declarações da API REST em sua política personalizada no Azure AD B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

A Identity Experience Framework subjacente ao Azure AD B2C (Azure Active Directory B2C), pode se integrar a APIs RESTful em um percurso do usuário. Este artigo mostra como criar um percurso do usuário que interage com um serviço RESTful usando um [perfil técnico RESTful](restful-technical-profile.md).

Usando o Azure AD B2C, é possível adicionar sua própria lógica de negócios a um percurso do usuário chamando o seu próprio serviço RESTful. A Identity Experience Framework pode enviar e receber dados de seu serviço RESTful para trocar declarações. Por exemplo, você pode:

- **Validar os dados de entrada do usuário**. Por exemplo, você pode verificar se o endereço de email fornecido pelo usuário existe no banco de dados do cliente e, se não tiver, apresente um erro.
- **Processar declarações**. Se um usuário inserir o nome com todas as letras maiúsculas ou minúsculas, sua API REST poderá formatar o nome usando somente a primeira letra maiúscula e retorná-la ao Azure AD B2C.
- **Aprimorar os dados do usuário com a integração adicional a aplicativos de linha de negócios corporativos**. Seu serviço RESTful pode receber o endereço de email do usuário, consultar o banco de dados do cliente e retornar o número de fidelidade do usuário ao Azure AD B2C. As declarações retornadas podem ser armazenadas na conta do Azure AD do usuário, avaliadas nas próximas etapas de orquestração ou incluídas no token de acesso.
- **Executar a lógica de negócios personalizada**. Você pode enviar notificações por push, atualizar bancos de dados corporativos, executar um processo de migração de usuário, gerenciar permissões, auditar bancos de dados e realizar qualquer outro fluxo de trabalho.

![Diagrama de uma troca de declarações do serviço RESTful](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> Se houver lentidão ou nenhuma resposta do serviço RESTful para Azure AD B2C, o tempo limite será de 30 segundos e a contagem de repetições será 2 vezes (o que significa que há 3 tentativas no total). As configurações de tempo limite e número de tentativas não são configuráveis no momento.

## <a name="calling-a-restful-service"></a>Chamando um serviço RESTful

A interação inclui uma troca de declarações de informações entre as declarações da API REST e o Azure AD B2C. É possível criar a integração com os serviços RESTful das seguintes maneiras:

- **Perfil técnico de validação**. A chamada para o serviço RESTful ocorre dentro de um [perfil técnico de validação](validation-technical-profile.md) do [perfil técnico autodeclarado](self-asserted-technical-profile.md) especificado ou um [controle de exibição de verificação](display-control-verification.md) de um [controle de exibição](display-controls.md). O perfil técnico de validação valida os dados fornecido pelo usuário para que o percurso do usuário possa avançar. Com o perfil técnico de validação, é possível:

  - Enviar declarações para sua API REST.
  - Validar as declarações e gerar mensagens de erro personalizadas que são exibidas para o usuário.
  - Enviar declarações de retorno da API REST para as etapas de orquestração subsequentes.

- **Troca de declarações**. Uma troca de declarações direta pode ser configurada chamando um perfil técnico da API REST diretamente de uma etapa de orquestração de um [percurso do usuário](userjourneys.md). Essa definição é limitada a:

  - Enviar declarações para sua API REST.
  - Validar as declarações e gerar mensagens de erro personalizadas que são retornadas para o aplicativo.
  - Enviar declarações de retorno da API REST para as etapas de orquestração subsequentes.

Você pode adicionar uma chamada à API REST em qualquer etapa no percurso do usuário definido por uma política personalizada. Por exemplo, você pode chamar uma API REST:

- Durante a entrada, logo antes de o Azure AD B2C validar as credenciais.
- Imediatamente após a entrada.
- Antes que o Azure AD B2C crie uma conta no diretório.
- Depois que o Azure AD B2C criar uma conta no diretório.
- Antes que o Azure AD B2C emita um token de acesso.

![Coleção do perfil técnico de validação](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>Enviar dados

No [perfil técnico RESTful](restful-technical-profile.md), o elemento `InputClaims` contém uma lista de declarações a serem enviadas ao serviço RESTful. Você pode mapear o nome da sua declaração para o nome definido no serviço RESTful, definir um valor padrão e usar [resolvedores de declarações](claim-resolver-overview.md).

Você pode configurar como as declarações de entrada são enviadas para o provedor de declarações RESTful usando o atributo SendClaimsIn. Os valores possíveis são:

- **Body**, enviado no corpo da solicitação HTTP POST no formato JSON.
- **Form**, enviado no corpo da solicitação HTTP POST em um formato de valor de chave separado de e comercial “&”.
- **Header**, enviado no cabeçalho de solicitação HTTP GET.
- **QueryString**, enviado na cadeia de caracteres de consulta de solicitação HTTP GET.

Quando a opção **Body** estiver configurada, o perfil técnico da API REST permitirá que você envie um payload JSON complexo para um ponto de extremidade. Para obter mais informações, confira [Enviar um payload JSON](restful-technical-profile.md#send-a-json-payload).

## <a name="receiving-data"></a>Recebendo dados

O elemento `OutputClaims` do [perfil técnico RESTful](restful-technical-profile.md) contém uma lista de declarações retornadas pela API REST. Talvez seja necessário mapear o nome da declaração definida em sua política para o nome definido na API REST. Você também pode incluir declarações que não são retornadas pelo provedor de identidade de API REST, desde que defina o atributo DefaultValue.

As declarações de saída analisadas pelo provedor de declarações RESTful sempre esperam analisar uma resposta de corpo JSON simples, como:

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

As declarações de saída devem ser semelhantes ao seguinte:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

Para analisar uma resposta de corpo JSON aninhada, defina os metadados de ResolveJsonPathsInJsonTokens como true. Na declaração de saída, defina PartnerClaimType como o elemento de caminho JSON que você deseja gerar.

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


As declarações de saída devem ser semelhantes ao seguinte:

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>Considerações de segurança

Você deve proteger seu ponto de extremidade de API REST para que somente clientes autenticados possam se comunicar com ele. A API REST deve usar um ponto de extremidade HTTPS. Defina os metadados de AuthenticationType para um dos seguintes métodos de autenticação:

- **Certificado do cliente** restringe o acesso usando a autenticação de certificado do cliente. Somente os serviços que têm certificados adequados poderão acessar sua API. Você armazena o certificado de cliente em uma chave de política do Azure AD B2C. Saiba mais sobre como [proteger seu serviço RESTful usando certificados do cliente](secure-rest-api.md#https-client-certificate-authentication).
- **Básico** protege a API REST com autenticação básica HTTP. Somente usuários verificados, incluindo Azure AD B2C, podem acessar a API. O nome de usuário e a senha são armazenados em chaves de política do Azure AD B2C. Saiba como [proteger seus serviços RESTful usando a autenticação Básica HTTP](secure-rest-api.md#http-basic-authentication).
- **Portador** restringe o acesso usando um token de acesso OAuth2 cliente. O token de acesso é armazenado em uma chave de política do Azure AD B2C. Saiba mais sobre como [proteger seu serviço RESTful usando token do portador](secure-rest-api.md#oauth2-bearer-authentication).

## <a name="rest-api-platform"></a>Plataforma da API REST
Sua API REST pode ser baseada em qualquer plataforma e escrita em qualquer linguagem de programação, desde que seja segura e possa enviar e receber declarações conforme especificado no [perfil técnico RESTful](restful-technical-profile.md).

## <a name="localize-the-rest-api"></a>Localizar a API REST
Em um perfil técnico RESTful, talvez você queira enviar o idioma/localidade da sessão atual e, se necessário, gerar uma mensagem de erro localizada. Usando o [resolvedor de declarações](claim-resolver-overview.md), você pode enviar uma declaração contextual, como o idioma do usuário. O exemplo a seguir mostra um perfil técnico RESTful demonstrando esse cenário.

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>Tratamento de mensagens de erro

A API REST talvez precise retornar uma mensagem de erro, como "O usuário não foi encontrado no sistema CRM". Se um erro ocorrer, a API REST deverá retornar uma mensagem de erro HTTP 409 (código de status de resposta de Conflito). Para saber mais, confira [Perfil técnico RESTful](restful-technical-profile.md#returning-validation-error-message).

Isso só pode ser obtido com a chamada de um perfil técnico da API REST de um perfil técnico de validação. Isso permite que o usuário corrija os dados na página e execute a validação novamente no envio da página.

Uma resposta HTTP 409 é necessária para impedir o processamento de quaisquer perfis técnicos de validação subsequentes nesta etapa de orquestração.

Se você fizer referência a um perfil técnico da API REST diretamente de um percurso do usuário, o usuário será redirecionado de volta para o aplicativo de terceiros confiável com a mensagem de erro relevante.

## <a name="publishing-your-rest-api"></a>Publicando sua API REST

A solicitação para seu serviço de API REST vem de servidores do Azure AD B2C. O serviço de API REST deve ser publicado em um ponto de extremidade HTTPS publicamente acessível. As chamadas à API REST chegarão de um endereço IP do datacenter do Azure.

Projete seu serviço de API REST e seus componentes subjacentes (como o banco de dados e o sistema de arquivos) para serem altamente disponíveis.

## <a name="next-steps"></a>Próximas etapas

Confira os seguintes artigos para obter exemplos de como usar um perfil técnico RESTful:

- [Passo a passo: Integrar as trocas de declarações da API REST no percurso do usuário do Azure AD B2C como validação da entrada do usuário](custom-policy-rest-api-claims-validation.md)
- [Passo a passo: Adicionar trocas de declarações da API REST a políticas personalizadas no Azure Active Directory B2C](custom-policy-rest-api-claims-validation.md)
- [Proteger seus serviços de API REST](secure-rest-api.md)
- [Referência: Perfil técnico RESTful](restful-technical-profile.md)
