---
title: Adicionar autenticação no Apache Cordova com os Aplicativos Móveis| Microsoft Docs
description: Saiba como usar os Aplicativos Móveis no Serviço de Aplicativo do Azure para autenticar usuários de seu aplicativo Apache Cordova usando vários provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft.
services: app-service\mobile
documentationcenter: javascript
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 10dd6dc9-ddf5-423d-8205-00ad74929f0d
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 10/30/2016
ms.author: crdun
ms.openlocfilehash: 23b5967782cf237ed5af2b802aabbbf9c2f781e7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62114203"
---
# <a name="add-authentication-to-your-apache-cordova-app"></a>Adicionar autenticação ao aplicativo Apache Cordova
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="summary"></a>Resumo
Neste tutorial, você adiciona a autenticação ao projeto de início rápido todolist no Apache Cordova usando um provedor de identidade com suporte. Este tutorial se baseia no tutorial [Introdução aos Aplicativos Móveis] , que você deve concluir primeiro.

## <a name="register"></a>Registrar seu aplicativo para autenticação e configurar o Serviço de Aplicativo
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

[Assista a um vídeo que mostra etapas semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-8-Azure-authentication)

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Agora, é possível verificar se o acesso anônimo para o back-end foi desabilitado. No Visual Studio:

* Abra o projeto criado quando você concluiu o tutorial [Introdução aos Aplicativos Móveis].
* Executar o aplicativo no **Emulador do Google Android**.
* Verifique se uma Falha Inesperada de Conexão é mostrada após o aplicativo ser iniciado.

Em seguida, atualize o aplicativo para autenticar os usuários antes de solicitar recursos do back-end do Aplicativo Móvel.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
1. Abra o projeto no **Visual Studio** e abra o arquivo `www/index.html` para edição.
2. Localize a marca meta `Content-Security-Policy` na seção de cabeçalho.  Adicione o host OAuth à lista de fontes permitidas.

   | Provedor | Nome do provedor do SDK | Host OAuth |
   |:--- |:--- |:--- |
   | Azure Active Directory | aad | https://login.microsoftonline.com |
   | Facebook | Facebook | https://www.facebook.com |
   | Google | Google | https://accounts.google.com |
   | Microsoft | microsoftaccount | https://login.live.com |
   | Twitter | Twitter | https://api.twitter.com |

    Veja a seguir um exemplo de Política de segurança de conteúdo (implementada para o Active Directory do Azure):

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
            data: gap: https://login.microsoftonline.com https://yourapp.azurewebsites.net; style-src 'self'">

    Substitua `https://login.microsoftonline.com` pelo host OAuth da tabela anterior.  Para obter mais informações sobre a marcação meta content-security-policy, consulte a [Documentação de Content-Security-Policy].

    Alguns provedores de autenticação não exigem mudanças no Content-Security-Policy quando usado em dispositivos móveis apropriados.  Por exemplo, nenhuma mudança na Política de segurança de conteúdo será necessária ao usar a autenticação do Google em um dispositivo Android.

3. Abra o arquivo `www/js/index.js` para edição, localize o método `onDeviceReady()` e adicione o seguinte sob o código de criação do cliente:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Esse código substitui o código existente que cria a referência de tabela e atualiza a interface do usuário.

    O método logon() inicia a autenticação com o provedor. O método login() é uma função assíncrona que retorna uma promessa de JavaScript.  O restante da inicialização é colocado dentro da resposta de promessa para que não seja executado até a conclusão do método login().

4. No código que você acabou de adicionar, substitua `SDK_Provider_Name` pelo nome de seu provedor de logon. Por exemplo, para o Azure Active Directory, use `client.login('aad')`.
5. Execute seu projeto.  Após a conclusão da inicialização do projeto, o aplicativo mostrará a página de logon do OAuth para o provedor de autenticação escolhido.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais [Sobre autenticação] com o Serviço de Aplicativo do Azure.
* Continue o tutorial adicionando [Notificações por push] a seu aplicativo do Apache Cordova.

Saiba como usar os SDKs.

* [SDK do Apache Cordova]
* [SDK do Servidor ASP.NET]
* [SDK do Servidor Node.js]

<!-- URLs. -->
[Introdução aos Aplicativos Móveis]: app-service-mobile-cordova-get-started.md
[documentação de Content-Security-Policy]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Notificações por push]: app-service-mobile-cordova-get-started-push.md
[Sobre autenticação]: app-service-mobile-auth.md
[SDK do Apache Cordova]: app-service-mobile-cordova-how-to-use-client-library.md
[SDK do Servidor ASP.NET]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[SDK do Servidor Node.js]: app-service-mobile-node-backend-how-to-use-server-sdk.md
