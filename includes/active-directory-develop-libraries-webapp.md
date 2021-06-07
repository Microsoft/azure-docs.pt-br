---
ms.openlocfilehash: 124290321815d22cad74d235205401b9380ff395
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "104578355"
---
| Idioma/estrutura | Projeto em<br/>GitHub                                                                                     | Pacote                                                                                                    | Introdução<br/>iniciado                               | Conectar usuários                                            | Acessar APIs Web                                                    | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------|:-------------------------------------------------:|:--------------------------------------------------------:|:------------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)                      | —                                                 | ![A biblioteca não pode solicitar tokens de ID para entrada do usuário.][n] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | GA                                                           |
| ASP.NET Core         | [Segurança do ASP.NET](/aspnet/core/security/)                                                                | [Microsoft.AspNetCore.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication/) | —                                                 | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca não pode solicitar tokens de acesso para APIs Web protegidas.][n] | GA                                                           |
| ASP.NET Core         | [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web)                               | [Microsoft. Identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web)                            | —                                                 | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://search.maven.org/artifact/com.microsoft.azure/msal4j)                                     |[Início rápido](../articles/active-directory/develop/quickstart-v2-java-webapp.md) | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | GA                                                           |
| Node.js              | [Nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [MSAL-nó](https://www.npmjs.com/package/@azure/msal-node)                                                | [Início rápido](../articles/active-directory/develop/quickstart-v2-nodejs-webapp-msal.md) | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | GA                                               |
| Node.js              | [Azure AD Passport](https://github.com/AzureAD/passport-azure-ad)                                         | [Passport-Azure-AD](https://www.npmjs.com/package/passport-azure-ad)                                       | [Início rápido](../articles/active-directory/develop/quickstart-v2-nodejs-webapp.md)      | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)                     | [MSAL](https://pypi.org/project/msal)                                                                      | [Início rápido](../articles/active-directory/develop/quickstart-v2-python-webapp.md)      | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y]    | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y]    | GA                                                           |
<!--
| Java | [ScribeJava](https://github.com/scribejava/scribejava) | [ScribeJava 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Java | [Gluu oxAuth](https://github.com/GluuFederation/oxAuth) | [oxAuth 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| Node.js | [openid-client](https://github.com/panva/node-openid-client/) | [openid-client 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| PHP | [PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [oauth2-client 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
| Ruby | [OmniAuth](https://github.com/omniauth/omniauth) | [omniauth 1.3.1](https://github.com/omniauth/omniauth/releases/tag/v1.3.1)<br/>[omniauth-oauth2 1.4.0](https://github.com/intridea/omniauth-oauth2) | ![X indicating no.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/