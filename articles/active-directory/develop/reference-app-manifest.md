---
title: Entendendo o manifesto do aplicativo do Azure Active Directory
titleSuffix: Microsoft identity platform
description: A cobertura detalhada do manifesto do aplicativo do Azure Active Directory, que representa a configuração de identidade de um aplicativo em um locatário do Azure AD, e é usado para facilitar a autorização do OAuth, a experiência de consentimento e muito mais.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 02/02/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 0291d2e6f0cee07bd7164b63dfd4ac8b02c42a01
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583036"
---
# <a name="azure-active-directory-app-manifest"></a>Manifesto do aplicativo do Azure Active Directory

O manifesto do aplicativo contém uma definição de todos os atributos de um objeto de aplicativo na plataforma de identidade da Microsoft. Ele também serve como um mecanismo para atualizar o objeto do aplicativo. Para obter mais informações sobre a entidade Aplicativo e seu esquema, confira a [Documentação da entidade do Aplicativo da API do Graph](/graph/api/resources/application).

Você pode configurar os atributos de um aplicativo por meio do portal do Azure ou programaticamente usando a [API REST](/graph/api/resources/application) ou o [PowerShell](/powershell/module/azuread#applications). No entanto, existem alguns cenários em que você precisará editar o manifesto do aplicativo para configurar o atributo de um aplicativo. Esses cenários incluem:

* Se você registrou o aplicativo como multilocatário do Azure AD e contas pessoais da Microsoft, não é possível alterar as contas da Microsoft compatíveis na interface do usuário. Em vez disso, você deve usar o editor de manifesto do aplicativo para alterar o tipo de conta suportado.
* Para definir permissões e funções às quais seu aplicativo dá suporte, você deve modificar o manifesto do aplicativo.

## <a name="configure-the-app-manifest"></a>Configurar o manifesto do aplicativo

Para configurar o manifesto do aplicativo:

1. Vá para a <a href="https://portal.azure.com/" target="_blank">portal do Azure <span class="docon docon-navigate-external x-hidden-focus"></span> </a>. Procurar e selecionar o serviço **Azure Active Directory**.
1. Selecione **Registros do Aplicativo**.
1. Selecione o aplicativo que você deseja configurar.
1. Na página **Visão Geral** do aplicativo, selecione a seção **Manifesto**. Um editor de manifesto baseado na Web é aberto, permitindo que você edite o manifesto no portal. Opcionalmente, você pode selecionar **Download** para editar o manifesto localmente e usar **Upload** para reaplicá-lo ao seu aplicativo.

## <a name="manifest-reference"></a>Referência do manifesto

Esta seção descreve os atributos encontrados no manifesto do aplicativo.

### <a name="id-attribute"></a>ID do atributo

| Chave | Tipo de valor |
| :--- | :--- |
| id | String |

O identificador exclusivo do aplicativo no diretório. Essa ID não é o identificador usado para identificar o aplicativo em qualquer transação de protocolo. Ele é usado para referenciar o objeto em consultas de diretório.

Exemplo:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>atributo accessTokenAcceptedVersion

| Chave | Tipo de valor |
| :--- | :--- |
| accessTokenAcceptedVersion | Int32 anulável |

Especifica a versão do token de acesso esperada pelo recurso. Esse parâmetro altera a versão e o formato do JWT produzido, independentemente do ponto de extremidade ou do cliente usado para solicitar o token de acesso.

O ponto de extremidade usado, v1.0 ou v2.0, é escolhido pelo cliente e só afeta a versão do id_tokens. Os recursos precisam configurar explicitamente `accesstokenAcceptedVersion` para indicar o formato do token de acesso com suporte.

Os valores possíveis para `accesstokenAcceptedVersion` são 1, 2 ou nulo. Se o valor for nulo, esse parâmetro usará 1 por padrão, o que corresponde ao ponto de extremidade v1.0.

Se `signInAudience` é `AzureADandPersonalMicrosoftAccount`, o valor precisa ser `2`.

Exemplo:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>atributo addIns

| Chave | Tipo de valor |
| :--- | :--- |
| addIns | Coleção |

Define o comportamento personalizado que um serviço de consumo pode usar para chamar um aplicativo em contextos específicos. Por exemplo, os aplicativos que podem renderizar fluxos de arquivos podem definir a propriedade `addIns` para a funcionalidade "FileHandler" dessa propriedade. Esse parâmetro permitirá que serviços, como Microsoft 365, chamem o aplicativo no contexto de um documento no qual o usuário está trabalhando.

Exemplo:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>atributo allowPublicClient

| Chave | Tipo de valor |
| :--- | :--- |
| allowPublicClient | Boolean |

Especifica o tipo de aplicativo de fallback. O Azure AD infere o tipo de aplicativo do replyUrlsWithType por padrão. Há determinados cenários em que o Azure AD não pode determinar o tipo de aplicativo cliente. Por exemplo, um cenário desse tipo é o fluxo [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) em que a solicitação HTTP ocorre sem um redirecionamento de URL). Nesses casos, o Azure AD interpretará o tipo de aplicativo com base no valor dessa propriedade. Se esse valor for definido como true, o tipo de aplicativo de fallback será definido como cliente público, como um aplicativo instalado em execução em um dispositivo móvel. O valor padrão é false, que significa que o tipo de aplicativo de fallback é cliente confidencial, como o aplicativo Web.

Exemplo:

```json
    "allowPublicClient": false,
```

### <a name="appid-attribute"></a>atributo appId

| Chave | Tipo de valor |
| :--- | :--- |
| appId | String |

Especifica o identificador exclusivo do aplicativo que é atribuído a um aplicativo pelo Azure AD.

Exemplo:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>atributo appRoles

| Chave | Tipo de valor |
| :--- | :--- |
| appRoles | Coleção |

Especifica a coleção de funções que um aplicativo pode declarar. Essas funções podem ser atribuídas a usuários, grupos ou entidades de serviço. Para ver exemplos e mais informações, confira [Adicionar funções de aplicativo ao seu aplicativo e recebê-las no token](howto-add-app-roles-in-azure-ad-apps.md).

Exemplo:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="errorurl-attribute"></a>atributo errorUrl

| Chave | Tipo de valor |
| :--- | :--- |
| errorUrl | String |

Incompatível.

### <a name="groupmembershipclaims-attribute"></a>atributo groupMembershipClaims

| Chave | Tipo de valor |
| :--- | :--- |
|GroupMembershipClaims | String |

Configura a declaração `groups` emitida em um usuário ou o token de acesso OAuth 2.0 que o aplicativo espera. Para definir esse atributo, use um dos seguintes valores válidos da cadeia de caracteres:

- `"None"`
- `"SecurityGroup"` (para grupos de segurança e funções do Azure AD)
- `"ApplicationGroup"` (essa opção inclui apenas os grupos atribuídos ao aplicativo)
- `"DirectoryRole"` (Obtém as funções de diretório do Azure AD das quais o usuário é membro)
- `"All"` (isso obterá todos os grupos de segurança, grupos de distribuição e funções de diretório do Azure AD dos quais o usuário conectado é membro).

Exemplo:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="optionalclaims-attribute"></a>atributo optionalClaims

| Chave | Tipo de valor |
| :--- | :--- |
| optionalClaims | String |

As declarações opcionais retornadas no token pelo serviço de token de segurança para este aplicativo específico.

No momento, os aplicativos que oferecem suporte a contas pessoais e ao Azure AD (registrados por meio do portal de registro de aplicativos) não podem usar declarações opcionais. No entanto, os aplicativos registrados apenas para o Azure AD usando o ponto de extremidade v2.0 podem obter as declarações opcionais solicitadas no manifesto. Para obter mais informações, confira [Declarações opcionais](active-directory-optional-claims.md).

Exemplo:

```json
    "optionalClaims": null,
```


### <a name="identifieruris-attribute"></a>atributo identifierUris

| Chave | Tipo de valor |
| :--- | :--- |
| identifierUris | Matriz de cadeia de caracteres |

Os URIs definidos pelo usuário que identificam exclusivamente um aplicativo Web em seu locatário do Azure AD ou em um domínio personalizado verificado, quando o aplicativo é multilocatário.

Exemplo:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>atributo informationalUrls

| Chave | Tipo de valor |
| :--- | :--- |
| informationalUrls | String |

Especifica os links para os termos de serviço e a política de privacidade do aplicativo. Os termos de serviço e a declaração de privacidade são revelados aos usuários por meio da experiência de consentimento do usuário. Para obter mais informações, consulte [Como: Adicionar termos de serviço e declaração de privacidade para aplicativos registrados do Azure AD](howto-add-terms-of-service-privacy-statement.md).

Exemplo:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>atributo keyCredentials

| Chave | Tipo de valor |
| :--- | :--- |
| keyCredentials | Coleção |

Contém referências a credenciais, segredos compartilhados com base em cadeia de caracteres e certificados X.509 atribuídos ao aplicativo. Essas credenciais são usadas ao solicitar tokens de acesso (quando o aplicativo está agindo como um cliente e não como um recurso).

Exemplo:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>atributo knownClientApplications

| Chave | Tipo de valor |
| :--- | :--- |
| knownClientApplications | Matriz de cadeia de caracteres |

Usado para agrupamento de consentimento no caso de uma solução que contenha duas partes: um aplicativo cliente e um aplicativo de API Web personalizado. Se você inserir a appID do aplicativo cliente nesse valor, o usuário precisará consentir somente uma vez no aplicativo cliente. O Azure AD saberá que o consentimento para o cliente significa implicitamente consentir à API Web. Ele provisionará automaticamente as entidades de serviço para o cliente e a API Web, simultaneamente. O cliente e o aplicativo de API Web precisam ser registrados no mesmo locatário.

Exemplo:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>atributo logoUrl

| Chave | Tipo de valor |
| :--- | :--- |
| logoUrl | String |

Leia apenas o valor que aponta para a URL da CDN para o logotipo que foi carregado no portal.

Exemplo:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>atributo logoutUrl

| Chave | Tipo de valor |
| :--- | :--- |
| logoutUrl | String |

A URL para fazer logoff do aplicativo.

Exemplo:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>atributo name

| Chave | Tipo de valor |
| :--- | :--- |
| name | String |

O nome de exibição do aplicativo.

Exemplo:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>atributo oauth2AllowImplicitFlow

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolean |

Especifica se este aplicativo Web pode solicitar tokens de acesso de fluxo implícitos OAuth 2.0. O padrão é false. Esse sinalizador é usado para aplicativos baseados em navegador, como os aplicativos de página única JavaScript. Para saber mais, digite `OAuth 2.0 implicit grant flow` no sumário e consulte os tópicos sobre fluxo implícito.

Exemplo:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>atributo oauth2AllowIdTokenImplicitFlow

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolean |

Especifica se este aplicativo Web pode solicitar os tokens de ID de fluxo implícitos OAuth 2.0. O padrão é false. Esse sinalizador é usado para aplicativos baseados em navegador, como os aplicativos de página única JavaScript.

Exemplo:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>atributo oauth2Permissions

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2Permissions | Coleção |

Especifica a coleção de escopos de permissão do OAuth 2.0 que o aplicativo de API Web (recurso) expõe aos aplicativos clientes. Esses escopos de permissões podem ser concedidos a aplicativos clientes durante o consentimento.

Exemplo:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>atributo oauth2RequiredPostResponse

| Chave | Tipo de valor |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolean |

Especifica se, como parte das solicitações de token OAuth 2.0, o Azure AD permitirá solicitações POST, em vez de solicitações GET. O padrão é false, que especifica que somente as solicitações GET serão permitidas.

Exemplo:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>atributo parentalControlSettings

| Chave | Tipo de valor |
| :--- | :--- |
| parentalControlSettings | String |

- `countriesBlockedForMinors` especifica os países/regiões em que o aplicativo está bloqueado para menores.
- `legalAgeGroupRule` especifica a regra de grupo de faixa etária que se aplica a usuários do aplicativo. Pode ser definido como `Allow`, `RequireConsentForPrivacyServices`, `RequireConsentForMinors`, `RequireConsentForKids` ou `BlockMinors`.

Exemplo:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>atributo passwordCredentials

| Chave | Tipo de valor |
| :--- | :--- |
| passwordCredentials | Coleção |

Confira a descrição da propriedade `keyCredentials`.

Exemplo:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>atributo preAuthorizedApplications

| Chave | Tipo de valor |
| :--- | :--- |
| preAuthorizedApplications | Coleção |

Lista os aplicativos e as permissões solicitadas para consentimento implícito. Exige que um administrador tenha fornecido o consentimento para o aplicativo. preAuthorizedApplications não exigem que o usuário consinta com as permissões solicitadas. As permissões listadas em preAuthorizedApplications não exigem o consentimento do usuário. No entanto, quaisquer permissões solicitadas adicionais não listadas no preAuthorizedApplications exigem o consentimento do usuário.

Exemplo:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publisherdomain-attribute"></a>atributo publisherDomain

| Chave | Tipo de valor |
| :--- | :--- |
| publisherDomain | String |

O domínio do fornecedor verificado para o aplicativo. Somente leitura.

Exemplo:

```json
    "publisherDomain": "{tenant}.onmicrosoft.com",
```

### <a name="replyurlswithtype-attribute"></a>atributo replyUrlsWithType

| Chave | Tipo de valor |
| :--- | :--- |
| replyUrlsWithType | Coleção |

Essa propriedade de vários valores contém a lista de valores redirect_uri registrados que o Azure AD aceitará como destinos quando retornar tokens. Cada valor de URI deve conter um valor de tipo de aplicativo associado. Os valores de tipo compatíveis são:

- `Web`
- `InstalledClient`
- `Spa`

Para saber mais, confira [restrições e limitações do replyUrl](./reply-url.md).

Exemplo:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>atributo requiredResourceAccess

| Chave | Tipo de valor |
| :--- | :--- |
| requiredResourceAccess | Coleção |

Com o consentimento dinâmico, o `requiredResourceAccess` gera a experiência de consentimento do administrador e a experiência de consentimento do usuário para usuários que estão usando o consentimento estático. No entanto, esse parâmetro não gera a experiência de consentimento do usuário para o caso geral.

- `resourceAppId` é o identificador exclusivo do recurso ao qual o aplicativo requer acesso. Esse valor deve ser igual à appId declarada no aplicativo do recurso de destino.
- `resourceAccess` é uma matriz que lista os escopos de permissão do OAuth2.0 e as funções de aplicativo que o aplicativo exige do recurso especificado. Contém os valores `id` e `type` dos recursos especificados.

Exemplo:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>atributo samlMetadataUrl

| Chave | Tipo de valor |
| :--- | :--- |
| samlMetadataUrl | String |

A URL dos metadados SAML do aplicativo.

Exemplo:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>atributo signInUrl

| Chave | Tipo de valor |
| :--- | :--- |
| signInUrl | String |

Especifica a URL da home page do aplicativo.

Exemplo:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>atributo signInAudience

| Chave | Tipo de valor |
| :--- | :--- |
| signInAudience | String |

Especifica quais contas da Microsoft são suportadas para o aplicativo atual. Os valores com suporte são:
- `AzureADMyOrg` – usuários com uma conta corporativa ou de estudante da Microsoft no locatário do Azure AD da minha organização (por exemplo, locatário único)
- `AzureADMultipleOrgs` – usuários com uma conta corporativa ou de estudante da Microsoft no locatário do Azure AD de qualquer organização (por exemplo, multilocatário)
- `AzureADandPersonalMicrosoftAccount` – usuários com uma conta Microsoft pessoal ou uma conta corporativa ou de estudante no locatário do Azure AD de qualquer organização
- `PersonalMicrosoftAccount` – Contas pessoais usadas para entrar em serviços como Xbox e Skype.

Exemplo:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>atributo tags

| Chave | Tipo de valor |
| :--- | :--- |
| marcas | Matriz de cadeia de caracteres  |

Cadeias de caracteres personalizadas que podem ser usadas para categorizar e identificar o aplicativo.

Exemplo:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>Problemas comuns

### <a name="manifest-limits"></a>Limites de manifesto

Um manifesto de aplicativo tem vários atributos que são chamados de coleções; por exemplo, appRoles, keyCredentials, knownClientApplications, identifierUris, redirectUris, requiredResourceAccess e oauth2Permissions. No manifesto completo do aplicativo para qualquer aplicativo, o número total de entradas em todas as coleções combinadas tem sido limitado a 1.200. Se você especificar antecipadamente 100 URIs de redirecionamento no manifesto do aplicativo, restarão a você apenas 1.100 entradas para usar em todas as outras coleções combinadas que compõem o manifesto.

> [!NOTE]
> Caso você tente adicionar mais de 1.200 entradas no manifesto do aplicativo, poderá ver o erro **"Falha ao atualizar o aplicativo xxxxxx. Detalhes do erro: O tamanho do manifesto excedeu o limite. Reduza o número de valores e repita a solicitação".**

### <a name="unsupported-attributes"></a>Atributos incompatíveis

O manifesto do aplicativo representa o esquema do modelo de aplicativo subjacente no Azure AD. Conforme o esquema subjacente evolui, o editor de manifesto será atualizado para refletir o novo esquema de tempos em tempos. Como resultado, você pode notar novos atributos aparecendo no manifesto do aplicativo. Em raras ocasiões, você pode notar uma alteração sintática ou semântica nos atributos existentes ou pode encontrar um atributo que existia anteriormente mas não é mais compatível. Por exemplo, você verá novos atributos nos [Registros de aplicativo](https://go.microsoft.com/fwlink/?linkid=2083908) que são conhecidos com um nome diferente na experiência Registros de aplicativo (Herdados).

| Registros de aplicativo (Herdados)| Registros de aplicativo           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

Para obter descrições para esses atributos, confira a seção [referência de manifesto](#manifest-reference).

Ao tentar carregar um manifesto baixado anteriormente, você poderá ver um dos erros a seguir. Esse erro é provável porque o editor de manifesto agora dá suporte a uma versão mais recente do esquema, que não corresponde àquela que você está tentando carregar.

* "Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Identificador de objeto inválido 'indefinido'. []."
* "Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Um ou mais valores de propriedade especificados são inválidos. []."
* "Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Não é permitido definir availableToOtherTenants nesta versão de API para atualização. []."
* "Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Atualizações à propriedade 'replyUrls' não são permitidas para este aplicativo. Use a propriedade 'replyUrlsWithType' em seu lugar. []."
* "Falha ao atualizar o aplicativo xxxxxx. Detalhe do erro: Um valor sem um nome de tipo foi encontrado e nenhum tipo esperado está disponível. Quando o modelo é especificado, cada valor no conteúdo precisa ter um tipo que pode ser especificado no conteúdo, seja explicitamente pelo chamador ou inferido implicitamente do valor pai. []"

Quando você vir um desses erros, recomendamos as seguintes ações:

1. Edite os atributos individualmente no editor de manifesto em vez de carregar um manifesto baixado anteriormente. Use a tabela [referência de manifesto](#manifest-reference) para entender a sintaxe e a semântica dos atributos novos e antigos, a fim de poder editar com êxito os atributos nos quais você está interessado.
1. Se o fluxo de trabalho exigir que você salve os manifestos no repositório de origem para uso posterior, sugerimos realocar os manifestos salvos em seu repositório com aquele que você vê na experiência de **Registros de aplicativo**.

## <a name="next-steps"></a>Próximas etapas

* Para obter mais informações sobre a relação entre os objetos aplicativo e entidade de serviço do aplicativo, confira [Objetos de aplicativo e de entidade de serviço no Azure AD](app-objects-and-service-principals.md).
* Confira o [Glossário de desenvolvedor da plataforma de identidade da Microsoft](developer-glossary.md) para obter definições de alguns conceitos principais do desenvolvedor da plataforma de identidade da Microsoft.

Use a seção de comentários a seguir para dar sua opinião e nos ajudar a aprimorar e adaptar nosso conteúdo.

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
