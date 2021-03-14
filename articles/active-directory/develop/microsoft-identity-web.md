---
title: Visão geral da biblioteca de autenticação na Web do Microsoft Identity
titleSuffix: Microsoft identity platform
description: Saiba mais sobre o Microsoft Identity Web, uma biblioteca de autenticação e autorização para ASP.NET Core aplicativos que se integram com Azure Active Directory, Azure AD B2C e Microsoft Graph e outras APIs da Web.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/09/2020
ms.author: jmprieur
ms.reviewer: marsma
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 8bc9773fa7e1b3db2e249e803f4d2a3cf39edb48
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102539"
---
# <a name="microsoft-identity-web-authentication-library"></a>Biblioteca de autenticação na Web do Microsoft Identity

O Microsoft Identity Web é um conjunto de bibliotecas de ASP.NET Core que simplifica a adição de suporte de autenticação e autorização a aplicativos Web e APIs Web que se integram à plataforma de identidade da Microsoft. Ele fornece uma camada de conveniência de API de superfície única que se une ASP.NET Core, seu middleware de autenticação e a [MSAL (biblioteca de autenticação da Microsoft) para .net](https://github.com/azuread/microsoft-authentication-library-for-dotnet).

## <a name="supported-application-scenarios"></a>Cenários de aplicativos com suporte

Se você estiver criando ASP.NET Core aplicativos Web ou APIs Web e quiser usar Azure Active Directory (AD do Azure) ou Azure AD B2C para IAM (gerenciamento de identidade e acesso), é recomendável usar o Microsoft Identity Web para todos esses cenários:

- [Aplicativo Web que conecta os usuários](scenario-web-app-sign-user-overview.md)
- [Aplicativo Web que conecta usuários e chama uma API da Web em seu nome](scenario-web-app-call-api-overview.md)
- [API Web protegida que somente usuários autenticados podem acessar](scenario-protected-web-api-overview.md)
- [API Web protegida que chama outra API Web (downstream) em nome do usuário conectado](scenario-web-api-call-api-overview.md)

## <a name="get-the-library"></a>Obter a biblioteca

Você pode obter o Microsoft Identity Web do [NuGet](#nuget), dos [modelos de projeto do .NET Core](#project-templates)e do [GitHub](#github).

#### <a name="nuget"></a>NuGet

O Microsoft Identity Web está disponível no NuGet como um conjunto de pacotes que fornecem funcionalidade modular com base nas necessidades do seu aplicativo. Use o comando da CLI do .NET `dotnet add` ou o **Gerenciador de pacotes NuGet** do Visual Studio para instalar os pacotes apropriados para seu projeto:

- [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) -o pacote principal. Exigido por todos os aplicativos que usam o Microsoft Identity Web.
- [Microsoft. Identity. Web. UI](https://www.nuget.org/packages/Microsoft.Identity.Web.UI) -opcional. Adiciona a IU para entrada e saída do usuário e um controlador associado para aplicativos Web.
- [Microsoft. Identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) -opcional. Fornece uma interação simplificada com a API de Microsoft Graph.
- [Microsoft. Identity. Web. MicrosoftGraphBeta](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraphBeta) -opcional. Fornece interação simplificada com o [ponto de extremidade](/graph/api/overview?view=graph-rest-beta&preserve-view=true)de API beta do Microsoft Graph.

#### <a name="project-templates"></a>Modelos de projeto

Os modelos de projeto Web do Microsoft Identity estão incluídos no .NET 5,0 e estão disponíveis para download para projetos ASP.NET Core 3,1.

Se você estiver usando o ASP.NET Core 3,1, instale os modelos com a CLI do .NET:

```dotnetcli
dotnet new --install Microsoft.Identity.Web.ProjectTemplates::1.0.0
```

O diagrama a seguir mostra uma exibição de alto nível dos tipos de aplicativos com suporte e seus argumentos relevantes:

:::image type="content" source="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" lightbox="media/microsoft-identity-web-overview/diagram-microsoft-identity-web-templates.png" alt-text="Diagrama dos modelos de projeto net CLI do ponto disponível para o Microsoft Identity Web":::
<br /><sup><b>*</b></sup>`MultiOrg`não tem suporte com o `webapi2` , mas pode ser habilitado no *appsettings.js* ao definir o locatário como `common` ou`organizations`
<br /><sup><b>**</b></sup>`--calls-graph`não tem suporte para Azure ad B2C

Este exemplo de comando da CLI do .NET, extraído do nosso [tutorial de servidor](tutorial-blazor-server.md)de nossa maioria, gera um novo projeto de servidor de mais novos, que inclui os pacotes corretos e o código inicial (valores de espaço reservado mostrados):

```dotnetcli
dotnet new blazorserver2 --auth SingleOrg --calls-graph --client-id "00000000-0000-0000-0000-000000000000" --tenant-id "11111111-1111-1111-1111-111111111111" --output my-blazor-app
```

#### <a name="github"></a>GitHub

O Microsoft Identity Web é um projeto de código-fonte aberto hospedado no GitHub: <a href="https://github.com/AzureAD/microsoft-identity-web" target="_blank">AzureAD/Microsoft-Identity-Web</a>

O [wiki do repositório](https://github.com/AzureAD/microsoft-identity-web/wiki) contém documentação adicional e, se você precisar de ajuda ou descobrir um bug, poderá [arquivar um problema](https://github.com/AzureAD/microsoft-identity-web/issues).

## <a name="features"></a>Recursos

O Microsoft Identity Web inclui vários recursos não fornecidos se você usar os modelos de projeto padrão do ASP.NET 3,1.

| Recurso                                                                                  | ASP.NET Core 3,1                                                     | Microsoft Identity Web                                                                                  |
|------------------------------------------------------------------------------------------|----------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| [Conectar usuários](scenario-web-app-sign-user-app-configuration.md) em aplicativos Web             | <li>Contas corporativas ou de estudante<li>Identidades sociais (com Azure AD B2C) | <li>Contas corporativas ou de estudante<li>Contas Microsoft pessoais<li>Identidades sociais (com Azure AD B2C)     |
| [Proteger APIs Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) | <li>Contas corporativas ou de estudante<li>Identidades sociais (com Azure AD B2C) | <li>Contas corporativas ou de estudante<li>Contas Microsoft pessoais<li>Identidades sociais (com Azure AD B2C)     |
| Validação do emissor em aplicativos multilocatários                                                   | Não                                                                   | Sim, para [todas as nuvens](authentication-national-cloud.md) e [Azure ad B2C](../../active-directory-b2c/index.yml) |
| Aplicativo Web/API [chama o Microsoft Graph] [cenário-API-Call-Graph]                             | Não                                                                   | Sim                                                                                                     |
| Aplicativo Web/API [chama API Web] [cenário-API-Call-API]                                       | Não                                                                   | Sim                                                                                                     |
| Dá suporte a credenciais de certificado                                                         | Não                                                                   | Sim, incluindo Azure Key Vault                                                                          |
| Consentimento incremental e suporte de acesso condicional em aplicativos Web                           | Não                                                                   | Sim, em MVC, páginas Razor e mais                                                                    |
| Certificados de criptografia de token em APIs Web                                                | Não                                                                   | Sim                                                                                                     |
| [Escopos/validação de função de aplicativo] [cenário-validação de API] em APIs Web                        | Não                                                                   | Sim                                                                                                     |
| `WWW-Authenticate` geração de cabeçalho em APIs Web                                         | Não                                                                   | Sim                                                                                                     |

## <a name="next-steps"></a>Próximas etapas

Para ver o Microsoft Identity Web em ação, experimente nosso tutorial de servidor mais incrivelmente:

[Tutorial: Criar um aplicativo Blazor Server que usa a plataforma de identidade da Microsoft para autenticação](tutorial-blazor-server.md)

O Microsoft Identity Web wiki no GitHub contém uma documentação de referência extensiva para vários aspectos da biblioteca. Por exemplo, o uso de certificado, o consentimento incremental e a referência de acesso condicional podem ser encontrados aqui:

- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates" target="_blank">Usando certificados com Microsoft. Identity. Web</a> (github)
- <a href="https://github.com/AzureAD/microsoft-identity-web/wiki/Managing-incremental-consent-and-conditional-access" target="_blank">Consentimento incremental e acesso condicional</a> (github)

<!-- LINKS -->
<!--  [miw-certs]: microsoft-identity-web-certificates.md  -->
<!--  [miw-certs-decrypt]: microsoft-identity-web-certificates.md#decryption-certificates  -->
<!--  [miw-inc-consent-ca-header]: microsoft-identity-web-consent-conditional-access.md#handling-incremental-consent-or-conditional-access-in-web-apis  -->
<!--  [miw-inc-consent-ca]: microsoft-identity-web-consent-conditional-access.md  -->
[cenário-API-Call-API]: cenário-Web-API-Call-API-Call-API. MD # opção-1-Call-Microsoft-Graph-with-the-SDK  
[cenário-API-Call-Graph]: cenário-Web-API-Call-API-Call-API. MD # opção-1-Call-Microsoft-Graph-with-the-SDK  
[cenário-validação de API]: scenario-protected-web-api-verification-scope-app-roles.md  
