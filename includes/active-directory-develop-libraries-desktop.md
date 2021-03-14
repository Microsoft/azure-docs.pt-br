---
ms.openlocfilehash: 60023f9b97a2ce68cf5689c800080454768ba40d
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/11/2021
ms.locfileid: "103007951"
---
| Idioma/estrutura | Projeto em<br/>GitHub                                                                                     | Pacote                                                                               | Introdução<br/>iniciado                        | Conectar usuários                                         | Acessar APIs Web                                                 | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|----------------------|-----------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:------------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Electron             | [Node.jsMSAL ](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) | [@azure/msal-node](https://www.npmjs.com/package/@azure/msal-node)                    | —                                          | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | Versão prévia pública                                               |
| Java                 | [MSAL4J](https://github.com/AzureAD/microsoft-authentication-library-for-java)                            | [msal4j](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j)               | —                                          | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| macOS (Swift/obj-C)  | [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc)            | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Tutorial](../articles/active-directory/develop/tutorial-v2-ios.md)             | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| UWP                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Tutorial](../articles/active-directory/develop/tutorial-v2-windows-uwp.md)     | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| WPF                  | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)                        | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | [Tutorial](../articles/active-directory/develop/tutorial-v2-windows-desktop.md) | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
<!--
| Java | Scribe | [Scribe Java](https://mvnrepository.com/artifact/org.scribe/scribe) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
| React Native | [React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png


<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/