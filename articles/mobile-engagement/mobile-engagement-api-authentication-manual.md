---
title: "Autenticar com APIs REST do Mobile Engagement - configuração manual"
description: "Descreve como configurar manualmente a autenticação para APIs REST do Mobile Engagement"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9d6132e1a01be489b8e8e28a0219cf8a0b50b318
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis---manual-setup"></a>Autenticar com APIs REST do Mobile Engagement - configuração manual
Esta é uma documentação de apêndice de [Autenticar com APIs REST do Mobile Engagement](mobile-engagement-api-authentication.md). Leia esse artigo primeiro para obter o contexto. Ele descreve uma maneira alternativa de fazer a configuração única da autenticação para APIs REST do Mobile Engagement usando o portal do Azure. 

> [!NOTE]
> As instruções a seguir se baseiam nesse [Guia do Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md) e são personalizadas para o que é necessário para a autenticação de APIs do Mobile Engagement. Portanto, consulte-o para entender as etapas abaixo em detalhes. 
> 
> 

1. Faça logon na sua conta do Azure por meio do [portal clássico](https://manage.windowsazure.com/).
2. Selecione **Active Directory** no painel à esquerda.
   
     ![selecionar Active Directory][1]
3. Escolha o **Active Directory Padrão** em seu portal do Azure. 
   
     ![escolher o diretório][2]
   
   > [!IMPORTANT]
   > Essa abordagem funciona somente quando você está trabalhando no Active Directory padrão de sua conta, e não funcionará se você estiver fazendo isso em um Active Directory que você criou em sua conta. 
   > 
   > 
4. Para exibir os aplicativos em seu diretório, clique em **Aplicativos**.
   
     ![exibir aplicativos][3]
5. Clique em **ADICIONAR**. 
   
     ![adicionar aplicativo][4]
6. Clique em **Adicionar um aplicativo que minha organização está desenvolvendo**
   
     ![novo aplicativo][5]
7. Preencha o nome do aplicativo e selecione o tipo de aplicativo como **APLICATIVO WEB E/OU API WEB** e clique no botão Avançar.
   
     ![nomear aplicativo][6]
8. É possível fornecer URLs fictícias para **URL DE LOGON** e **URI DA ID DO APLICATIVO**. Elas não são usadas em nosso cenário e as próprias URLs não são validadas.  
   
     ![propriedades do aplicativo][7]
9. Ao final deste artigo, você terá um aplicativo AAD com o nome fornecido anteriormente, como o seguinte. Este é o seu **NOME\_APLICATIVO\_AD**. Anote-o.  
   
     ![nome do aplicativo][8]
10. Clique no nome do aplicativo e clique em **Configurar**.
    
      ![configurar aplicativo][9]
11. Anote a ID DO CLIENTE que será usada como **ID\_CLIENTE** para suas chamadas à API. 
    
     ![configurar aplicativo][10]
12. Role para baixo até a seção **Chaves** e adicione uma chave com duração de dois anos (validade) de preferência e clique em **Salvar**. 
    
     ![configurar aplicativo][11]
13. Copie imediatamente o valor mostrado para a chave, pois ele será exibido apenas agora e não será armazenado, portanto, não será exibido novamente. Se você perdê-lo, terá que gerar uma nova chave. Esse será o **SEGREDO_DO_CLIENTE** para suas chamadas à API. 
    
     ![configurar aplicativo][12]
    
    > [!IMPORTANT]
    > Essa chave expirará ao final da duração especificada, portanto lembre-se de renová-la quando chegar a hora, caso contrário a autenticação da API não funcionará mais. Você também pode excluir e recriar essa chave se achar que ela foi comprometida.
    > 
    > 
14. Clique no botão **EXIBIR PONTOS DE EXTREMIDADE**, o que abrirá a caixa de diálogo **Pontos de Extremidade do Aplicativo**. 
    
    ![][13]
15. Na caixa de diálogo Pontos de Extremidade do Aplicativo, copie o **PONTO DE EXTREMIDADE DO TOKEN OAUTH 2.0**. 
    
    ![][14]
16. Esse ponto de extremidade terá o seguinte formato, em que o GUID na URL é seu **TENANT_ID**, então anote-o: 
    
        https://login.microsoftonline.com/<GUID>/oauth2/token
17. Agora, configuraremos as permissões nesse aplicativo. Para isso, você precisará abrir o [portal do Azure](https://portal.azure.com). 
18. Clique em **Grupos de Recursos** e localize o grupo de recursos **Mobile Engagement**.  
    
    ![][15]
19. Clique no grupo de recursos **Mobile Engagement** e navegue até a folha **Configurações**. 
    
    ![][16]
20. Clique em **Usuários** na folha Configurações e clique em **Adicionar** para adicionar um usuário. 
    
    ![][17]
21. Clique em **Selecionar uma função**
    
    ![][18]
22. Clique em **Proprietário**
    
    ![][19]
23. Procure o nome de seu aplicativo **NOME\_APLICATIVO\_AD** na caixa Pesquisar. Você não verá ele por padrão aqui. Depois de encontrá-lo, selecione-o e clique em **Selecionar** na parte inferior da folha. 
    
    ![][20]
24. Na folha **Adicionar Acesso**, ele será exibido como **1 usuário, 0 grupos**. Clique em **OK** nessa folha para confirmar a alteração. 
    
    ![][21]

Agora você concluiu a configuração necessária do AAD e está pronto para chamar as APIs. 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client_secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



