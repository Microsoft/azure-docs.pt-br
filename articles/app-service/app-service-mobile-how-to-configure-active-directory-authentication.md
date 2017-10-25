---
title: "Como configurar a autenticação do Active Directory do Azure para seu aplicativo de Serviços de Aplicativos"
description: "Saiba como configurar a autenticação do Active Directory do Azure para seu aplicativo de Serviços de Aplicativos."
author: mattchenderson
services: app-service
documentationcenter: 
manager: syntaxc4
editor: 
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/01/2016
ms.author: mahender
ms.openlocfilehash: 25f0578a9e273c30ecc98af5b66c6dd43305aa03
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-configure-your-app-service-application-to-use-azure-active-directory-login"></a>Como configurar seu aplicativo de Serviço de Aplicativo para usar o logon do Active Directory do Azure
[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

Este tópico mostra como configurar os Serviços de Aplicativos do Azure para usar o Azure Active Directory como um provedor de autenticação.

## <a name="express"> </a>Configurar o Active Directory do Azure usando configurações expressas
1. No [portal do Azure], navegue até o seu aplicativo. Clique em **Configurações** e depois em **Autenticação/Autorização**.
2. Se o recurso Autenticação / Autorização não estiver habilitado, mude a opção para **Ativado**.
3. Clique em **Azure Active Directory** e clique em **Expresso** em **Modo de Gerenciamento**.
4. Clique em **OK** para registrar seu aplicativo no Active Directory do Azure. Isso criará um novo registro. Caso queira escolher um registro existente, clique em **Selecionar um aplicativo existente** e pesquise pelo nome de um registro criado anteriormente no seu locatário.
   Clique no registro para selecioná-lo e clique em **OK**. Em seguida, clique em **OK** na folha de configurações do Active Directory do Azure.
   Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo.
5. (Opcional) Para restringir o acesso ao seu site somente para usuários autenticados pelo Azure Active Directory, defina a **Ação a ser executada quando a solicitação não for autenticada** como **Logon com o Azure Active Directory**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Active Directory do Azure para autenticação.
6. Clique em **Salvar**.

Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo.

## <a name="advanced"> </a>(Método alternativo) Configurar manualmente o Active Directory do Azure com configurações avançadas
Você também pode optar por fornecer as configurações manualmente. Essa é a solução preferencial se o locatário do AAD que você deseja usar for diferente do locatário usado para entrar no Azure. Para concluir a configuração, você deve primeiro criar um registro no Active Directory do Azure e fornecer alguns detalhes de registro ao Serviço de Aplicativo.

### <a name="register"> </a>Registrar seu aplicativo com o Active Directory do Azure
1. Faça logon no [portal do Azure]e navegue até o seu aplicativo. Copie a **URL** do aplicativo. Você a utilizará para configurar seu aplicativo do Active Directory do Azure.
2. Navegue até **Active Directory** e, em seguida, selecione os **Registros do aplicativo**, então clique em **Novo registro de aplicativo** na parte superior para iniciar um novo registro de aplicativo. 
3. No diálogo Criar registro de aplicativo, insira um **Nome** para seu aplicativo, selecione o tipo da **API do aplicativo Web** e cole a URL do aplicativo (da Etapa 1) na caixa **URL de logon**. Em seguida, clique em **Criar**.
4. Em alguns segundos, você deverá ver o novo registro de aplicativo que você acabou de criar aparecer.
5. Depois que o aplicativo tiver sido adicionado, clique no nome do registro do aplicativo, clique em **Configurações** na parte superior e depois em **Propriedades** 
6. Cole a URL do Aplicativo (da etapa 1) na caixa **URI da ID do aplicativo**, cole a URL do Aplicativo (da etapa 1) também na **URL da Home Page** e clique em **Salvar**
7. Agora, clique nas **URLs de Resposta**, edite a **URL de Resposta**, cole a URL do Aplicativo (da etapa 1), modifique o protocolo para certificar-se de que você tem o protocolo **https://** (não http://) e, acrescentado ao final da URL, */.auth/login/aad/callback*. (Por exemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`.) Clique em **Salvar**.   
8.  Neste ponto, copie a **ID do Aplicativo** para o aplicativo. Mantenha isso para uso posterior. Você precisará dela para configurar seu aplicativo Web.
9. Feche a folha de detalhes do Registro do Aplicativo. Você deve retornar para o resumo de Registro de Aplicativo do Azure Active Directory, clique no botão **Pontos de Extremidade** na parte superior, em seguida, copie a URL do **Documento de Metadados de Federação**. 
10. Abra uma nova janela do navegador e navegue até a URL, colando e navegando até a página XML. No topo do documento existirá um elemento **EntityDescriptor** e deverá haver um atributo **entityID** do formato `https://sts.windows.net/` seguido por um GUID específico para seu locatário (chamado de "ID do locatário"). Copie esse valor - ele servirá como sua **URL do Emissor**. Você irá configurar seu aplicativo para usá-la posteriormente.

### <a name="secrets"> </a>Adicionar informações do Active Directory do Azure ao seu aplicativo
1. De volta ao [portal do Azure], navegue até o seu aplicativo. Clique em **Autenticação/Autorização**. Se o recurso Autenticação/Autorização não estiver habilitado, mude a opção para **Ativado**. Clique em **Azure Active Directory**, em Provedores de Autenticação, para configurar seu aplicativo. (Opcional) Por padrão, o Serviço de Aplicativo fornece autenticação, mas não restringe o acesso autorizado ao conteúdo do site e às APIs. Você deve autorizar os usuários no código do aplicativo. Selecione a **Ação a tomar quando a solicitação não está autenticada**, defina isso como **Efetuar logon com o Azure Active Directory**. Isso exige que todas as solicitações sejam autenticadas e todas as solicitações não autenticadas sejam redirecionadas ao Active Directory do Azure para autenticação.
2. Na configuração da Autenticação do Active Directory, clique em **Avançado** em **Modo de Gerenciamento**. Cole a ID do aplicativo na caixa ID do Cliente (da etapa 8) e cole a entityId (da etapa 10) no valor de URL do Emissor. Em seguida, clique em **OK**.
3. Na folha de configuração da Autenticação do Active Directory, clique em **Salvar**.

Agora você está pronto para usar um Active Directory do Azure para autenticação em seu aplicativo.

## <a name="optional-configure-a-native-client-application"></a>(Opcional) Configurar um aplicativo de cliente nativo
O Active Directory do Azure também permite que você registre clientes nativos, o que oferece maior controle sobre o mapeamento de permissões. Isso é necessário se você desejar executar logons usando uma biblioteca, como a **Biblioteca de Autenticação do Active Directory**.

1. Navegue até **Active Directory** no [portal clássico do Azure].
2. Selecione o diretório e, em seguida, selecione a guia **Aplicativos** na parte superior. Clique em **ADICIONAR** na parte inferior para criar um novo registro de aplicativo.
3. Clique em **Adicionar um aplicativo que a minha organização está desenvolvendo**.
4. No Assistente para Adicionar aplicativo, insira um **Nome** para o seu aplicativo e clique no tipo **Aplicativo Cliente Nativo**. Em seguida, clique em continuar.
5. Na caixa **URI de redirecionamento**, digite o ponto de extremidade do site */.auth/login/done*, usando o esquema HTTPS. Esse valor deve ser similar a *https://contoso.azurewebsites.net/.auth/login/done*. Caso queira criar um aplicativo do Windows, use o [SID de pacote](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como o URI.
6. Depois que o aplicativo nativo tiver sido adicionado, clique na guia **Configurar** . Encontre a **ID do cliente** e anote esse valor.
7. Role a página para baixo até a seção de **Permissões para outros aplicativos** e clique em **Adicionar aplicativo**.
8. Procure o aplicativo Web que você registrou anteriormente e clique no ícone de adição. Em seguida, clique em verificar para fechar a caixa de diálogo. Se o aplicativo Web não puder ser encontrado, navegue até seu registro e adicione uma nova URL de resposta (por exemplo, a versão HTTP da sua URL atual), clique em Salvar e repita essas etapas - o aplicativo deverá aparecer na lista.
9. Na nova entrada que você acabou de adicionar, abra a lista suspensa **Permissões Delegadas** e selecione **Acessar (nome do aplicativo)**. Em seguida, clique em **Salvar**.

Você configurou um aplicativo de cliente nativo que pode acessar o aplicativo de Serviço de Aplicativo.

## <a name="related-content"> </a>Conteúdo relacionado
[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth-config.png



<!-- URLs. -->

[portal do Azure]: https://portal.azure.com/
[portal clássico do Azure]: https://manage.windowsazure.com/
[alternative method]:#advanced
