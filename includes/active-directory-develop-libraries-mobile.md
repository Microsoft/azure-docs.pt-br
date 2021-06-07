---
ms.openlocfilehash: 15e809e1fa361f758a3df7e5b56d4fb80400f656
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007878"
---
| Plataforma          | Projeto em<br/>GitHub                                                                          | Pacote                                                                               | Introdução<br/>iniciado                    | Conectar usuários                                         | Acessar APIs Web                                                 | Disponibilidade geral (GA) *ou*<br/>Visualização pública<sup>1</sup> |
|-------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------|:--------------------------------------:|:-----------------------------------------------------:|:---------------------------------------------------------------:|:------------------------------------------------------------:|
| Android (Java)    | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | [Início rápido](../articles/active-directory/develop/quickstart-v2-android.md) | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| Android (Kotlin)  | [MSAL Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)        | [MSAL](https://mvnrepository.com/artifact/com.microsoft.identity.client/msal)         | —                                      | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| iOS (Swift/obj-C) | [MSAL para iOS e macOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [MSAL](https://cocoapods.org/pods/MSAL)                                               | [Tutorial](../articles/active-directory/develop/tutorial-v2-ios.md)         | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
| Xamarin (.NET)    | [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)             | [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) | —                                      | ![A biblioteca pode solicitar tokens de ID para entrada do usuário.][y] | ![A biblioteca pode solicitar tokens de acesso para APIs Web protegidas.][y] | GA                                                           |
<!--
| React Native |[React Native App Auth](https://github.com/FormidableLabs/react-native-app-auth/blob/main/docs/config-examples/azure-active-directory.md) | [react-native-app-auth](https://www.npmjs.com/package/react-native-app-auth) | ![X indicating no.][n] | ![Green check mark.][y] | ![Green check mark.][y] | -- |
-->

<sup>1</sup> os [termos de uso complementares para visualizações de Microsoft Azure][preview-tos] se aplicam a bibliotecas em *Visualização pública*.

<!--Image references-->

[y]: ../articles/active-directory/develop/media/common/yes.png
[n]: ../articles/active-directory/develop/media/common/no.png

<!--Reference-style links -->

[preview-tos]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/