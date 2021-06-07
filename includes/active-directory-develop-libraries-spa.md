---
ms.openlocfilehash: a393cf978318c39081805cae7e38c3386ff156f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103010687"
---
| Idioma/estrutura | Projeto em<br/>GitHub                                                                                                    | Pacote                                                                      | Introdução<br/>iniciado                             | Conectar usuários                                         | Acessar APIs Web                                                 | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|----------------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|:-----------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Angular              | [MSAL angular v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular)<sup>2</sup>         | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     | —                                               | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | Versão prévia pública                                               |
| Angular              | [MSAL angular](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/msal-angular-v1/lib/msal-angular)<sup>3</sup> | [@azure/msal-angular](https://www.npmjs.com/package/@azure/msal-angular)     |[Tutorial](../articles/active-directory/develop/tutorial-v2-angular.md)| ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| AngularJS            | [MSAL AngularJS](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-angularjs)<sup>3</sup>         | [@azure/msal-angularjs](https://www.npmjs.com/package/@azure/msal-angularjs) | —                                               | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | Versão prévia pública                                               |
| JavaScript           | [MSAL.js v2](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser)<sup>2</sup>              | [@azure/msal-browser](https://www.npmjs.com/package/@azure/msal-browser)     | [Tutorial](../articles/active-directory/develop/tutorial-v2-javascript-auth-code.md) | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
|JavaScript|[MSAL.js 1,0](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core)<sup>3</sup> | [@azure/msal-core](https://www.npmjs.com/package/@azure/msal-core)    | [Tutorial](../articles/active-directory/develop/tutorial-v2-javascript-spa.md)| ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| React                | [MSAL reagir](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-react)<sup>2</sup>                 | [@azure/msal-react](https://www.npmjs.com/package/@azure/msal-react)         | —                                               | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | Versão prévia pública                                               |
<!--
| Vue | [Vue MSAL]( https://github.com/mvertopoulos/vue-msal) | [vue-msal]( https://www.npmjs.com/package/vue-msal) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

<sup>2</sup> o [fluxo de código de autenticação][auth-code-flow] somente com PCKE (recomendado). 

<sup>3</sup> somente [fluxo de concessão implícita][implicit-flow] .

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[Microsoft-SDL]: https://www.microsoft.com/securityengineering/sdl/
[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[auth-code-flow]: ../articles/active-directory/develop/v2-oauth2-auth-code-flow.md
[implicit-flow]: ../articles/active-directory/develop/v2-oauth2-implicit-grant-flow.md