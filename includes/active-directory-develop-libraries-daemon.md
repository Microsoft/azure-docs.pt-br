---
ms.openlocfilehash: 0837c0a23c837065dc2214f947912ee25e4f1d2d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007984"
---
| Idioma/estrutura | Projeto em<br/>GitHub                                                                 | Pacote                                                                                | Introdução<br/>iniciado                           | Conectar usuários                                            | Acessar APIs Web                                                 | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|----------------------|---------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------|:---------------------------------------------:|:--------------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| .NET                 | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)    | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client/) | [Início rápido](../articles/active-directory/develop/quickstart-v2-netcore-daemon.md) | ![A biblioteca não pode solicitar tokens de ID para entrada do usuário.][n] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)        | [msal4j](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html)          | —                                             | ![A biblioteca não pode solicitar tokens de ID para entrada do usuário.][n] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| Nó               | [Nó MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [MSAL-nó](https://www.npmjs.com/package/@azure/msal-node)  | [Início rápido](../articles/active-directory/develop/quickstart-v2-nodejs-console.md)  | ![A biblioteca não pode solicitar tokens de ID para entrada do usuário.][n] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA  |
| Python               | [MSAL Python](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [MSAL-Python](https://github.com/AzureAD/microsoft-authentication-library-for-python)  | [Início rápido](../articles/active-directory/develop/quickstart-v2-python-daemon.md)  | ![A biblioteca não pode solicitar tokens de ID para entrada do usuário.][n] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
<!--
|PHP| [The PHP League oauth2-client](https://oauth2-client.thephpleague.com/usage/) | [League\OAuth2](https://oauth2-client.thephpleague.com/) | ![Green check mark.][n] | ![X indicating no.][n] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/