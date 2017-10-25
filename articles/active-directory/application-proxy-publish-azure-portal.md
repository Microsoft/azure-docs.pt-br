---
title: Publicar aplicativos com o Proxy de Aplicativo do Azure AD | Microsoft Docs
description: Publique aplicativos locais na nuvem com o Proxy de Aplicativo do Azure AD no Portal do Azure.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d94ac3f4-cd33-4c51-9d19-544a528637d4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: e00a939f2b20ab8e0a2ddf0ff91e59db440213ac
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="publish-applications-using-azure-ad-application-proxy"></a>Publicar aplicativos usando o Proxy de Aplicativo do AD do Azure

> [!div class="op_single_selector"]
> * [Portal do Azure](application-proxy-publish-azure-portal.md)
> * [Portal clássico do Azure](active-directory-application-proxy-publish.md)

O Proxy de Aplicativo do Active Directory (AD) do Azure o ajuda a dar suporte a funcionários remotos publicando aplicativos locais para serem acessados via Internet. É possível publicar esses aplicativos por meio do portal do Azure para fornecer acesso remoto seguro de fora de sua rede.

Este artigo explica as etapas para publicar um aplicativo local com o Proxy de Aplicativo. Depois de concluir este artigo, os usuários poderão acessar remotamente o seu aplicativo. E você estará pronto para configurar recursos adicionais para o aplicativo como logon único, informações personalizadas e requisitos de segurança.

Se você for iniciante com o Proxy de Aplicativo, aprenda mais sobre este recurso com o artigo [Como fornecer acesso remoto seguro a aplicativos locais](active-directory-application-proxy-get-started.md).


## <a name="publish-an-on-premises-app-for-remote-access"></a>Publicar um aplicativo local para acesso remoto

Siga estas etapas para publicar seus aplicativos com Proxy de Aplicativo. Se você já não tiver baixado e configurado um conector para sua organização, vá para [Introdução ao Proxy de Aplicativo e instale o conector](active-directory-application-proxy-enable.md) primeiro e, em seguida, publique seu aplicativo.

> [!TIP]
> Se você estiver testando pela primeira vez o Proxy de Aplicativo, escolha um aplicativo que esteja configurado para autenticação baseada em senha. O Proxy de Aplicativo oferece suporte a outros tipos de autenticação, mas os aplicativos baseados em senha são mais fáceis e rápidos de executar. 

1. Entre como administrador no [Portal do Azure](https://portal.azure.com/).
2. Selecione **Azure Active Directory** > **Aplicativos empresariais** > **Novo aplicativo**.

  ![Adicionar um aplicativo empresarial](./media/application-proxy-publish-azure-portal/add-app.png)

3. Selecione **Todos** e, em seguida, selecione **Aplicativo local**.  

  ![Adicionar seu próprio aplicativo](./media/application-proxy-publish-azure-portal/add-your-own.png)

4. Forneça as seguintes informações sobre o aplicativo:

   - **Nome**: o nome do aplicativo que será exibido no painel de acesso e no portal do Azure. 

   - **URL interna**: é a URL para acessar o aplicativo dentro da sua rede privada. Você pode fornecer um caminho específico no servidor back-end para publicar, enquanto o restante do servidor é não publicado. Assim, você pode publicar sites diferentes no mesmo servidor como diferentes aplicativos, e dar a cada um deles seu próprio nome e suas regras de acesso.

     > [!TIP]
     > Se você publicar um caminho, verifique se ele inclui todas as imagens, scripts e folhas de estilo necessários para seu aplicativo. Por exemplo, se seu aplicativo está em https://seuaplicativo/app e usa imagens localizadas em https://seuaplicativo/media, você deve publicar https://seuaplicativo/ como o caminho. Essa URL interna não precisa ser a página de aterrissagem que os usuários veem. Para obter mais informações, consulte [Definir uma página inicial personalizada para aplicativos publicados](application-proxy-office365-app-launcher.md).

   - **URL externa**: o endereço que seus usuários usam para acessar o aplicativo de fora da sua rede. Se você não quiser usar o domínio padrão de Proxy de Aplicativo, leia sobre [domínios personalizados no Proxy de Aplicativo do Azure AD](active-directory-application-proxy-custom-domains.md).
   - **Pré-autenticação**: como o Proxy de Aplicativo verifica os usuários antes de lhes dar acesso ao aplicativo. 

     - Azure Active Directory: o Proxy de Aplicativo redireciona os usuários para entrar com o Azure AD, que autentica as permissões para o diretório e o aplicativo. É recomendável manter essa opção como padrão, para que você possa tirar proveito dos recursos de segurança do Azure AD como acesso condicional e Autenticação Multifator.
     - Passagem: os usuários não precisam ser autenticados no Azure Active Directory para acessar o aplicativo. Você ainda pode configurar os requisitos de autenticação no back-end.
   - **Grupo de Conectores**: conectores processam o acesso remoto ao seu aplicativo e o ajudam a organizar conectores e aplicativos por região, rede ou finalidade. Se você ainda não tiver grupos de conectores criados, seu aplicativo é atribuído a **Padrão**.

   ![Configurar seu aplicativo](./media/application-proxy-publish-azure-portal/configure-app.png)
5. Se necessário, defina configurações adicionais. Para a maioria dos aplicativos, você deve manter essas configurações em seus estados padrão. 
   - **Tempo limite do aplicativo de back-end**: Defina esse valor como **Longo** somente se seu aplicativo estiver lento para se autenticar e se conectar. 
   - **Converter URLs nos cabeçalhos**: Mantenha esse valor como **Sim** a menos que seu aplicativo exija o cabeçalho de host original na solicitação de autenticação.
   - **Converter URLs no corpo do aplicativo**: Mantenha esse valor como **Não** a menos que você tenha inserido no código HTML links para outros aplicativos locais e não use domínios personalizados. Para saber mais, consulte [Conversão de link com o Proxy de Aplicativo](application-proxy-link-translation.md).
   
   ![Configurar seu aplicativo](./media/application-proxy-publish-azure-portal/additional-settings.png)

6. Selecione **Adicionar**.


## <a name="add-a-test-user"></a>Adicionar um usuário de teste 

Para testar se o aplicativo foi publicado corretamente, adicione uma conta de usuário de teste. Verifique se essa conta já tem permissões para acessar o aplicativo dentro da rede corporativa.

1. De volta à folha Início Rápido, selecione **Atribuir um usuário para teste**.

  ![Atribuir um usuário para teste](./media/application-proxy-publish-azure-portal/assign-user.png)

2. Na folha Usuários e grupos, selecione **Adicionar**.

  ![Adicionar um usuário ou grupo](./media/application-proxy-publish-azure-portal/add-user.png)

3. Na folha de Adicionar Atribuição, selecione **Usuários e grupos**, em seguida escolha a conta que você deseja adicionar. 
4. Selecione **Atribuir**.

## <a name="test-your-published-app"></a>Testar seu aplicativo publicado

No seu navegador, navegue até a URL externa configurada durante a etapa de publicação. Você deve ver a tela inicial e ser capaz de entrar com a conta de teste que configurou.

![Testar seu aplicativo publicado](./media/application-proxy-publish-azure-portal/test-app.png)


## <a name="next-steps"></a>Próximas etapas
- [Baixe conectores](active-directory-application-proxy-enable.md) e [crie grupos de conectores](active-directory-application-proxy-connectors-azure-portal.md) para publicar aplicativos em redes e locais separados.

- [Configurar o logon único](application-proxy-sso-azure-portal.md) para seu aplicativo recém-publicado
